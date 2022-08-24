## Evaluation
{:#evaluation}

Separate sections with smaller exps with different findings
{:.todo}

Research question: How well does link traversal query processing perform over decentralized environments with structural properties

### Live exploration is required for heterogeneous fragmentations

Show that hardcoded data access to specific files is not good, because different vaults have different frag strategies.
Any findings by comparing different frag strategies?
{:.todo}

### Structural properties move bottleneck from number of links to query plan

Compare duration of planning with query exec for discover queries and link following
{:.todo}

### Structural properties and cMatch are both necessary

Prove this with discover queries comparing just cMatch with cMatch+LDP:contains
{:.todo}

### Some discovery methods are preferred over others

Show that for queries where type index filtering applies, pim:storage is not necessary, and less links will have to be followed. Because type index is more selective. (pim:storage is a form of index as well! => use as fallback)
This requires us to make an experiment with pim:storage disabled but type idx not
{:.todo}

### Intra- versus intra-vault querying

Type index and other struct props are good for intra-pod querying, but for inter-vault querying, we also need cMatch, as we don't have cross-vault indexes (yet!).
{:.todo}

### Zero-knowledge query planning is ineffective

- To measure query plan perf: comp with a query that first indexes data locally via construct, and than proper query planning => downside: delays time until first result!
	- Implement a link traversal actor that first traverses to find all triples, and then queries afterwards? Or just a simple hacked exp?
{:.todo}
