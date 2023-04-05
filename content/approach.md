## Approach
{:#approach}

In this section, we introduce techniques for handling Solid's structural properties discussed in [](#solid).
Our goal is not to introduce additional components or structural properties to the Solid ecosystem,
but instead, we use what the Solid ecosystem provides today,
and investigate how to query over this as performant as possible.
We start by discussing the preliminaries of the formalities we will introduce.
Next, we discuss our pipeline-based link queue approach.
Then, we discuss two novel discovery approaches for LTQP.
Finally, we discuss their implementations.

### Formal preliminaries

Hereafter, we summarize the semantics of [SPARQL querying](cite:cites sparqlsemantics)
and [LTQP](cite:cites linktraversalfoundations, guidedlinktraversal).

The infinite set of *RDF triples* is formalized as $$ \mathcal{T} = (\mathcal{I} \cup \mathcal{B}) \times \mathcal{I} \times (\mathcal{I} \cup \mathcal{B} \cup \mathcal{L}) $$,
where $$\mathcal{I}$$, $$\mathcal{B}$$, and $$\mathcal{L}$$ respectively denote the disjoint, infinite sets of IRIs, blank nodes, and literals.
Furthermore, $$\mathcal{V}$$ is the infinite set of all variables that is disjoint from $$\mathcal{I}$$, $$\mathcal{B}$$, and $$\mathcal{L}$$.
A tuple $$tp \in (\mathcal{V} \cup \mathcal{I}) \times (\mathcal{V} \cup \mathcal{I}) \times (\mathcal{V} \cup \mathcal{I} \cup \mathcal{L})$$ is called a *triple pattern*.
A finite set of these triple pattern is called a *basic graph pattern* (BGP).
More complex SPARQL query operators exist,
but since BGPs form the foundational building block of a SPARQL query,
we only consider BGPs for the remainder of this work.
The query results of a SPARQL query $$P$$ over a set of RDF triples $$G$$ are called *solution mappings*,
which are denoted by $$[[P]]_G$$, consisting of partial mappings $$\mu : \mathcal{V} \rightarrow (\mathcal{I} \cup \mathcal{B}\cup \mathcal{L})$$.
An RDF triple $$t$$ *matches* a triple pattern $$tp$$ if $$\exists \mu : t = \mu[tp]$$, where $$\mu[tp]$$ is the triple pattern that is obtained by replacing all variables from $$\mu$$ in $$tp$$.

Formally, the reachability approaches that were discussed in [](#related-work)
define which links should be followed during link traversal,
and are usually captured as [*reachability criteria*](cite:cites linktraversalfoundations).
However, since this formalization lacks expressive power to capture the structural properties we require,
we formalize new reachability criteria in this work as *source selectors*
within the [subweb specification formalization](cite:cites guidedlinktraversal).
Within this formalization, a source selector $$\sigma$$ is defined as $$\sigma : \mathcal{W} \rightarrow 2^{\mathcal{I}}$$,
where $$\mathcal{W}$$ is a Web of Linked Data.
The Web of Linked Data $$\mathcal{W}$$ is a tuple $$\langle D, data, adoc \rangle$$,
where $$D$$ is a set of documents, $$data$$ a function from $$D$$ to $$2^\mathcal{T}$$
such that $$data(d)$$ is finite for each $$d \in D$$,
and $$adoc$$ a partial dereferencing function from $$\mathcal{U}$$ to $$D$$.

Based on these definitions, we define the set of all Solid data vaults as $$\Upsilon$$,
where each vault $$\upsilon \in \Upsilon$$ is defined as a set of triples, where $$triples(\upsilon) \subseteq \mathcal{T}$$.
For a vault $$\upsilon_{LDP}$$ exposed through the LDP interface,
the triples contained in such a vault are captured in different documents $$D_{\upsilon} \subseteq D$$.
Hereby, $$triples(\upsilon_{LDP}) = \{ t \mid \forall d \in D_{\upsilon} \land t \in data(d) \}$$.

### Pipeline-based link queue

To execute a query,
our approach builds upon the [zero-knowledge query planning technique](cite:cites zeroknowldgequeryplanning)
to construct a logical query plan ahead of query execution.
This resulting plan produces a tree of logical query operators representing the query execution order.
To execute this plan, the logical operators are executed by specific physical operators.
Our physical query execution builds upon the [iterator-based pipeline approach](cite:cites linktraversalsparql),
which is [the most popular among LTQP implementations](cite:cites squin, sihjoin, diamondrete).
We consider the execution plan as a [pipeline](cite:cites pipelining) of iterator-based physical operators,
where intermediary results flow through chained operators with pull-based results.

Instead of [letting operators trigger the dereferencing of URIs](cite:cites linktraversalsparql),
we follow a [link queue-based approach](cite:cites linktraversaloptimization).
The architecture of this approach is visualized in [](#figure-link-queue).
Concretely, we consider a continuously growing *triple source* as the basis of the pipeline tree,
which is able to produce a (possibly infinite) stream of RDF triples.
This triple source is fed triples originating from a loop consisting of the *link queue*, *dereferencer*, and a set of *link extractors*.
The link queue accepts links from a set of link extraction components,
which are invoked for every document that has been dereferenced by the dereferencer.
The dereferenced documents containing triples are also sent to the continuously growing triple source.
This link queue is initialized with a set of seed URIs,
and the dereferencer continuously dereferences the URIs in the queue until it is empty.
Since the link extractors are invoked after every dereference operation,
this queue may virtually become infinitely long.

<figure id="figure-link-queue">
<img src="img/link-queue.svg" alt="Link queue" class="img-narrow">
<figcaption markdown="block">
Link queue, dereferencer and link extractors feeding triples into a triple source,
producing a stream of triples to tuple-producing operators
in a pipelined query execution.
</figcaption>
</figure>

This link queue and link extractor approach is generic enough to implement
[other LTQP methods](cite:cites linktraversalsparql, linktraversalfoundations, linktraversalpropertypaths, guidedlinktraversal, linktraversaloptimization)
for determining and prioritizing links that need to be followed.
For example, one extractor may consider `rdfs:seeAlso` links,
while another extractor may consider URIs of a triple that matches with a triple pattern from the query.
Optionally, operators in the query pipeline may push links into the link queue,
which enables [context-based reachability semantics](cite:cites linktraversalpropertypaths).
Link extractors only consider URIs as links,
and ignore matching blank nodes and literals.

The triple source is connected to all [tuple-producing SPARQL operators](cite:cites sparqlsemantics) in the leaves of the query plan,
such as triple patterns and property path operators,
into which a stream of triples is sent.
The source indexes all triples, to ensure that an operator
that is executed later in the execution process does not miss any triples.

### Discovery of data vault

In this section, we introduce a novel discovery approach for traversing over Solid data vaults as discussed in [](#solid).

#### Intuitive description

To achieve link traversal within a vault,
we assume that the WebID document is available as seed URI,
or is discovered through some other reachability approach.
As discussed in [](#solid), the root of a vault can be discovered from a WebID document
by dereferencing the object URI referred to by the `pim:storage` predicate.
Next, all resources within this vault can be discovered by recursively following `ldp:contains` links from the root container.

We only consider triples for the `pim:storage` and `ldp:contains` predicates
that have the current document URI as subject.
If subjects contain fragment identifiers, we only consider them if the current document URI had this fragment identifier as well before it was dereferenced.
For example, if a WebID with fragment identifier `#me` was discovered,
then we only consider triples with the document URI + `#me` as subject.

#### Formal description

We can formalize our discovery approach for the roots of data vaults as a following source selector starting from a given WebID with URI $$i$$
as $$\sigma_{\text{SolidVault}}(W) = \{ o \mid \langle i \text{ pim:storage } o \rangle \in data(adoc(i))\}$$.
Disjunctively coupled with this,
we can formalize a source selector that can recursively traverse an LDP container as
$$\sigma_{\text{LdpContainer}}(W) = \{ o \mid \forall s : \langle s \text{ ldp:contains } o \rangle \in data(adoc(s)\}$$

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

As discussed in [](#solid), the type index enables class-based resource discovery in a vault.
In this section, we introduce a novel method that follows links in the type index,
with an optional filter that only follows links matching with a class in the query.

#### Intuitive description

As before, we consider a WebID document as the starting point.
From this document, we follow the `solid:publicTypeIndex` and `solid:privateTypeIndex` links.
For each discovered type index, we consider all `solid:TypeRegistration` resources,
and follow their `solid:instance` and `solid:instanceContainer` links.

As an optimization, we can also take into account the type information within the registrations of the type index,
to only follow those links for classes that are of interest to the current query.
Concretely, this involves considering the objects referred to by `solid:forClass` on each type registration.
To know whether or not a class is relevant to the current query,
we explicitly check for the occurrence of this class within the query as object within triples using the `rdf:type` predicate.
For subjects in the query without `rdf:type` predicate, the matching class is unknown, which is why we consider all type registrations in this case.

#### Formal description

To discover and traverse type indexes,
we formalize the following source selector from a given WebID with URI $$s$$ when querying a BGP $$B$$:

$$
\sigma_{\text{SolidTypeIndex}}(W) = \{ o \mid \forall t,r,c : \phi(B, c) \\
\begin{array}{ll}
    \land & (\langle s \text{ solid:publicTypeIndex } t \rangle \lor \langle s \text{ solid:privateTypeIndex } t \rangle) \\
          & \in data(adoc(s))\\
    \land & (\langle r \text{ rdf:type solid:TypeRegistration} \rangle \\
          & \land \langle r \text{ solid:forClass } c \rangle) \in data(adoc(t))\\
    \land & (\langle r \text{ solid:instance } o \rangle \lor \langle r \text{ solid:instanceContainer } o \rangle) \\
          & \in data(adoc(t))\}
\end{array}
$$

Since `solid:instanceContainer` links to LDP containers,
$$\sigma_{\text{SolidTypeIndex}}$$ should be disjunctively combined with $$\sigma_{\text{LdpContainer}}$$.

In this formalization, we consider $$\phi(B, c)$$ a filtering predicate function for determining which classes are considered within the type index.
To consider _all_ type registrations within the type index, we can implement $$\phi(B, c)$$ as a predicate always returning `true`.
To only consider type registrations that match with a class mentioned in the query, we introduce the following filtering function:

$$
\phi_{\text{QueryClass}}(B, c) = \left\{ \begin{array}{ll}
        \text{true}  & \text{if } \exists tp \in B : \\
                     & \langle ?v \text{ rdf:type } c \rangle \text{ matches } tp\\
                     & \text{or if } \exists s : \langle s \text{ } ?p \text{ } ?o \rangle \in B \\
                     & \land \{ o \mid \langle s \text{ rdf:type } o \in B \} = \empty \\
        \text{false} & \text{else}.\end{array} \right.
$$

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

We have implemented our system using an existing SPARQL query engine framework (*name and reference omitted for double-blindness*).
Concretely, we have implemented the pipeline-based link queue as a separate module,
and we provide link extractors corresponding to the source selectors introduced in previous sections.
Our implementation has full SPARQL 1.1 support, and consists of pipelined implementations of all monotonic SPARQL operators.
This pipelined implementation is important for iterative tuple processing in a non-blocking manner,
because the link queue and the resulting stream of triples may become infinitely long.

Our implementation focuses on the SPARQL query language,
instead of alternatives such as [LDQL](cite:cites ldql) and [SPARQL-LD](cite:cites sparqlld)
that incorporate link navigation paths into the query.
As discussed in [](#solid), different Solid apps or user preferences may lead to the storage of similar data at different locations within vaults.
Hence, link navigation must *decoupled* from the query to make queries reusable for different Solid users,
as link paths to data may differ across different vaults.
Instead, our implementation uses LDP container traversal and the type index to replace explicit navigation links.

To provide a stable reference implementation that can be used for the experiments in this work and future research,
our implementation focuses on extensibility and reusability.
Our implementation builds upon best practises in LTQP and lessons learned from [other implementations](cite:cites squin) including,
the use of [client-side caching](cite:cites linktraversalcaching),
the different [reachability semantics](cite:cites linktraversalfoundations),
[zero-knowledge query planning](cite:cites zeroknowldgequeryplanning) applied to arbitrary join operations instead of only triple patterns in BGPs,
and [more](cite:cites linktraversalsparql).
Furthermore, our implementation allows users to explicitly pass seed URIs,
but falls back to [query-based seed URIs](cite:cites squin) if no seeds were provided.
This fallback considers all URIs within the query as seed URIs.

Hence, our implementation can query over one or more Solid data vaults.
This also includes the ability to perform authenticated to documents within vaults behind access control.
To ensure that common HTTP errors that may occur during link traversal don't terminate the query execution process,
we enable a default _lenient_ mode, which ignores dereference responses with HTTP status code in ranges 400 and 500.
