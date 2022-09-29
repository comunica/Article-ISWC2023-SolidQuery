## Related Work
{:#related-work}

In this section, we discuss the related work around Link Traversal Query Processing,
and related benchmarks for this paradigm.

### Link Traversal Query Processing

The Link Traversal Query Processing (LTQP) paradigm was introduced [more than a decade ago](cite:cites linktraversalsparql)
as a way to query over the Web of Linked Data as if it was a single globally distributed dataspace,
without having to first index it in a single location.
LTQP does this by employing the [*follow-your-nose* principle of Linked Data](cite:cites linkeddata) during query execution,
where new RDF triples are continuously added to a local dataset while discovering new sources by following links between documents.
For this, an [iterator-based pipeline](cite:cites linktraversalsparql) was introduced
that allows query execution to take place without having to wait until all links have been followed.

Since query execution happens in parallel with source discovery in LTQP,
traditional query optimization algorithms that happen before query execution using statistics of the dataset can not be used,
as those statistics are not known yet.
To cope with this problem, a [zero-knowledge query planning technique](cite:cites zeroknowldgequeryplanning)
was introduced that orders triple patterns in a query based on several link traversal-specific heuristics.

In practise, the number of links that could be followed within the Web of Linked Data can become very large.
In the worst case, a single query could require traversing the whole Web, which is not feasible in practise.
Therefore, the [formal LTQP model](cite:cites linktraversalfoundations) enables different reachability criteria,
which are different strategies for deciding which links to follow, each leading to different semantics of query result completeness.
These reachability criteria are the following:

cNone
: No links are followed

cMatch
: Only links in triples are followed for those triples match with a triple pattern within the query.

cAll
: All links are followed

[Context-based semantics](cite:cites linktraversalpropertypaths) is an extension of these reachability semantics
that was introduced to be able to cope with property path expressions in the [SPARQL 1.1 language](cite:cites spec:sparqllang).
Furthermore, next to query-driven reachability, [another extension](cite:cites guidedlinktraversal) of the LTQP model introduces
the ability for data publishers to express which links should be followed using *subweb specifications*.

Next to filtering links via different reachability semantics,
a second methodology for improving query result arrival times is through [*link prioritization*](cite:cites linktraversaloptimization).
However, existing techniques are based on heuristics, which only sometimes result in faster query results compared to no prioritization.

LTQP is considered an [*integrated* query execution approach](cite:cites linkeddatamanagement),
where data retrieval is done *during* query execution.
This is in contrast to *two-phase* query execution approaches,
which involve retrieving and indexing data *before* query execution.
While [two-phase approaches](cite:cites summaries, comparingsummaries) are able to produce better query plans using traditional cardinality-based planning techniques,
they have to wait for the data retrieval phase to be completed,
which may be impractical or even impossible for certain queries.

Even though [multiple query languages](cite:cites ldql, nautilod, ldpath) have been introduced specifically for LTQP,
its [SPARQL-based execution model](cite:cites linktraversalsparql) is still the most widely used.
Since SPARQL is the only language among these that is a standard, and the fact that it is more widely known and supported by different tools,
we make use of it within this work.
Nevertheless, the concepts within this work can be applied to other languages as well.

LTQP is related to the idea of [SQL-based query execution over the Web](cite:cites queryingwwwsql, infogatheringwwww3ql).
However, while LTQP considers the Web of Linked Data a large database using the RDF data model,
these SQL-based approaches over the Web focus on querying attributes or content within Web pages.
Furthermore, LTQP is also related to the concept of [focused crawling](cite:cites focusedcrawling, focusedcrawlingimproving),
where crawlers search for Web pages of specific topics to populate a local database or index.
Focus crawling techniques are however designed to run as a preprocessing step *before* query execution,
while LTQP traverses documents *during* query execution.

### Link Traversal Benchmarks

Aside from the lack of reusable link traversal query engines,
there is also a lack of easily reusable benchmarks for evaluation the performance of link traversal query engines.
According to a recent [survey of Linked Data querying approaches](cite:cites linkeddatamanagement),
*"there are no well-defined and well-understood benchmarks to test Linked Data query processing systems"*.
Nevertheless, there has been work that provide a foundation onto which benchmarks may be built.

[QWalk](cite:cites linktraversaldiverse) is a methodology for building a large set of queries based on random walks through an existing dataset.
The output of this methodology is a set of Basic Graph Patterns that crosses through various documents through dereferenceable links.
QWalk has been designed to be executed on the actual Web, to evaluate uncontrolled environments.
Due this this, most of the queries generated by the authors make use of links that have gone dead by now,
which makes these queries unsuitable for a reliable and reusable benchmark.
Furthermore, the authors propose making use of queries from existing benchmarks
such as [FedBench](cite:cites fedbench) and [DBpedia SPARQL Benchmark](cite:cites dbpediasparqlbenchmark) for execution over the actual Web.
Unfortunately, these also lead to inconsistent and non-reproducible experimental results,
which makes them unsuitable for reliable link traversal benchmarking.
In contrast, the benchmark we propose makes use of a closed environment that is in full control of the experimenter.

[WODSim](cite:cites walkingwithoutamap) is a tool that accepts an RDF dataset as input,
and is able to simulate a Web of Linked Data documents.
For each triple in the dataset, it can either place the triple inside the Linked Data document(s)
identified by the triple's subject, object, or both.
While this tool is beneficial for running experiments in a reproducible manner,
it does not ship with a standard dataset, which makes is less convenient for reusability.
Our benchmark builds on top of the WODSim approach for fragmenting triples into documents,
and also provides more advanced fragmentation strategies.
Furthermore, it ships with a standard dataset for more convenient usage.

Aside from these approaches, evaluation of link traversal engines
is usually done via [hand crafted queries](cite:cites linktraversalsparql, linktraversalcaching, zeroknowldgequeryplanning, sihjoin).
