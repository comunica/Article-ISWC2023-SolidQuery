## Introduction
{:#introduction}

Even though the Web has transformed our world to become more interconnected than ever before,
the Web is becoming increasingly centralized in recent years, contrary to [its original vision](cite:cites webproposal).
Today, the majority of data on the Web is flowing towards isolated *data silos*,
which are in the hands of large companies.
This siloization of data leads to a variety of problems,
ranging from issues with cross-silo interoperability and vendor lock-in
to privacy issues and the fact that data is in control by companies instead of users.

Because of these reasons, and the introduction of user-empowering legislature such as GDPR and CCPA,
[decentralization initiatives](cite:cites solid, mastodon, decentralizednanopubs) are gaining popularity.
The common goal of these initiatives is
to give people back control over their own data
by decentralizing data on the Web instead of making use of centralized data silos.
Initiatives such as [Solid](cite:cites solid) do this by allowing users to store any kind of data in their own personal data vault,
which are in full control by the users.
These data vaults form personal [Knowledge Graphs](cite:cites knowledgegraphs), which are often represented as collections of [Linked Data documents](cite:cites linkeddata).
The presence of such data vaults results in a massive distribution of data,
where practical cases such as social networking applications could require accessing thousands or even millions of documents
across different data vaults across the Web.
However, these applications can not yet be built today,
due to the lack of effective query execution techniques that are capable of handling the requirements of decentralized environments such as Solid.

The majority of research in the domain of query execution over Knowledge Graphs has been focused on centralized use cases,
where all data is captured in just one or a small number of federated sources, usually exposed as [SPARQL endpoints](cite:cites spec:sparqlprot).
Even though [several federated query execution approaches have been introduced](cite:cites fedx, tpf, hibiscus),
they have been designed for federating over *a few (~10) large sources*,
while decentralized environments such as Solid are identified by *a large number (~millions) of small sources*.
Furthermore, federated query execution techniques assume all sources to be known prior to query execution,
which is not feasible in decentralized environments due to the lack of a centralized index.
Therefore, these federated querying techniques are currently not suitable for decentralized environments.

[*Link Traversal Query Processing (LTQP)*](cite:cites linktraversal, linktraversalfoundations)
is an alternative query execution paradigm that is more promising for handling querying over large decentralized environments.
It is identified by its ability to query over a continuously growing range of documents that are discovered during query execution
by following hyperlinks between Linked Data documents using the [*follow-your-nose* principle](cite:cites linkeddata).
While LTQP has mainly been a theoretically interesting technique, it has not seen any practical use cases so far.

In this work, we prove that LTQP is not only interesting in theory,
but that it is an effective paradigm for handling querying execution within large decentralized environments.
We do this by exploiting specific structural properties within decentralized environments
that can be used for more effective source discovery and optimization of query execution.
We apply our research to the Solid ecosystem,
but these concepts are generalizable to [other decentralization initiatives](cite:cites mastodon, decentralizednanopubs).
To the best of our knowledge, this is the first in-depth analysis of query execution within the Solid ecosystem.

This article is structured as follows.
In the next section ([](#related-work)), we discuss the related work,
after which we provide an analysis of the structural properties of Solid data vaults in [](#solid).
Next, in [](#benchmark) we provide a benchmark that simulates a decentralized Solid environments based on this analysis.
In [](#approach), we introduce and explain LTQP algorithms that make use of these structural properties,
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
