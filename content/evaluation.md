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

- **Source selector strategy**: LDP, Type Index, Filtered Type Index, LDP and Type Index, LDP and Filtered Type Index
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

All results (table)
Say that we omit cNone and cAll (explain with numbers why)
{:.todo}

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