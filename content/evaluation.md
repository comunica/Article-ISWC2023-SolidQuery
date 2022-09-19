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
| cnone-base | 38 | 8 | 0.00 | 0.00% | 0 |
| cmatch-base | 4,417 | 425 | 1.25 | 12.50% | 0 |
| call-base | 83,084 | 450 | 2.63 | 19.38% | 7 |
| cnone-ldp | 2,361 | 342 | 20.50 | 74.14% | 0 |
| cmatch-ldp | 19,141 | 825 | 39.13 | 99.14% | 0 |
| call-ldp | 82,887 | 449 | 2.88 | 20.63% | 7 |
| cnone-ldp-idx | 2,814 | 406 | 20.50 | 74.14% | 0 |
| cmatch-ldp-idx | 17,101 | 763 | 39.13 | 99.14% | 0 |
| call-ldp-idx | 79,937 | 466 | 3.50 | 23.75% | 7 |
| cnone-ldp-idx-filt | 2,466 | 400 | 20.50 | 74.14% | 0 |
| cmatch-ldp-idx-filt | 17,199 | 762 | 39.13 | 99.14% | 0 |
| call-ldp-idx-filt | 80,307 | 458 | 3.63 | 24.38% | 7 |

<figcaption markdown="block">
Overview of the average execution times (ms), total number of HTTP requests, average number of results, correctness, and number of timeouts across all 8 **discover** queries.
</figcaption>
</figure>

<figure id="results-queries-short" class="table" markdown="1">

| Experiment | Time | Requests | Results | Correctness | Timeouts |
| --- | ---: | ---: | ---: | ---: | ---: |
| cnone-base | 74 | 15 | 0.14 | 14.29% | 0 |
| cmatch-base | 27,122 | 596 | 0.43 | 42.86% | 2 |
| call-base | 119,300 | 0 | 0.29 | 28.57% | 7 |
| cnone-ldp | 1,933 | 126 | 1.57 | 28.57% | 0 |
| cmatch-ldp | 69,765 | 291 | 0.43 | 42.86% | 4 |
| call-ldp | 119,335 | 0 | 0.29 | 28.57% | 7 |
| cnone-ldp-idx | 2,816 | 164 | 1.57 | 28.57% | 0 |
| cmatch-ldp-idx | 69,084 | 307 | 0.43 | 42.86% | 4 |
| call-ldp-idx | 119,419 | 0 | 0.29 | 28.57% | 7 |
| cnone-ldp-idx-filt | 2,170 | 161 | 1.57 | 28.57% | 0 |
| cmatch-ldp-idx-filt | 69,600 | 305 | 0.43 | 42.86% | 4 |
| call-ldp-idx-filt | 119,287 | 0 | 0.29 | 28.57% | 7 |

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

#### Type index is more selective than LDP container traversal

Show that for queries where type index filtering applies, pim:storage is not necessary, and less links will have to be followed. Because type index is more selective. (pim:storage is a form of index as well! => use as fallback)
This requires us to make an experiment with pim:storage disabled but type idx not
Say that type index is optional, so it can not be used on its own, as completeness can not be guaranteed
{:.todo}

Prove this with discover queries comparing just cMatch with cMatch+pim:storage, cMatch+typeidx, cMatch+pim:storage+typeidx
{:.todo}

#### Zero-knowledge query planning is ineffective

- To measure query plan perf: comp with a query that first indexes data locally via construct (measure that time), and than proper query planning => downside: delays time until first result!
	- Implement a link traversal actor that first traverses to find all triples, and then queries afterwards? Or just a simple hacked exp?
- Say that indexed is only theoretical, as collecting all links requires traversal querying. Also show that some queries would produce infinite stream of urls. We just need better planning. Also optimal case follows all links in parallel.
{:.todo}

#### Live exploration is required for heterogeneous fragmentations

Optional: if time left. (we could also just discuss this in conclusions if not time left)
Show that hardcoded data access to specific files is not good, because different vaults have different frag strategies.
Any findings by comparing different frag strategies?
{:.todo}