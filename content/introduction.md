## Introduction
{:#introduction}

Despite transforming our world to be more interconnected than ever before,
the Web has become increasingly centralized in recent years, contrary to [its original vision](cite:cites webproposal).
Today, the majority of data on the Web is flowing towards isolated *data silos*,
which are in the hands of large companies.
This siloization of data leads to various problems,
ranging from issues with cross-silo interoperability and vendor lock-in,
to privacy issues and individuals' data being controlled by companies instead of themselves.

Because of these reasons, and the introduction of user-empowering legislature such as GDPR and CCPA,
[decentralization initiatives](cite:cites solid, bluesky, mastodon, decentralizednanopubs) are gaining popularity.
Their common goal is
to give people back control over their own data
by guarding it in chosen locations on the Web instead of aggregated in silos.
Initiatives such as [Solid](cite:cites solid) and [Bluesky](cite:cites bluesky) achieve this by allowing users to store any kind of data in their own _personal data vault_,
which they fully control.
In Solid, these data vaults form personal [Knowledge Graphs](cite:cites knowledgegraphs, dedecker_quweda_2022), represented as collections of [Linked Data documents](cite:cites linkeddata) containing [RDF triples](cite:cites spec:rdf).
The presence of such data vaults results in a large-scale distribution of data,
where applications involving multiple individuals' data require accessing
thousands or even millions of documents
across different data vaults across the Web.
These applications cannot effectively be built today
due to the lack of query processing techniques that can handle such decentralized environments.

The majority of research in the domain of query execution over Knowledge Graphs on the Web has been focused on _centralized_ use cases,
where all data is captured in a single or a small number
of sources, usually exposed as [SPARQL endpoints](cite:cites spec:sparqlprot).
Even though [several federated query execution approaches exist](cite:cites fedx, tpf, hibiscus, splendid),
they have been designed for federating over *a few (~10) large sources*,
while decentralized environments such as Solid are identified by *a large number (~thousands) of small sources*.
Furthermore, federated query execution techniques assume sources to be known prior to query execution,
which is not feasible in decentralized environments due to the lack of a central index.
Hence, they are unsuitable for decentralized environments.

[*Link Traversal Query Processing (LTQP)*](cite:cites linktraversal, linktraversalfoundations)
is an alternative query execution paradigm that is more promising for uncharted decentralized environments.
It can query over a continuously growing range of documents that are discovered during query execution,
by _following hyperlinks_ between Linked Data documents using the [*follow-your-nose* principle](cite:cites linkeddata).
While LTQP has been theoretically interesting, it has not seen any practical use so far,
in particular because of performance concerns.

Besides the [Linked Data principles](cite:cites linkeddata),
decentralization efforts such as Solid provide additional structural properties on top of these principles that allow us to make
additional assumptions about data, documents, and their organization during query execution.
For example, Solid makes use of [Linked Data Platform](cite:cites spec:ldp) containers
to provide _completeness guarantees_ when finding data within vaults,
and it provides the [Type Index](cite:cites spec:typeindex) to enable _type-based document discovery_.

In this work, we prove that LTQP
can be an effective paradigm,
if we exploit specific structural properties within decentralized environments
for more effective source discovery and query optimization.
While we apply our research to the Solid ecosystem,
these concepts may be generalizable to [other decentralization initiatives](cite:cites bluesky, mastodon, decentralizednanopubs).
To the best of our knowledge, this is the first querying analysis for the Solid ecosystem.

This article is structured as follows.
In the [next section](#related-work), we discuss the related work,
after which we provide an analysis of the structural properties of Solid data vaults in [](#solid).
In [](#approach), we introduce LTQP algorithms that make use of these structural properties,
which are evaluated in [](#evaluation).
Finally, we conclude in [](#conclusions).

<!--
Contributions:
- Analysis of structural properties in solid pods
- SolidBench: Benchmark to simulate a Web of data with configurable structural axioms
- Guided link traversal algorithms for querying over Solid data vaults
- Implementation of existing (all?) and new algorithms
- An evaluation of link traversal algorithms within a simulated Web of Solid data vaults
{:.todo}
-->
