## Conclusions
{:#conclusions}

- Our results show that link traversal behaves quite different under these assumptions
- Say termination and completeness problems are solved!(?)
- The ability to close the world around solid pods create query opt opoortunities
- Best termination condition is a LIMIT in the query? (but incompat with ORDER BY...)
- Mention need for new datastructures (see ideas note)
- Need for adaptive query planning! (mention eddy-like adaptive operation of the link queue (walking without a map - section 3), but it doesn't consider adaptivity of the query plan)
- Important finding: walking without a map (conclusions) says that fundamental problem in LTQP is finding data as early as possible, but we show that this is solved for solid, and that main problem here is query planning!
- LDQL not needed, because type idx and ldp give us navigational things (this is not something the developer should have to express, because this is different in different pods!)
- Need for better query containment for type index
- From Executing SPARQL Queries ... "Due to the openness and the widely distributed nature of the Web we cannot assume to find all data that is relevant to answer a query with our approach. Hence, we should never expect complete results." -> This is not true anymore for Solid, since we can guarantee completeness within a pod!
{:.todo}
