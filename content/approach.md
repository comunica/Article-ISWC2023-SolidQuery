## Approach
{:#approach}

In this section, we introduce algorithms for handling the structural properties discussed in [](#solid).
We start by discussing the link traversal approaches we build upon.
Then, we discuss two novel discovery approaches for LTQP.
Finally, we discuss their implementations.

### Pipeline-based link queue

To execute a query,
our approach builds upon the heuristics-based [zero-knowledge query planning technique](cite:cites zeroknowldgequeryplanning)
to construct a logical query plan ahead of query execution.
This resulting plan produces a tree of logical query operators representing in what order the query should be executed.
To execute this plan, the logical operators are executed by specific physical operators.

Our physical query execution builds upon the [iterator-based pipeline approach](cite:cites linktraversalpipeline),
which is [the most popular among LTQP implementations](cite:cites squin, sihjoin, diamondrete).
This involves considering the execution plan as a [pipeline](cite:cites pipelining) of iterator-based physical operators,
through which intermediary results can flow through these chained operators to produce results in a pull-based manner.

Instead of [letting operators trigger the dereferencing of URIs](cite:cites linktraversalpipeline),
we follow a [link queue-based approach](cite:cites linktraversaloptimization).
Concretely, we consider a continuously growing *triple source* as the basis of the pipeline tree,
which is able to produce a (possibly infinite) stream of RDF triples.
This triple source is fed triples originating from a loop consisting of the *link queue*, *dereferencer*, and a set of *link extractors*.
The link queue accepts links from a set of link extraction components,
which are invoked for every document that has been dereferenced by the dereferencer.
The dereferenced documents containing triples are also sent to the continuously growing triple source.
This link queue is initialized with a set of seed URIs,
and the dereferencer then continuously dereferences the URIs in the queue until it is empty.
Since the link extractors are invoked after every dereference operation,
this link queue may virtually become infinitely long.

This link queue and link extractor approach is generic enough to implement
[all the different methods](cite:cites linktraversalpipeline, linktraversalfoundations, linktraversalpropertypaths, guidedlinktraversal, linktraversaloptimization)
for determining and prioritizing links that need to be followed.
For example, one link extractor may extract all objects of each RDF triple matching the `rdfs:seeAlso` predicate,
while another link extractor may extract all components of each triple that matches with a triple pattern within the query.
Optionally, operators within the query pipeline may also push links directly into the link queue,
which may enable implementation of [context-based reachability semantics](cite:cites linktraversalpropertypaths).
All link extractors only consider URIs as links,
and thereby ignore any matches for blank nodes and literals.

This queue is then connected to all [tuple-producing SPARQL operators](cite:cites sparqlsemantics) in the leaves of the query plan,
such as triple patterns and property path operators,
into which a stream of triples is sent.
The queue indexes all triples locally, to ensure that a triple pattern operator
that is executed later in the execution process does not miss any triples.

<figure id="figure-link-queue">
<img src="img/link-queue.svg" alt="Link queue">
<figcaption markdown="block">
Link queue, dereferencer and link extractors feeding triples into a triple source,
which produces a continuous stream of triples to tuple-producing operators
in a pipelined query execution.
</figcaption>
</figure>

### Discovery of data vault

So far, no discovery methods exist
that can handle the traversal of a Solid data vaults as described in [](#solid).
Hence, we introduce an approach in this section.

#### Intuitive description

In order to achieve this traversal within a vault,
we assume that the WebID document is available as seed URI,
or that this WebID document has been discovered through some other reachability approach.
As discussed in [](#solid), the root of a vault can be discovered from a WebID document
by dereferencing the object URI referred to by the `pim:storage` predicate.
Next, all resources within this vault can be discovered by recursively following `ldp:contains` links from the root LDP container.

Note that we only consider triples for the `pim:storage` and `ldp:contains` predicates
that have the current document URI as subject.
If subjects contain fragment identifiers, we only consider them if the current document URI had this fragment identifier as well before it was dereferenced.
For example, if a WebID with fragment identifier `#me` was discovered,
then we only consider triples with the document URI + `#me` as subject.

#### Formal description

Formally, reachability approaches are usually captured as [*reachability criteria*](cite:cites linktraversalfoundations).
However, since this formalization is restricted to considering either all or no URIs within specific data triples,
it is not expressive enough to express the ability to only follow specific URIs within only subject, predicate, or object in data triples.
Therefore, we formalize our reachability criterion as a *source selector*
within the [subweb specification formalization](cite:cites guidedlinktraversal) that _is_ expressive enough to capture this.

Within this formalization, a source selector $$\sigma$$ is defined as $$\sigma : \mathcal{W} \rightarrow 2^{\mathcal{U}}$$,
where $$\mathcal{W}$$ is a Web of Linked Data, and $$\mathcal{U}$$ is the infinite set of all possible URIs.
The Web of Linked Data $$\mathcal{W}$$ is a tuple $$\langle D, data, adoc \rangle$$,
where $$D$$ is a set of documents, $$data$$ a function from $$D$$ to $$2^\mathcal{T}$$
such that $$data(d)$$ is finite for each $$d \in D$$,
$$adoc$$ a partial dereferencing function from $$\mathcal{U}$$ to $$D$$,
and $$\mathcal{T}$$ is the set of all RDF triples.

We can formalize our discovery approach for data vaults as the following source selector starting from a given WebID with URI $$s$$:

$$
\sigma_{\text{SolidVault}}(W) = \{ o \mid \langle s \text{ pim:storage } o \rangle \in data(adoc(s))\}
$$

Disjunctively coupled with this source selector $$\sigma_{\text{SolidVault}}$$,
we can formalize the following source selector that can recursively traverse an LDP container:

$$
\sigma_{\text{LdpContainer}}(W) = \{ o \mid \forall s . \langle s \text{ ldp:contains } o \rangle \in data(adoc(s)\}
$$

<!--A reachability criterion $$c$$ is defined as a total computable function $$c : \mathcal{T} \times \mathcal{I} \times \mathcal{B} \rightarrow \{ \text{true}, \text{false} \}$$,
where $$\mathcal{T}$$ is the infinite set of all possible data triples,
$$\mathcal{I}$$ is the infinite set of all document URIs.
and $$\mathcal{B}$$ is the infinite set of all possible query patterns.

We can formalize our discovery approach for data vaults as the following reachability criterion:

$$
c_{\text{SolidVault}}(t, id, B) = \left\{ \begin{array}{ll}
        \text{true}  & \text{if}\ t\ \text{matches}\ (id\ \text{pim:storage}\ ?\text{var})\\
                     & \lor\ t\ \text{matches}\ (id\ \text{ldp:contains}\ ?\text{var}),\\
        \text{false} & \text{else}.\end{array} \right.
$$
-->

### Discovery of type index

As discussed in [](#solid), the type index provides a way to discover resources in a data vault by RDF classes.
In this section, we introduce a discovery method that follows all links in the type index,
and a filter-based discovery method that builds upon it by only following those type index links that match with a class mentioned in the query.

#### Intuitive description

As before, we consider a WebID document as a starting point.
From this document, we follow the `solid:publicTypeIndex` and `solid:privateTypeIndex` links.
For each discovered type index, we consider all `solid:TypeRegistration` resources,
and follow their `solid:instance` and `solid:instanceContainer` links.

As an optimization, we can also take into the type information within the type registrations within the type index,
to only follow those links for classes that are of interest to the current query.
Concretely, this involves considering the objects referred to by `solid:forClass` on each type registration.
To know whether or not a class is relevant to the current query,
we explicitly check for the occurrence of this class within the query as object within triples using the `rdf:type` predicate.
In future work, more complex approaches for determining the relevance of a class to a query could be devised based on [inferencing](cite:cites rif).

#### Formal description

To discover type indexes and follow links within them,
we formalize the following source selector from a given WebID with URI $$s$$ and a BGP $$B$$:

$$
\sigma_{\text{SolidTypeIndex}}(W) = \{ o \mid \forall i,r,c : \phi(B, c) \\
\begin{array}{ll}
    \land & (\langle s \text{ solid:publicTypeIndex } i \rangle \lor \langle s \text{ solid:privateTypeIndex } i \rangle) \\
          & \in data(adoc(s))\\
    \land & (\langle r \text{ rdf:type solid:TypeRegistration} \rangle \\
          & \land \langle r \text{ solid:forClass } c \rangle) \in data(adoc(i))\\
    \land & (\langle r \text{ solid:instance } o \rangle \lor \langle r \text{ solid:instanceContainer } o \rangle) \\
          & \in data(adoc(i))\}
\end{array}
$$

Since the `solid:instanceContainer` can link to other LDP containers,
$$\sigma_{\text{SolidTypeIndex}}$$ should be disjunctively combined with $$\sigma_{\text{LdpContainer}}$$.

In this formalization, we consider $$\phi(B, c)$$ a filtering function for determining which classes are considered within the type index.
To consider _all_ type registrations within the type index, we can implement $$\phi(B, c)$$ as a function always returning `true`.
To only consider those type registrations that match with a class mentioned in the query, we introduce the filtering function $$\phi_{\text{QueryClass}}$$:

$$
\phi_{\text{QueryClass}}(B, c) = \left\{ \begin{array}{ll}
        \text{true}  & \text{if } \exists tp \in B : \\
                     & \langle v \text{ rdf:type } c \rangle \text{ matches } tp\\
        \text{false} & \text{else}.\end{array} \right.
$$

TODO: formalize BGPs and triples and RDF stuff
{:.todo}

<!--
$$
c_{\text{DiscoverSolidTypeIndex}}(t, id, B) = \left\{ \begin{array}{ll}
        \text{true}  & \text{if}\ t\ \text{matches}\ (id\ \text{solid:publicTypeIndex}\ ?\text{var})\\
                     & \lor\ t\ \text{matches}\ (id\ \text{solid:privateTypeIndex}\ ?\text{var})\\
                     & \lor\ t\ \text{matches}\ (id\ \text{solid:privateTypeIndex}\ ?\text{var}),\\
        \text{false} & \text{else}.\end{array} \right.
$$

$$
c_{\text{FollowAllSolidTypeIndexLinks}}(t, id, B) = \left\{ \begin{array}{ll}
        \text{true}  & \text{if}\ t\ \text{matches}\ (id\ \text{solid:publicTypeIndex}\ ?\text{var})\\
                     & \lor\ t\ \text{matches}\ (id\ \text{solid:privateTypeIndex}\ ?\text{var})\\
                     & \lor\ t\ \text{matches}\ (id\ \text{solid:privateTypeIndex}\ ?\text{var}),\\
        \text{false} & \text{else}.\end{array} \right.
$$
-->

### Implementation

- Focus on extensibility and modularity
- Full SPARQL 1.1 support (pipelined impl of operators: important because "Since the link queue may virtually become infinitely long, the stream of triples sent to tuple-producing operators may also be infinitely long.")
{:.todo}

Since the [zero-knowledge query planning technique](cite:cites zeroknowldgequeryplanning)
only defines plan selection rules for joining triple patterns in a BGP,
we extended these rules in our implementation to be able to join arbitrary operators.

We have implemented the different [reachability semantics](cite:cites linktraversalfoundations) (cNone, cMatch, cAll)
as discused in [](#related-work) as link extraction components.

Our implementation supports users to explicitly pass seed URIs,
but falls back to [query-based seed URIs](cite:cites squin) if no manual seed URIs have been passed.
This fallback involves finding all URIs within the query, and adding them as seed URIs to the link queue.

- taking into account auth and HTTP 403's
- Cite linktraversalcaching
- We extended cMatch to also work with property paths (define formally?), and extended zero-knowledge planner.
- LDP-based actors
- Type index actor
- Make use of subweb specification formalization to capture LDP and type idx, and make sure to also include formal basis of link traversal
{:.todo}
