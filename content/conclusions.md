## Conclusions
{:#conclusions}

User-oriented decentralized applications require results in the order of seconds or less to [avoid losing the user's attention](cite:cites uiresponsetime).
Our work has shown that Link Traversal Query Processing is able to achieve such timings,
especially as it is able to produce results in an iterative manner, with first results mostly being produced in less than a second.
As such, LTQP with the algorithms introduced in this work are effective for querying over decentralized environments with specific structural properties,
but there are open research opportunities for optimizing more complex queries as provided by the benchmark.
We have shown this by applying LTQP to simulated Solid environments, for which we have introduced algorithms to capture these structural properties.

Up until now, LTQP has always been applied to querying Linked Open Data on the Web.
In that context, it was assumed that [_"we should never expect complete results"_](cite:cites linktraversalsparql), because of the openness and distributed nature of the Web.
However, when applying LTQP to specific decentralized environments such as Solid,
this limitation does not hold anymore,
<span class="comment" data-author="RV">Yes, so that was actually the elephant in the room for me. How do we connect this for the reader to the fact that we are not able to reach 100% accuracy?</span>
since there are additional assumptions that we can make use of during query execution.
For instance, the ability to close the world around Solid vaults,
and the data discovery techniques that Solid vaults provide,
create opportunities for query execution that allow us to guarantee complete results.
While we have investigated the specific case of querying Solid vaults,
these concepts may be generalizable to other [decentralization efforts](cite:cites bluesky, mastodon).
This is possible, because our approach solely relies on the structural properties
provided by standards such as [LDP](cite:cites spec:ldp) and the [Type Index](cite:cites spec:typeindex),
which can be used outside of the Solid ecosystem.

Due to the possibility of incomplete results,
LTQP research over Linked Open Data is moving into the direction of
[finding query-relevant documents as early as possible](cite:cites linktraversaloptimization).
In the context of Solid, we have shown that finding all query-relevant documents is not the main bottleneck during query execution anymore.
<span class="comment" data-author="RV">This is again where the 94% is bothering me; let's discuss tomorrow. Perhaps a measure of completeness of all relevant documents is needed?</span>
Instead, the *effectiveness of the query plan* has become the new bottleneck.
While finding query-relevant documents is still relevant for specific decentralized environments,
we show the need for more research towards better query planning techniques.
Since LTQP leads to data being discovered during query execution,
[adaptive query planning](cite:cites adaptive_book) techniques are highly promising.
So far, these techniques have only seem limited adoption within [LTQP](cite:cites linktraversaloptimization)
and [SPARQL query processing](cite:cites sparql_adaptive_anapsid, sparql_adaptive_ldeddies, sparql_adaptive_robust).

Our findings indicate that discovery approaches such as the Solid type index have a great potential for improving query performance,
<span class="comment" data-author="RV">Can we name numbers here? <q>We see improvements up to xxx% compared to…</q></span>
and future work in the direction of implicit type knowledge in queries and query decomposition over different type index entries could be relevant.
Furthermore, alternative structural properties could offer more expressivity,
such as [characteristics sets](cite:cites characteristicssets) and [other summarization techniques](cite:cites comparingsummaries, spec:shapetrees).
Next to that, the incorporation of more expressive [Linked Data Fragments interfaces](cite:cites ldf, smartkg, sage, wisekg, spf, brtpf) in certain Solid vaults
could introduce interesting trade-offs in terms of server and client query execution effort,
especially if they can be combined in a [heterogeneous manner](cite:cites heterogeneous_lars, heterogeneous_fedqpl, heterogeneous_replicas).

This work provides an answer to the increasing need of querying over decentralized environments,
and uncovers next steps for resolving current limitations.
Hence, it brings us closer towards querying a decentralized Web where users are in full control.
