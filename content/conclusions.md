## Conclusions
{:#conclusions}

- We showed that LTQP is effective for querying over pods (and what its limitations are) using a new benchmark, but extensions are needed that make use of assumptions
    - Our results show that link traversal behaves quite different under these assumptions
    - Say termination and completeness problems are solved!(?)
        - From Executing SPARQL Queries ... "Due to the openness and the widely distributed nature of the Web we cannot assume to find all data that is relevant to answer a query with our approach. Hence, we should never expect complete results." -> This is not true anymore for Solid, since we can guarantee completeness within a pod!
    - LDQL not needed, because type idx and ldp give us navigational things (this is not something the developer should have to express, because this is different in different pods!)
    - The ability to close the world around solid pods create query opt opoortunities => may also be applied for other decentralized environments (mastodon?)
    - Say that without a map (conclusions) says that fundamental problem in LTQP is finding data as early as possible, but we show that this is solved for solid, and that main problem here is query planning!
- Concretely for Solid, more improvements are needed for complex queries
    - Need for adaptive query planning! (mention eddy-like adaptive operation of the link queue (walking without a map - section 3), but it doesn't consider adaptivity of the query plan) (also mentioned in linkeddatamanagement)
        - Decomposing parts of the query plan for different parts of the type index also requires adaptive query planning
    - Better combination of LDP+idx needed, where pruning of idx can help LDP better.
    - Need for better query containment for type index
    - Practical problem with type index: Say that type index is optional, so it can not be used on its own, as completeness can not be guaranteed.
        - Mention shape trees as alternative to type indexes
- Argue that this speed is enough for apps, esp since results arrive itertively
    - Future work: heterogeneous interfaces
    - Future work: better q planning will solve compl for other query categories
{:.todo}





Optional:
- Best termination condition is a LIMIT in the query? (but incompat with ORDER BY...)
- Mention need for new datastructures (see ideas note)
{:.todo}
