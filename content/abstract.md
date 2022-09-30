## Abstract
<!-- Context      -->
To counter societal and economic problems caused by data silos on the Web,
efforts such as Solid strive to reclaim private data by storing it in permissioned documents over a large number of personal vaults across the Web.
<!-- Need         -->
Building applications on top of such a decentralized knowledge graph
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
and provide the _SolidBench_ benchmark to simulate Solid environments representatively.
We introduce LTQP algorithms leveraging these structural properties,
and evaluate their effectiveness.
<!-- Findings     -->
Our experiments indicate that these new algorithms obtain complete query results in the order of seconds,
which existing algorithms cannot achieve.
Furthermore, we discuss limitations with respect to more complex queries.
<!-- Conclusion   -->
This work reveals that a traversal-based querying method using structural assumptions can be effective for large-scale decentralization,
but that advances are needed in the area of query planning for LTQP to handle more complex queries.
<!-- Perspectives -->
These insights open the door to query-driven decentralized applications,
in which declarative queries shield developers from the inherent complexity of a decentralized landscape.


<span id="keywords" rel="schema:about"><span class="title">Keywords</span>
<a href="https://en.wikipedia.org/wiki/Linked_Data" resource="http://dbpedia.org/resource/Linked_Data">Linked Data</a>,
<a href="https://en.wikipedia.org/wiki/Decentralization" resource="http://dbpedia.org/resource/Decentralization">Decentralization</a>,
<a href="https://en.wikipedia.org/wiki/Resource_Description_Framework" resource="http://dbpedia.org/resource/Resource_Description_Framework">RDF</a>,
<a href="https://en.wikipedia.org/wiki/SPARQL" resource="http://dbpedia.org/resource/SPARQL">SPARQL</a>,
Link Traversal,
<a href="https://solidproject.org/" resource="https://solidproject.org/">Solid</a>
</span>

<!--<span class="printonly" id="acmreferenceformat">
<span class="title">ACM Reference Format:</span>
Doe, J. My Awesome Article. In <i>Conference Companion: The Conference Companion, April 23—27, 2018, Lyon, France</i>. Publisher, New York, NY, USA, 4 pages.
<i>http://dx.doi.org/1.2/111.222</i>
</span>-->

<span class="printonly firstpagefooter">
<span class="footnotecopyright">
This paper is published under the Creative Commons Attribution 4.0 International (CC-BY 4.0) license.
Authors reserve their rights to disseminate the work on their personal and corporate Web sites with the appropriate attribution.<br />
<span style="font-style:italic">CONFERENCE</span><br />
©2018 Copyright,
published under Creative Commons CC BY 4.0 License.<br />
Publisher 1122.<br />
DOI: http://dx.doi.org/1.2/111.222
</span>
</span>
