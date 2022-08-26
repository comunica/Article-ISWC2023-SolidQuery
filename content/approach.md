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
To execute this plan, the logical operators are execute by specific physical operators.

The physical query execution builds upon the [iterator-based pipeline approach](cite:cites linktraversalpipeline),
which is [the most popular among LTQP implementations](cite:cites squin, sihjoin, diamondrete).
This involves considering the execution plan as a [pipeline](cite:cites pipelining) of iterator-based physical operators,
through which intermediary results can flow through these chained operators to produce results in a pull-based manner.

Instead of [letting operators trigger the dereferencing of URIs](cite:cites linktraversalpipeline),
we follow a [link queue-based approach](cite:cites linktraversaloptimization).
Concretely, we consider the link queue as the basis of the pipeline tree,
which is able to produce a (possibly infinite stream of RDF triples).
This queue accepts links from a set of link extraction components,
which are invoked for every document that has been dereferenced.
This link queue is initialized with a set of seed URIs,
and then continuously dereferences the URIs in the queue until it is empty.
Since the link extractors are invoked after every dereference operation,
this link queue may virtually become infinitely long.

For example, one link extractor may extract all objects of each RDF triple matching the `rdfs:seeAlso` predicate,
while another link extractor may extract all components of each triple that matches with a triple pattern within the query.
All link extractors only consider URIs as links,
and thereby ignore any matches for blank nodes and literals.

This queue is then connected to all tuple-producing SPARQL operators in the leaves of the query plan,
such as triple patterns and property path operators,
into which a stream of triples is sent.
The queue indexes all triples locally, to ensure that a triple pattern operator
that is executed later in the execution process does not miss any triples.



Cite RDF and SPARQL formal stuff
{:.todo}

Figure of link queue and how it's connected to everything and the link extractors
{:.todo}


### Discovery of data vault

Write me
{:.todo}

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
