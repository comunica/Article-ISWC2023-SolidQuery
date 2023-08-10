## Abstract
<!-- Context      -->
To counter societal and economic problems caused by data silos on the Web,
efforts such as Solid strive to reclaim private data by storing it in permissioned documents over a large number of personal vaults across the Web.
<!-- Need         -->
Building applications on top of such a decentralized Knowledge Graph
involves significant technical challenges:
centralized aggregation prior to query processing is impossible for legal reasons,
and current federated querying techniques cannot handle this large scale of distribution
at the expected performance.
<!-- Task         -->
We propose an extension to Link Traversal Query Processing (LTQP)
that incorporates structural properties within decentralized environments
to tackle their unprecedented scale.
<!-- Object       -->
In this article,
we analyze the structural properties of the Solid decentralization ecosystem that are relevant for query execution,
we introduce novel LTQP algorithms leveraging these structural properties,
and evaluate their effectiveness.
<!-- Findings     -->
Our experiments indicate that these new algorithms obtain correct results in the order of seconds,
which existing algorithms cannot achieve.
<!-- Conclusion   -->
This work reveals that a traversal-based querying method using structural assumptions can be effective for large-scale decentralization,
but that advances are needed in the area of query planning for LTQP to handle more complex queries.
<!-- Perspectives -->
These insights open the door to query-driven decentralized applications,
in which declarative queries shield developers from the inherent complexity of a decentralized landscape.
