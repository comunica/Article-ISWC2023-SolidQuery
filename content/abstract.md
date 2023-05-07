## Abstract
<!-- Context      -->
To counter societal and economic problems caused by data silos on the Web,
efforts such as Solid strive to reclaim private data by storing it in permissioned documents over a large number of personal vaults across the Web.
<!-- Need         -->
Building applications on top of such a decentralized Knowledge Graph
involves significant technical challenges:
centralized aggregation prior to query processing is excluded for legal reasons,
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
Our experiments indicate that these new algorithms obtain accurate results in the order of seconds,
which existing algorithms cannot achieve.
<!-- Conclusion   -->
This work reveals that a traversal-based querying method using structural assumptions can be effective for large-scale decentralization,
but that advances are needed in the area of query planning for LTQP to handle more complex queries.
<!-- Perspectives -->
These insights open the door to query-driven decentralized applications,
in which declarative queries shield developers from the inherent complexity of a decentralized landscape.


<span class="comment" data-author="RV">Below, I'm trying an abstract with a different angle. It's not necessarily better; it's different. The aim with the abstract below is to strategically focus the minds of reviewers: yes, LTQP is an old technique and no one likes it. But the conditions have changed now.</span>

<!-- Context      -->
The Link Traversal Query Processing (LTQP) technique,
in which SPARQL queries are evaluated over an open Web of documents
rather than a highly optimized quadstore,
has failed to become more than a topic of theoretical interest
due to severe practical issues such as limited performance.
However,
having so far only been applied to _public_ data,
<!-- Need         -->
its applicability needs to be reconsidered for different environments.
The incorporation of _private_ data on the Web
could tip the balance in favor of LTQP,
with privacy concerns restricting
the permitted expressivity of traditional quadstore query interfaces
and the extent to which they are allowed to cache or aggregate data.
Additionally,
such closed contexts lead to more predicable data distributions,
which can help mitigate longstanding LTQP performance issues.
<!-- Task         -->
We created an extension to LTQP
that incorporates structural properties within decentralized environments.
<!-- Object       -->
In this article,
we discuss relevant structural properties of personal data vaults,
introduce novel LTQP algorithms leveraging these structural properties,
and evaluate their effectiveness.
<!-- Findings     -->
We show that accurate results are obtained in the order of seconds,
which existing algorithms cannot achieve.
<!-- Conclusion   -->
Our work reveals that LTQP method using structural assumptions can be effective for large-scale decentralized private data.
<!-- Perspectives -->
This insight opens the door to query-driven decentralized applications,
in which declarative queries shield developers from the inherent complexity of a decentralized landscape.
