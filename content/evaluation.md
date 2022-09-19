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

### Experimental Results

In this section, we present only those results that offer insights into the research question.
[](#results-queries-discover), [](#results-queries-short), and [](#results-queries-complex)
show the aggregated results for the different combinations of our setup
for the discover, short, and complex queries of the benchmark, respectively.
Within these tables, the execution time, number of actual results, and correctness are averaged across all executed queries.
The timeout column represents the total number of timeouts that took across all queries for a given combination.
The correctness of each query execution is calculated as the percentage of expected results that were actually produced.

<figure id="results-queries-discover" class="table" markdown="1">

| Experiment | Time | Results | Correctness | Timeouts |
| --- | ---: | ---: | ---: | ---: |
| cnone-base | 38.23 | 0.00 | 0.00% | 0 |
| cmatch-base | 4,416.88 | 1.25 | 12.50% | 0 |
| call-base | 83,083.68 | 2.63 | 19.38% | 7 |
| cnone-ldp | 2,360.75 | 20.50 | 74.14% | 0 |
| cmatch-ldp | 19,141.25 | 39.13 | 99.14% | 0 |
| call-ldp | 82,887.43 | 2.88 | 20.63% | 7 |
| cnone-ldp-idx | 2,813.55 | 20.50 | 74.14% | 0 |
| cmatch-ldp-idx | 17,100.58 | 39.13 | 99.14% | 0 |
| call-ldp-idx | 79,936.70 | 3.50 | 23.75% | 7 |
| cnone-ldp-idx-filt | 2,465.63 | 20.50 | 74.14% | 0 |
| cmatch-ldp-idx-filt | 17,199.13 | 39.13 | 99.14% | 0 |
| call-ldp-idx-filt | 80,306.60 | 3.63 | 24.38% | 7 |

<figcaption markdown="block">
Overview of the average execution times (ms), average number of results, correctness, and number of timeouts across all **discover** queries.
</figcaption>
</figure>

<figure id="results-queries-short" class="table" markdown="1">

| Experiment | Time | Results | Correctness | Timeouts |
| --- | ---: | ---: | ---: | ---: |
| cnone-base | 74.09 | 0.14 | 14.29% | 0 |
| cmatch-base | 27,121.51 | 0.43 | 42.86% | 2 |
| call-base | 119,300.34 | 0.29 | 28.57% | 7 |
| cnone-ldp | 1,933.29 | 1.57 | 28.57% | 0 |
| cmatch-ldp | 69,764.71 | 0.43 | 42.86% | 4 |
| call-ldp | 119,335.40 | 0.29 | 28.57% | 7 |
| cnone-ldp-idx | 2,816.43 | 1.57 | 28.57% | 0 |
| cmatch-ldp-idx | 69,084.46 | 0.43 | 42.86% | 4 |
| call-ldp-idx | 119,419.09 | 0.29 | 28.57% | 7 |
| cnone-ldp-idx-filt | 2,170.37 | 1.57 | 28.57% | 0 |
| cmatch-ldp-idx-filt | 69,599.86 | 0.43 | 42.86% | 4 |
| call-ldp-idx-filt | 119,286.77 | 0.29 | 28.57% | 7 |

<figcaption markdown="block">
Overview of the average execution times (ms), average number of results, correctness, and number of timeouts across all **short** queries.
</figcaption>
</figure>

<figure id="results-queries-complex" class="table" markdown="1">

| Experiment | Time | Results | Correctness | Timeouts |
| --- | ---: | ---: | ---: | ---: |
| cnone-base | 12,875.77 | 0.00 | 0.00% | 1 |
| cmatch-base | 16,692.83 | 0.50 | 2.50% | 4 |
| call-base | 39,907.18 | 0.00 | 0.00% | 12 |
| cnone-ldp | 19,071.62 | 0.00 | 0.00% | 4 |
| cmatch-ldp | 38,281.22 | 0.00 | 0.00% | 11 |
| call-ldp | 40,022.23 | 0.00 | 0.00% | 12 |
| cnone-ldp-idx | 19,889.92 | 0.00 | 0.00% | 4 |
| cmatch-ldp-idx | 37,504.05 | 0.00 | 0.00% | 11 |
| call-ldp-idx | 39,655.95 | 0.00 | 0.00% | 12 |
| cnone-ldp-idx-filt | 18,902.70 | 0.00 | 0.00% | 4 |
| cmatch-ldp-idx-filt | 37,508.85 | 0.00 | 0.00% | 11 |
| call-ldp-idx-filt | 39,682.78 | 0.00 | 0.00% | 12 |

<figcaption markdown="block">
Overview of the average execution times (ms), average number of results, correctness, and number of timeouts across all **complex** queries.
</figcaption>
</figure>

### Discussion

#### Solely reachability semantics are insufficient

Also show cNone and cAll briefly, but those are either incorrect or too slow, and say we don't consider them further in this work.
{:.todo}

Prove this with discover queries comparing just cMatch with cMatch+pim:storage, cMatch+typeidx, cMatch+pim:storage+typeidx
{:.todo}

But, cMatch is necessary for inter-pod traversal, while LDP and type index can only used for intra-pod querying. (show with a couple of queries and their correctness)
{:.todo}

#### Type index is more selective than LDP container traversal

Show that for queries where type index filtering applies, pim:storage is not necessary, and less links will have to be followed. Because type index is more selective. (pim:storage is a form of index as well! => use as fallback)
This requires us to make an experiment with pim:storage disabled but type idx not
Say that type index is optional, so it can not be used on its own, as completeness can not be guaranteed
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