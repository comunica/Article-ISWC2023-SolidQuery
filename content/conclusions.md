## Conclusions
{:#conclusions}

User-oriented decentralized applications require results in the order of seconds or less to [avoid losing the user's attention](cite:cites uiresponsetime).
Our work has shown that Link Traversal Query Processing is able to achieve such timings,
especially as it is able to produce results in an iterative manner, with first results mostly being produced in less than a second.
As such, LTQP with the algorithms introduced in this work are effective for querying over decentralized environments with specific structural properties,
but there are open research opportunities for optimizing more complex queries as provided by our benchmark.
We have shown this by applying LTQP to simulated Solid environments, for which we have introduced specific algorithms for capturing these structural properties.

Up until now, LTQP has always been applied to querying Linked Open Data on the Web.
In that context, it was assumed that [_"we should never expect complete results"_](cite:cites linktraversalsparql), because of the openness and distributed nature of the Web.
However, when applying LTQP to specific decentralized environments such as Solid,
this limitation does not hold anymore,
since there are additional assumptions that we can make use of during query execution.
For instance, the ability to close the world around Solid vaults,
and the data discovery techniques that Solid vaults provide,
create opportunities for query execution that allow us to guarantee complete results.
While we have investigated the specific case of querying Solid vaults,
these concepts may be generalizable to other decentralization efforts, such as [Mastodon](cite:cites mastodon).

Due to the expectation of incomplete results,
LTQP research over Linked Open Data is moving into the direction of
[finding query-relevant documents as early as possible](cite:cites linktraversaloptimization).
In the context of Solid, we have shown that finding all query-relevant documents is not the main bottleneck during query execution anymore.
Instead, the *effectiveness of the query plan* is has become the new bottleneck.
While finding query-relevant documents is still relevant for specific decentralized environments,
we show the need for more research towards better query planning techniques.
Since LTQP leads to data being discovered during query execution,
[adaptive query planning](cite:cites adaptive_book) techniques are highly promising.
So far, these techniques have only seem limited adoption within [LTQP](cite:cites linktraversaloptimization)
and [SPARQL query processing](cite:cites sparql_adaptive_anapsid, sparql_adaptive_ldeddies, sparql_adaptive_robust).
For example, we see many opportunities in decomposing parts of the query plan upon discovery of Solid type index entries,
which requires a type index-aware adaptive query plan optimizer.

Our findings indicate that discovery approaches such as the Solid type index have a great potential for improving query performance.
However, we have only scratched the surface of what is possible in this work.
On the one hand, usage of this type index can be further improved.
While we only made us of this index for explicit `rdf:type` occurrences in the query,
this could be extended to also consider implicit type knowledge via [inferencing](cite:cites rif).
Furthermore, [query containment techniques](cite:cites sparqlquerycontainment) could be investigated for determining which parts of the query match with index entries,
which could also lead to better a combination of type index and LDP-based discovery.
On the other hand, alternatives to the type index could offer more expressivity,
such as [characteristics sets](cite:cites characteristicssets) and [other summarization techniques](cite:cites comparingsummaries).
For example, the [Shape Trees](cite:cites spec:shapetrees) specification offers a similar index for Solid vaults,
but instead makes use of nested [ShEx](cite:cites spec:shex) shape definitions for expressing data locations,
which could be used for both data discovery and [query optimization](cite:cites shapestats).
Next to that, the incorporation of more expressive [Linked Data Fragments interfaces](cite:cites ldf, smartkg, sage, wisekg, spf, brtpf) in certain Solid vaults
could introduce interesting trade-offs in terms of server and client query execution effort.
However, since different vaults could expose different types of interfaces,
which are only discovered on the fly during query execution,
novel adaptive query execution algorithms for [heterogeneous interfaces](cite:cites heterogeneous_lars, heterogeneous_fedqpl, heterogeneous_replicas) will be required.
Finally, our work only considers read queries,
while similar open questions remain for write-queries when updating data within decentralized environments.

This work provides an answer to the increasing need of querying techniques across decentralized environments,
and uncovers the next steps that are needed for resolving current limitations.
As such, this brings us one step closer towards a decentralized Web where users can be in full control over their data.
