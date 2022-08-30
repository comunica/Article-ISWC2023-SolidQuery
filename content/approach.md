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

So far, no reachability approaches exist
that can handle the traversal of a Solid data vaults as described in [](#solid).
Hence, we introduce an approach in this section.

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

Formally, reachability approaches can be captured as [*reachability criteria*](cite:cites linktraversalfoundations).
A reachability criterion $$c$$ is defined as a total computable function $$c : \mathcal{T} \times \mathcal{I} \times \mathcal{B} \rightarrow \{ \text{true}, \text{false} \}$$,
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

### Discovery of type index

Write me
{:.todo}

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

- Cite linktraversalcaching
- We extended cMatch to also work with property paths (define formally?), and extended zero-knowledge planner.
- LDP-based actors
- Type index actor
- Make use of subweb specification formalization to capture LDP and type idx, and make sure to also include formal basis of link traversal
{:.todo}
