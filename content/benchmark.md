## Benchmark
{:#benchmark}

In this section, we introduce *SolidBench*, a benchmark that enables reproducible performance measurements
of different query execution approaches within a decentralized environment.
For this, SolidBench simulates a decentralized Solid environment with corresponding workload representative of a social networking application.
Hereafter, we start by explaining the design considerations of the benchmark and our use case scenario, after which we introduce an overview of SolidBench.
Next, we zoom in on important details of the benchmark, such as fragmentation of the data and the query workload.

### Design considerations

The goal of our benchmark is to simulate a realistic decentralized environment based on the Solid ecosystem,
and provide a realistic workload that simulates a Solid application that reads data from one or more vaults.
To develop this benchmark, we built upon the choke point-based design methodology from [the Linked Data Benchmark Council](cite:cites ldbc).

Based on our analysis of the Solid ecosystem in [](#solid),
and requirements from [similar benchmarks](cite:cites ldbc, lingbm, ldbc_snb_interactive),
we introduce the following requirements for our benchmark:

1. **WebIDs**: Dataset consists of WebIDs corresponding to simulated agents. WebIDs refer to one LDP-based storage vault, and are represented using a standard RDF serialization.
2. **Data vaults**: Dataset consists of data vaults containing RDF files in LDP containers.
3. **Type indexes**: WebIDs link to type indexes, containing registrations for data in the agent's vault.
4. **Variability of vaults**: Data is organized differently in across vaults, to simulate different data organization preferences.
5. **Scalable dataset**: Dataset is configurable in the number of vaults and vault sizes.
6. **Workload**: Queries that evaluate different query operations and data linking structures.
7. **Metrics**: Measuring metrics such as total query execution time, result arrival times, number of HTTP requests, and correctness.
8. **Configuration**: Configurable in terms of queries and dataset properties, with default values.

### Social Network Scenario

Since the original goal of the Solid project was to enable social interactions in a decentralized manner,
the use case scenario our benchmark tackles is that of a social network.
Concretely, different users each have their own data vault, and each user can provide personal details about themselves in their WebID document such as name, city of residence, and birthday.
Next to that, users can express unidirectional knowledge relationships to other users.
Furthermore, users can create posts, or leave comments on other posts.
To stay in line with the ownership model of Solid, each post or comment a user creates, is stored within that user's data vault.
Hence, chains of comments can span multiple data vaults.

### SolidBench Overview

To avoid reinventing the wheel, we build upon the well-established [Social Network Benchmark (SNB)](cite:cites ldbc_snb_interactive, ldbc_snb_details),
which models a social network akin to Facebook, and meets most of the requirements of our desired use case scenario.

However, since SNB was designed to evaluate the performance of centralized query engine,
its dataset generator outputs its dataset in a single large file, e.g. serialized as RDF Turtle.
Since our aim is to simulate a decentralized social network,
we introduce a fragmentation layer on top of this generator.
This fragmenter is able to take in any dataset as input,
and provide a fragmented version of this dataset that simulates an interlinked set of Linked Data documents,
inspired by [WODSim](cite:cites walkingwithoutamap).
To simplify evaluation and testing,
we also provide a built-in Web server that can serve the generated data vaults over HTTP using a single command,
which is done using a slimmed-down version of the [Community Solid Server](cite:cites solidcommunityserver).
This server disables authentication and authorization by default,
so evaluations can focus on query performance.

For the query workload, we build upon the *interactive* workload of SNB,
and extend it with additional queries to cover link-related choke points.
Since the query templates that are produced by the generator of SNB assume a centralized dataset,
we also add a layer on top of these query templates that can transform the queries to correspond to the decentralized dataset.
Related to this, we also provide a tool that can produce validation queries and results to measure the correctness of a system.
Since we focus on read-only queries in this work, we do not consider the write queries of SNB.

All aspects of this benchmark are [fully configurable using JSON-LD configuration files](cite:cites componentsjs),
ranging from fragmentation strategies to properties of query templates.
Furthermore, we incorporate our benchmark into an existing benchmark runner (*name omitted due to double-blind review process*),
which enables convenient creation and execution of this benchmark.

By default, SolidBench sets the scale factor of the SNB generator to 0.1,
which results in 157.210 RDF files over 1.529 data vaults using the default fragmentation strategy.
In total, there are 3.556.159 triples across all files, with an average of 22,62 triples per file.
Even though this scale can be increased arbitrarily high,
we notice that this default scale can already stress existing LTQP approaches beyond their capabilities.
Furthermore, 27 query templates are provided which can be instantiated any number of times to simulate a query workload.
For more details on properties of this dataset and its schema, we refer to the [SNB papers](cite:cites ldbc_snb_interactive, ldbc_snb_details).
The benchmark is open-source at [https://anonymous.4open.science/r/webconf-2023-querysolid-benchmark/](https://anonymous.4open.science/r/webconf-2023-querysolid-benchmark/).

In summary, we introduce the following tools with SolidBench:

- **Dataset generator**: consisting of SNB's existing generator, and a new dataset fragmenter.
- **Query generator**: consisting of SNB's existing generator, and a new fragmentation-aware query template instantiator.
- **Validation generator**: building on top of SNB's validator, produces fragmentation-aware correctness validation sets containing queries and expected results.
- **Dataset server**: serving of fragmented datasets over HTTP.
- **Benchmark runner**: incorporation into an existing benchmarking system for execution against query engines via the [SPARQL protocol](cite:cites spec:sparqlprot).

<!--
157210 files
1529 pods
3556159 triples
3556159/157210=22,62

Counted using:
- find . type -f | wc -l
- ll | wc -l
- find . -type f -exec wc -l {} \; | awk '{total += $1} END{print total}'

Benchmark aspects:
- Generator: SNB+fragmenter: schema and generator for creating datasets at different scales
- 27 query templates covering different chokepoints (19 from SNB interactive and 8 new)
- Tools to execute experiment and measurement of metrics
-->

### Fragmentation

To convert the centralized dataset produced by SNB into a decentralized environment,
we provide a tool to fragment datasets using different fragmentation strategies.
While this tool is highly configurable in terms of its strategies using declarative JSON-LD-based configuration files,
we summarize its functionality in terms of two fragmentation dimensions for brevity.
Finally, we illustrate its functionality by discussing fragmentation strategies of posts within the SNB dataset.
All strategies within this tool are implemented in a streaming manner,
which means that it can handle input datasets of any size,
and the dataset does not have to be loaded fully in memory before it can be processed.

**Fragmentation dimensions**

*Triple document assignment* is the first dimension of fragmentation,
which concerns the task of deciding which triples are placed in what files.
Inspired by [WODSim](cite:cites walkingwithoutamap), we provide subject and object-based approaches,
which respectively place each triple in the file referred to by their subject or object.
These approaches can also be combined to place triples in both files referred to by subject and object.
Additionally, we provide composition-based approaches, using which triples matching certain triple patterns can be assigned to a different approach.
The second dimension of fragmentation is that of *URI rewriting*,
in which URIs can be modified to eventually end up in different documents according to the first dimension.
For example, this allows URIs matching a regex to be modified,
or triples to be appended upon matching a triple pattern.

**Strategies for fragmenting posts and comments**

Based on the two fragmentation dimensions discussed above,
our fragmenter can be configured to manage different fragmentation strategies
for posts and comments in the SNB data schema.
While these strategies can be applied to both posts and comments,
we only explain them hereafter in terms of posts:

1. **Separate**: Each post created by a person is placed in a separate RDF file within that person's vault.
2. **Single**: Posts created by a person are placed in a single RDF file within that person's vault.
3. **Location-based**: Posts created by a person are placed in files in that person's vault corresponding to the location at which the post was created.
4. **Time-based**: Posts created by a person are placed in files in that person's vault corresponding to the post creation day.
5. **Composite**: The strategies above are assigned randomly to all persons in the dataset.

By default, SolidBench makes use of the composite strategy,
which results in fragmentation variance across the different vaults,
which is realistic for the Solid ecosystem.

### Workload

As mentioned above, we make use of the *interactive* workload of SNB,
since these correspond to the workload that social network applications would produce.
We consider other SNB workloads (such as the business intelligence workload) out of scope,
since these perform dataset analytics, which requires access to the whole dataset,
which is not feasible in decentralization environments such as Solid where data can reside behind access control.
Furthermore, we also focus solely on the class of read-only queries due to the scope of this article,
but our approach can easily be extended towards write queries.

The SNB interactive workload consists of two classes of query templates: *short* and *complex* read queries.
Since these queries cover the choke points related to linking structures only partially,
we add *discover* query templates as third class.

**Choke points**

<!-- https://arxiv.org/pdf/2001.02299.pdf -->


Following the [choke point-based design methodology](cite:cites ldbc),
the short and complex SNB interactive workloads already cover the majority of the 33 choke points introduced by SNB.
We refer to the [SNB specification](cite:cites ldbc_snb_details) for more details
on the correlation of short and complex query templates to these choke points.
Since the short and complex query classes only partially cover choke points related *linking structures* within data vaults,
we introduce *discover* queries dedicated for covering these choke points on linking structures.
The full list of linking-related choke points and discover queries are available in the [appendix](#appendix-workload).
More details on all query templates can be found at [https://anonymous.4open.science/r/webconf-2023-querysolid-benchmark/templates/queries/README.md](https://anonymous.4open.science/r/webconf-2023-querysolid-benchmark/templates/queries/README.md).

**Query template instantiation**

Our benchmark contains 27 query templates, from which 19 are derived from queries within SNB.
These query templates can be instantiated multiple times for different resources, based on the dataset scale.
By default, each template is instantiated five times, so that metrics can be averaged to reduce the effect of outliers.
Due to the fragmentation and URI rewriting we apply on top of the SNB dataset,
we were unable to make use of the standard SNB query templates and its method of query instantiation.
Therefore, we have implemented a custom query template instantiation tool that takes into account these fragmentations.

[](#template-discover-8) shows an example of the template for discover query 8,
which covers the majority of choke points related to linking structures.
It is instantiated with a person's WebID URI, and finds all messages
created by people that this person likes messages from.
<!--Since it starts from all liked messages of the starting person, then navigates to the creator of those messages,
and then retrieves the contents of those messages, it covers CP L.6.
Furthermore, since messages are fragmented in different ways across vaults, and the query spans different vaults, it covers CP L.7.
Since messages can be captured within the user's type index, CP L.8 is also covered.
Finally, since only message-related documents needs to be retrieved from the vaults,
all other documents could potentially pruned out, covering CP L.9.-->

<figure id="template-discover-8" class="listing">
````/code/template-discover-8.txt````
<figcaption markdown="block">
SPARQL template for discover query 8.
</figcaption>
</figure>

**Metrics**

We consider the following performance metrics in SolidBench:

- **Query execution time**: Time (milliseconds) between sending the query to the engine, and obtaining the final result.
- **Query result arrival times**: For each result, time (milliseconds) between sending the query, and obtaining that result.
- **HTTP requests**: For a query, the number of HTTP requests the engine issued during its execution.
- **Correctness**: The percentage of query results conforming to the expected query results.

For measuring query execution and result arrival times,
a warmup round with all instantiated query templates must take place first.
To ensure that we take into account the volatile nature of the Web and the live traversal property of LTQP,
the HTTP cache of the query engine is flushed before every query execution,
while this cache can still be used within the span of a single query execution.
