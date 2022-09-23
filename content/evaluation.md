## Evaluation
{:#evaluation}

In this section, we tackle the research question _"How well does link traversal query processing perform over decentralized environments with structural properties"_.
Within this work, we apply this to the structural properties of the decentralized environment provided by Solid,
but findings may be generalizable.
We provide an answer to this research question by simulating Solid data vaults using the benchmark introduced in [](#benchmark) using the default configuration,
and evaluating different approaches based on the implementation discussed in [](#approach).

We first introduce the design of our experiment,
followed by the results of these experiments,
and a discussion of our results to answer our research question.

### Experimental Design

We make use of a full factorial experiment containing the following factors and values:

- **Source selector strategy**: None, LDP, Type Index, Filtered Type Index, LDP and Type Index, LDP and Filtered Type Index
- **Reachability semantics**: cNone, cMatch, cAll

Within the source selectors, the LDP strategy corresponds to the disjunction of the source selectors $$\sigma_{\text{SolidVault}}$$ and $$\sigma_{\text{LdpContainer}}$$,
the Type Index corresponds to $$\sigma_{\text{LdpContainer}}$$ and $$\sigma_{\text{SolidTypeIndex}}$$ with $$\phi(B, c)$$ always returning `true`,
and the Filtered Type Index corresponds to $$\sigma_{\text{LdpContainer}}$$ and $$\sigma_{\text{SolidTypeIndex}}$$ with $$\phi_{\text{QueryClass}}$$,

Our experiments were performed on a 64-bit Ubuntu 14.04 machine with a 24-core 2.40 GHz CPU and 128 GB of RAM.
The Solid vaults and query client were executed in isolated Docker containers on dedicated CPU cores with a simulated network.
To foster reproducibility,
the experimental setup, raw results, and scripts to process them will be made available after the double-blind review process.
All queries were configured with a timeout of two minutes, and were executed three times to average metrics over.
Each query template in the benchmark was instantiated five times, which resulted in 40 discover queries, 35 short queries, and 60 complex queries.

### Experimental Results

In this section, we present only those results that offer insights into the research question.
[](#results-queries-discover), [](#results-queries-short), and [](#results-queries-complex)
show the aggregated results for the different combinations of our setup
for the discover, short, and complex queries of the benchmark, respectively.
Within these tables, the execution time, number of actual results, and correctness are averaged across all executed queries.
The number of HTTP requests is counted across all query executions that did not time out within each combination.
The timeout column represents the number of query templates that lead to a timeout for a given combination.
The correctness of each query execution is calculated as the percentage of expected results that were actually produced.

<figure id="results-queries-discover" class="table" markdown="1">

| Experiment | Time | Requests | Results | Correctness | Timeouts |
| --- | ---: | ---: | ---: | ---: | ---: |
| cnone-base | 43 | 8 | 0.00 | 0.00% | 0 |
| cmatch-base | 4,658 | 437 | 1.25 | 12.50% | 0 |
| call-base | 83,734 | 446 | 2.88 | 20.63% | 7 |
| cnone-idx | 2,040 | 291 | 20.50 | 74.14% | 0 |
| cmatch-idx | 16,529 | 664 | 39.13 | 99.14% | 0 |
| call-idx | 80,597 | 478 | 3.63 | 24.38% | 7 |
| cnone-idx-filt | 1,926 | 278 | 20.50 | 74.14% | 0 |
| cmatch-idx-filt | 16,253 | 631 | 39.13 | 99.14% | 0 |
| call-idx-filt | 80,439 | 482 | 3.50 | 23.75% | 7 |
| cnone-ldp | 2,431 | 342 | 20.50 | 74.14% | 0 |
| cmatch-ldp | 19,113 | 831 | 39.13 | 99.14% | 0 |
| call-ldp | 83,691 | 442 | 2.88 | 20.63% | 7 |
| cnone-ldp-idx | 2,844 | 406 | 20.50 | 74.14% | 0 |
| cmatch-ldp-idx | 17,026 | 733 | 39.13 | 99.14% | 0 |
| call-ldp-idx | 80,234 | 476 | 3.63 | 24.38% | 7 |
| cnone-ldp-idx-filt | 2,879 | 405 | 20.50 | 74.14% | 0 |
| cmatch-ldp-idx-filt | 16,903 | 750 | 39.13 | 99.14% | 0 |
| call-ldp-idx-filt | 80,228 | 467 | 3.63 | 24.38% | 7 |

<figcaption markdown="block">
Overview of the average execution times (ms), total number of HTTP requests, average number of results, correctness, and number of timeouts across all 8 **discover** queries.
</figcaption>
</figure>

<figure id="results-queries-short" class="table" markdown="1">

| Experiment | Time | Requests | Results | Correctness | Timeouts |
| --- | ---: | ---: | ---: | ---: | ---: |
| cnone-base | 80 | 15 | 0.14 | 14.29% | 0 |
| cmatch-base | 27,429 | 596 | 0.43 | 42.86% | 2 |
| call-base | 119,280 | 0 | 0.29 | 28.57% | 7 |
| cnone-idx | 2,023 | 121 | 1.57 | 28.57% | 0 |
| cmatch-idx | 69,039 | 275 | 0.43 | 42.86% | 4 |
| call-idx | 119,521 | 0 | 0.29 | 28.57% | 7 |
| cnone-idx-filt | 1,995 | 121 | 1.57 | 28.57% | 0 |
| cmatch-idx-filt | 68,785 | 275 | 0.43 | 42.86% | 4 |
| call-idx-filt | 119,699 | 0 | 0.29 | 28.57% | 7 |
| cnone-ldp | 2,372 | 140 | 1.57 | 28.57% | 0 |
| cmatch-ldp | 69,403 | 291 | 0.43 | 42.86% | 4 |
| call-ldp | 119,237 | 0 | 0.29 | 28.57% | 7 |
| cnone-ldp-idx | 2,784 | 164 | 1.57 | 28.57% | 0 |
| cmatch-ldp-idx | 68,973 | 307 | 0.43 | 42.86% | 4 |
| call-ldp-idx | 119,196 | 0 | 0.29 | 28.57% | 7 |
| cnone-ldp-idx-filt | 2,833 | 164 | 1.57 | 28.57% | 0 |
| cmatch-ldp-idx-filt | 69,138 | 307 | 0.43 | 42.86% | 4 |
| call-ldp-idx-filt | 119,599 | 0 | 0.29 | 28.57% | 7 |

<figcaption markdown="block">
Overview of the average execution times (ms), total number of HTTP requests, average number of results, correctness, and number of timeouts across all 7 **short** queries.
</figcaption>
</figure>

<figure id="results-queries-complex" class="table" markdown="1">

| Experiment | Time | Requests | Results | Correctness | Timeouts |
| --- | ---: | ---: | ---: | ---: | ---: |
| cnone-base | 12,876 | 11 | 0.00 | 0.00% | 1 |
| cmatch-base | 16,693 | 1,620 | 0.50 | 2.50% | 4 |
| call-base | 39,907 | 0 | 0.00 | 0.00% | 12 |
| cnone-ldp | 19,072 | 351 | 0.00 | 0.00% | 4 |
| cmatch-ldp | 38,281 | 106 | 0.00 | 0.00% | 11 |
| call-ldp | 40,022 | 0 | 0.00 | 0.00% | 12 |
| cnone-ldp-idx | 19,890 | 400 | 0.00 | 0.00% | 4 |
| cmatch-ldp-idx | 37,504 | 110 | 0.00 | 0.00% | 11 |
| call-ldp-idx | 39,656 | 0 | 0.00 | 0.00% | 12 |
| cnone-ldp-idx-filt | 18,903 | 395 | 0.00 | 0.00% | 4 |
| cmatch-ldp-idx-filt | 37,509 | 109 | 0.00 | 0.00% | 11 |
| call-ldp-idx-filt | 39,683 | 0 | 0.00 | 0.00% | 12 |

<figcaption markdown="block">
Overview of the average execution times (ms), total number of HTTP requests, average number of results, correctness, and number of timeouts across all 12 **complex** queries.
</figcaption>
</figure>

Update complex results table once experiments are done
{:.todo}

In each table, make the rows with highest correctness bold.
{:.todo}

These results show that there are combinations of approaches that achieve a very high level of correctness for discover queries,
and an average level of correctness for short queries.
However, for complex queries, none of the combinations achieve an acceptable level of correctness.
Hence, we consider this last query category too complex for current link traversal approaches, and we do not consider them further in this article.

### Discussion

#### Intra-vault and inter-vault data discovery

The results above show that if we desire correct results,
that the combination of cMatch semantics together at least one of the data vault discovery methods is required.
This combination is needed because our workload contains queries that either target data within a single vault (e.g. D1),
or data spanning multiple data vaults (e.g. D8).
While the different data vault discovery methods are able to discover data *within* vaults,
the reachability of cMatch is required to discover data *across* multiple vaults.

Due to this, cNone (follow no links) is an ineffective replacement for cMatch (follow links matching query),
because link traversal across multiple vaults will not take place, which will lead to too few query results.
Concretely, for discover queries cNone can only achieve a completeness of 74.14% for discover queries and 28.57% for short queries,
compared to respectively 99.14% and 42.86% for cMatch.
However, for those queries that target a single vault, cNone can be used instead of cMatch without a loss of correctness,
leading to a lower number of HTTP requests and lower query execution times.

Since cAll leads to all links being followed, including those followed by cMatch,
it is theoretically a sufficient replacement for cMatch.
However, our results show that the number of links that are being followed becomes too high, which leads to timeouts for nearly all queries.

Our results show that solely using reachability semantics (cMatch or cAll) without a data discovery method is insufficient for discover queries,
where a completeness of only up to 12.50% or 19.38% can be achieved.
However, when looking at the short queries category, solely using reachability semantics appears to be sufficient,
with the number of HTTP requests and query execution time even being lower.
This difference exists because the discover category contains queries that discover data related to a certain person or resource,
while the short queries target only details of specific resources. 
Discover queries therefore depend on an overview of the vault, while short queries only depend on specific links between resources within a vault.

#### Type index discovery is more selective than LDP-based discovery

**Query execution times**

When comparing the number of HTTP requests and query execution times for different data vault discovery approaches under cMatch in [](#results-queries-discover),
we can observe that using the type index leads to fewer HTTP requests and faster query execution compared to LDP-based discovery.
To explain this behaviour in more detail, [](#figure-queries_indexvsstorage_time_relative) shows the average query execution times of each discover query separately,
for the different combinations of data vault discovery approaches.
To simplify comparability, the execution times within this figure are [relative to the maximum query execution time per query](#linktraversaloptimization).
The [appendix](#appendix-evaluation) contains more detailed query result arrival times for several of these queries.
Furthermore, [](#figure-queries_indexvsstorage_http) shows the average number of HTTP requests for each of those discover queries.

<figure id="figure-queries_indexvsstorage_time_relative">
<img src="img/experiments/queries_indexvsstorage_time_relative.svg" alt="Relative execution times of discover queries for index versus storage">
<figcaption markdown="block">
Relative query execution times for all discover queries with different combinations of data vault discovery with cMatch.
</figcaption>
</figure>

<figure id="figure-queries_indexvsstorage_http">
<img src="img/experiments/queries_indexvsstorage_http.svg" alt="HTTP requests of discover queries for index versus storage">
<figcaption markdown="block">
Average number of HTTP requests for all discover queries with different combinations of data vault discovery with cMatch.
</figcaption>
</figure>

Mention some percentages below?
{:.todo}

This figure shows that for six queries (D1, D2, D5, D6, D7, D8), using just the type index is faster or comparable to just LDP-based discovery.
When the filter-enabled type index approach is used, three queries (D1, D3, D8) are made even faster compared to the non-filtered type index approach.
This is because those queries target a possibly empty subset of the type index entries,
which means that a significant range of links can be pruned out,
which leads to a major reduction in the number of HTTP requests,
which is a main bottleneck in link traversal.

However, for two queries (D4, D5), the filter-enabled becomes slower than the non-filtered type index approach.
This is because those queries perform relatively fast across all approaches,
while the processing overhead of type index filtering becomes too high compared to its potential benefit.

These results show that using the type index together with LDP-based discovery is not beneficial in general,
which is primarily caused by the higher overall number of HTTP requests caused by traversing both the type index and nested LDP containers.
Query D8 that has a result limit of 10 does however show that this combination deserves further investigation,
because this query leads to a prioritization of selective links via the type index,
which allows the query to terminate earlier with fewer HTTP requests.

The execution times of D4 in [](#figure-queries_indexvsstorage_time_relative) are an outlier,
due to the very low absolute execution times (~1ms).

#### Zero-knowledge query planning is ineffective

<figure id="results-planning-effectiveness" class="table" markdown="1">

| Query | Traversal-based | Index-based | Dereferencing | HTTP Requests |
| --- | --: | --: | --: | --: |
| D1 | 5,826.83 | 451.42 | 88.73% | 222 |
| D2 | 6,043.50 | 646.77 | 61.80% | 223 |
| D3 | 7,490.17 | 1,118.69 | 83.53% | 428 |
| D4 | 5,734.25 | 509.56 | 81.67% | 228 |
| D5 | 1,499.62 | 365.76 | 79.89% | 222 |
| D8 | 4,739.88 | 2,292.81 | 92.51% | 217 |

<figcaption markdown="block">
TODO
</figcaption>
</figure>

- As shown above, http reqs reduced but not exec time. Because of query plan! (Go over heuristics planner to show why not works for solid?)
- Based on oracle emitting all links we need
- To measure query plan perf: comp with a query that first indexes data locally via construct (measure that time), and than proper query planning => downside: delays time until first result! (also not always possible for infinite link queues!)
	- Implement a link traversal actor that first traverses to find all triples, and then queries afterwards? Or just a simple hacked exp?
- Say that indexed is only theoretical, as collecting all links requires traversal querying. Also show that some queries would produce infinite stream of urls. We just need better planning. Also optimal case follows all links in parallel.
{:.todo}

#### Live exploration is required for heterogeneous fragmentations

Optional: if time left. (we could also just discuss this in conclusions if not time left)
Show that hardcoded data access to specific files is not good, because different vaults have different frag strategies.
Any findings by comparing different frag strategies?
{:.todo}