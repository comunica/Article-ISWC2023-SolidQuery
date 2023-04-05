## Related Work
{:#related-work}

The Link Traversal Query Processing (LTQP) paradigm was introduced [more than a decade ago](cite:cites linktraversalsparql)
as a way to query over the Web of Linked Data as if it was a globally distributed dataspace,
without having to first index it in a single location.
LTQP does this by employing the [*follow-your-nose* principle of Linked Data](cite:cites linkeddata) during query execution,
where new RDF are continuously added to a local dataset while discovering new sources by following links between documents.
An [iterator-based pipeline](cite:cites linktraversalsparql)
allows execution to take place without waiting until all links have been followed.

LTQP is thus an [*integrated* approach](cite:cites linkeddatamanagement)
with parallel source discovery and query execution,
in contrast to [*two-phase* approaches](cite:cites summaries, comparingsummaries)
that perform data retrieval and indexing *before* query execution.
As a one-phase approach,
LTQP cannot rely on traditional pre-execution optimization algorithms
that require prior dataset statistics.
A [zero-knowledge query planning technique](cite:cites zeroknowldgequeryplanning)
instead orders triple patterns in a query based on link traversal-specific heuristics.
LTQP is related to the idea of [*SQL-based query execution over the Web*](cite:cites queryingwwwsql, infogatheringwwww3ql)
and to the concept of [*focused crawling*](cite:cites focusedcrawling, focusedcrawlingimproving).
While LTQP considers the Web of Linked Data a large database using the RDF data model,
SQL-based approaches focus on querying attributes or content within Web pages.
Focused crawlers search for Web pages of specific topics to populate a local database or index,
using a two-phase approach where a preprocessing step precedes execution.
While two-phase approaches in general are able to produce better query plans using traditional cardinality-based planning techniques,
waiting for data retrieval to be completed
may be impractical or even impossible for certain queries.

The number of potential links to be followed within the Web of Linked Data can become prohibitively large.
In the worst case, a single query could theoretically require traversing the entire Web.
Therefore, the [formal LTQP model](cite:cites linktraversalfoundations) enables different _reachability criteria_,
which embody strategies for deciding what links to follow, each leading to different result completeness semantics.
*cNone* follows no URLs,
*cAll* follows all URLs in all encountered triple components,
and *cMatch* only follows URLs in triple components for those triples that match a triple pattern within the query.
[_Context-based semantics_](cite:cites linktraversalpropertypaths) is an extension of these reachability semantics
to cope with property path expressions in the [SPARQL 1.1 language](cite:cites spec:sparqllang).
Next to query-driven reachability, [another extension](cite:cites guidedlinktraversal) introduces
the ability for data publishers to express which links should be followed using *subweb specifications*.

Next to filtering links via different semantics,
a second methodology for lowering query result arrival times is through [*link prioritization*](cite:cites linktraversaloptimization).
However, existing techniques only sometimes result in faster query results compared to no prioritization.

Even though [multiple query languages](cite:cites ldpath, ldql, nautilod) have been introduced specifically for LTQP,
its [SPARQL-based execution model](cite:cites linktraversalsparql) is still the most widely used.
Since SPARQL is the only language among these that is a standard, and the fact that it is more widely known and supported by different tools,
we make use of it within this work.
Nevertheless, the concepts within this work can be applied to other languages.

In general, LTQP has mostly been applied to querying Linked *Open* Data on the Web.
In contrast to our work, we apply LTQP to decentralized environments with specific structural properties,
and where not all data is open and accessible to everyone.
