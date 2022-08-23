## Abstract
<!-- Context      -->
As a way to counter societal and economic problems with centralization,
decentralization efforts such as Solid lead to data being spread over a massive number of Linked Data documents on the Web.
This raises significant technical issues when querying over this data as if it was a global Knowledge Graph,
since this data can not be centralized due to legal reasons,
and current (federated) querying techniques have not been designed to handle this large scale of data distribution.
<!-- Need         -->
Hence, there is a need for an alternative query execution paradigm that can cope with such decentralized environments.
<!-- Task         -->
For this, we propose the use of Link Traversal Query Processing (LTQP) for querying over decentralized environments,
and extend it by making use of structural properties within these environments.
<!-- Object       -->
In this article,
we analyze the structural properties of the Solid decentralization ecosystem that are relevant for query execution,
and provide a benchmark in which Solid environments can be simulated.
Furthermore, we introduce LTQP algorithms based on these structural properties,
and evaluate its effectiveness.
<!-- Findings     -->
Our experiments show that ...TODO...
<!-- Conclusion   -->
This works shows that a traversal-based querying method using structural assumptions is effective for querying over decentralized environments,
but that significant advances are needed in the area of query planning for LTQP.
<!-- Perspectives -->
This opens the window towards query-driven decentralized applications,
in which application developers interact with decentralized environments using declarative queries as if it was a single centralized database.


<span id="keywords" rel="schema:about"><span class="title">Keywords</span>
<a href="https://en.wikipedia.org/wiki/Linked_Data" resource="http://dbpedia.org/resource/Linked_Data">Linked Data</a>,
<a href="https://en.wikipedia.org/wiki/Resource_Description_Framework" resource="http://dbpedia.org/resource/Resource_Description_Framework">RDF</a>,
<a href="https://en.wikipedia.org/wiki/SPARQL" resource="http://dbpedia.org/resource/SPARQL">SPARQL</a>,
Link Traversal,
Solid
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
