## Introduction
{:#introduction}

Despite transforming our world to be more interconnected than ever before,
the Web has become increasingly centralized in recent years, contrary to [its original vision](cite:cites webproposal).
The majority of data on the Web today is flowing towards *data silos*,
which are in the hands of large companies.
This siloization of data leads to various problems,
ranging from issues with cross-silo interoperability and vendor lock-in,
to privacy breaches and individuals' data being controlled by companies instead of themselves.

Because of increasing awareness and user-empowering legislation such as the GDPR and CCPA,
[decentralization initiatives](cite:cites solid, bluesky, mastodon, decentralizednanopubs) are gaining popularity.
Their common goal is
to give people back control over their own data
by guarding it in chosen locations on the Web instead of aggregated in silos.
Initiatives such as [Solid](cite:cites solid) and [Bluesky](cite:cites bluesky) achieve this by allowing users to store any kind of data in their own _personal data vault_,
which they fully control.
In Solid, these data vaults form personal [Knowledge Graphs](cite:cites knowledgegraphs, dedecker_quweda_2022), represented as collections of [Linked Data documents](cite:cites linkeddata) containing [RDF triples](cite:cites spec:rdf).
The presence of such data vaults results in a large-scale distribution of data,
where applications involving multiple individuals' data
will require accessing thousands or even millions of access-controlled data items
in data vaults across the Web.

The state of the art in RDF query processing does not support such applications,
as it is unprepared to handle large-scale decentralization.
The RDF querying research of the past two decades
has focused on data that either _is_ or _can be_ centralized.
Some datasets would be in a [private or public quadstore](cite:cites spec:sparqlprot),
specifically designed to enable efficient query processing.
Other datasets would be Linked Open Data with a permissive license,
making it possible to copy or cache data in a local quadstore.
This permanent option to centralize
limited the need for decentralized query techniques
that allow data to remain at its source.
After all,
downloading and aggregating datasets
would nearly always be more efficient.
Yet when we use Linked Data for permissioned or sensitive data,
it becomes impossible to download datasets in their entirety.

While [_federated_ query execution approaches do exist](cite:cites fedx, tpf, hibiscus, splendid),
they currently handle a *small* number (~$$10$$) of *large* sources,
whereas decentralized environments such as Solid are characterized
by a *large* number (>$$10^3$$) of *small* sources.
Furthermore, federated query execution techniques assume sources to be known prior to query execution,
which is not feasible in decentralized environments due to the lack of a central index.
Hence, existing techniques are ill-suited for the envisaged scale of decentralization. 

[*Link Traversal Query Processing (LTQP)*](cite:cites linktraversal, linktraversalfoundations)
is an alternative query execution paradigm that is more promising for uncharted decentralized environments.
LTQP allows querying over a continuously growing selection of source documents that are discovered *during* query execution,
by following hyperlinks between Linked Data documents using the [*follow-your-nose* principle](cite:cites linkeddata).
Although LTQP has been theoretically interesting, it has not seen any practical use so far,
in particular because of performance concerns.

Fortunately,
decentralized ecosystems such as Solid exhibit additional structural properties
in addition to the Linked Data principles,
allowing us to improve query performance
through assumptions about data, documents, and their organization.
For example, Solid makes use of [Linked Data Platform](cite:cites spec:ldp) containers
to provide _completeness guarantees_ when finding data within vaults,
and provides a [Type Index](cite:cites spec:typeindex) to enable _type-based document discovery_.

In this work,
we prove that LTQP can be an effective paradigm
for the structured retrieval of data from unindexed decentralized Knowledge Graphs.
The key is to exploit the aforementioned structural properties
for more effective source discovery and query optimization.
While we apply our research to the Solid ecosystem,
these concepts may be generalizable to [other decentralization initiatives](cite:cites bluesky, mastodon, decentralizednanopubs).

This article is structured as follows.
In the [next section](#related-work), we discuss related work,
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
