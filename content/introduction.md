## Introduction
{:#introduction}

Write me.
{:.todo}

- Query research mainly focused on centralization or federation over a small number of sources, but query research over a large number of sources remains limited (cite link traversal)
- Due to various reasons, decentralization efforts (solid, mastodon) are gaining popularity, where centralization is impossible due to various reasons
- With data being spread over a huge number document-based sources, querying becomes difficult
- While federated querying techniques don't scale to the number of required sources, and depend on SPARQL endpoints, they are not suitable
- In this work, we investigate how link traversal can be used for querying within decentralized environments
    - The goal of this work is to evaluate the effectiveness of link traversal decentralized environments, since centralization is not possible.
- For this, link traversal can make use of assumptions within decentralized environments for optimizing query execution
- We introduce and evaluate algorithms for guided link traversal over decentralized environments
    - We apply our research to Solid use case, but the concepts are generalizable to other decentralization initiatives (mastodon, nanopubs, ...)

Contributions:
- SolidBench: Benchmark to simulate a Web of data with configurable structural axioms
- Guided link traversal algorithms for querying over Solid data vaults
- Implementation of existing (all?) and new algorithms
- An evaluation of link traversal algorithms within a simulated Web of Solid data vaults
{:.todo}
