## Evaluation
{:#evaluation}

Separate sections with smaller exps with different findings
{:.todo}

Research question: How well does link traversal query processing perform over decentralized environments with structural properties

### Solely reachability semantics are insufficient

Also show cNone and cAll briefly, but those are either incorrect or too slow, and say we don't consider them further in this work.
{:.todo}

Prove this with discover queries comparing just cMatch with cMatch+pim:storage, cMatch+typeidx, cMatch+pim:storage+typeidx
{:.todo}

But, cMatch is necessary for inter-pod traversal, while LDP and type index can only used for intra-pod querying. (show with a couple of queries and their correctness)
{:.todo}

### Type index is more selective than LDP container traversal

Show that for queries where type index filtering applies, pim:storage is not necessary, and less links will have to be followed. Because type index is more selective. (pim:storage is a form of index as well! => use as fallback)
This requires us to make an experiment with pim:storage disabled but type idx not
Say that type index is optional, so it can not be used on its own, as completeness can not be guaranteed
{:.todo}

### Zero-knowledge query planning is ineffective

- To measure query plan perf: comp with a query that first indexes data locally via construct (measure that time), and than proper query planning => downside: delays time until first result!
	- Implement a link traversal actor that first traverses to find all triples, and then queries afterwards? Or just a simple hacked exp?
- Say that indexed is only theoretical, as collecting all links requires traversal querying. Also show that some queries would produce infinite stream of urls. We just need better planning. Also optimal case follows all links in parallel.
{:.todo}

### Live exploration is required for heterogeneous fragmentations

Optional: if time left. (we could also just discuss this in conclusions if not time left)
Show that hardcoded data access to specific files is not good, because different vaults have different frag strategies.
Any findings by comparing different frag strategies?
{:.todo}