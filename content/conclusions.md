## Conclusions
{:#conclusions}

User-oriented decentralized applications require results in the order of seconds or less to [avoid losing the user's attention](cite:cites uiresponsetime).
Our work has shown that Link Traversal Query Processing is able to achieve such timings,
especially as it is able to produce results in an iterative manner, with first results mostly being produced in less than a second.
As such, LTQP with the algorithms introduced in this work is effective for querying over decentralized environments with specific structural properties,
but there are open research opportunities for optimizing more complex queries as provided by the benchmark.
We have shown this by applying LTQP to simulated Solid environments, for which we have introduced algorithms to capture these structural properties.

Before this work, LTQP tended to exclusively focus on Linked Open Data,
which centralized quadstores handle better anyway.
After all, in addition to major performance concerns,
it was assumed that [<q>we should never expect complete results</q>](cite:cites linktraversalsparql)  with LTQP because of the unbounded and distributed nature of the Web.
Prior aggregation was therefore always the logical option for efficient queries,
limiting the evaluation space to fixed datasets with a compatible license.

However, LTQP becomes relevant again within decentralized environments such as Solid,
where aggregation is not an option because of permissioning.
Performance concerns can be addressed
through the usage of additional assumptions during query execution.
For instance, the ability to close the world around Solid vaults,
and the data discovery techniques that Solid vaults provide,
create opportunities for query execution that allow us to guarantee complete results.
While we have investigated the specific case of querying Solid vaults,
these concepts may be generalizable to other [decentralization efforts](cite:cites bluesky, mastodon).
This is possible, because our approach solely relies on the structural properties
provided by specifications such as [LDP](cite:cites spec:ldp) and the [Type Index](cite:cites spec:typeindex),
which can be used outside of the Solid ecosystem.

LTQP research over Linked Open Data is exploring the direction of
[finding query-relevant documents as early as possible](cite:cites linktraversaloptimization)
due to the possibility of incomplete results.
In the context of Solid, we have shown that finding all query-relevant documents is not the main bottleneck during query execution anymore.
Instead, the *effectiveness of the query plan* has become the new bottleneck.
While finding query-relevant documents is still relevant for specific decentralized environments,
we show the need for more research towards better query planning techniques.
Since LTQP leads to data being discovered during query execution,
[adaptive query planning](cite:cites adaptive_book) techniques are highly promising.
So far, these techniques have only seen limited adoption within [LTQP](cite:cites linktraversaloptimization)
and [SPARQL query processing](cite:cites sparql_adaptive_anapsid, sparql_adaptive_ldeddies, sparql_adaptive_robust).

Our findings indicate that discovery approaches such as the Solid Type Index harbor a great potential for improving query performance,
and future work in the direction of _implicit type knowledge_ within queries (e.g., through RDFS reasoning)
and query decomposition over _different type index entries_ could be relevant.
Furthermore, alternative structural properties could offer more expressivity,
such as [_characteristics sets_](cite:cites characteristicssets) and [other _summarization techniques_](cite:cites comparingsummaries, spec:shapetrees).
Additionally, more work is needed to investigate the impact of [privacy](cite:cites privacy_aggregation) and [security](cite:cites ldtraversalsecurity) during LTQP over decentralized environments.
The incorporation of more expressive [Linked Data Fragments interfaces](cite:cites ldf, smartkg, sage, wisekg, spf, brtpf) in certain Solid vaults
could introduce interesting trade-offs in terms of server and client query execution effort,
especially if they can be combined in a [heterogeneous manner](cite:cites heterogeneous_lars, heterogeneous_fedqpl, heterogeneous_replicas).

In summary, traversal-based querying over decentralized environments can become practically feasible performance-wise.
Furthermore, it is useful given the lack of alternatives, because centralization of private data may not be feasible or legal.
However, for complex queries, more improvements through future research are needed.

This work provides answers to the increasing need of querying over decentralized environments,
and uncovers next steps for resolving current limitations.
Hence, it brings us closer to querying a decentralized Web where users are in full control.
