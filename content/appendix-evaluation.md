## Experimental Results
{:#appendix-evaluation}

[](#figure-querytimes_d1-3) and [](#figure-querytimes_d2-3) show the query result arrival times for D1 and D2 respectively
with different combinations of data vault discovery with cMatch.
Since D1 specifically queries for resources of the type Post, it can very selectively make use of the Post entry within the type index,
which makes the filtered type index approach faster than the non-filtered approach.
D2 targets both resources of type Comment and Post, which means that it has to make use of both entries within the type index,
which causes the performance difference between the filtered and non-filtered type index approach to be negligeable.

[](#figure-querytimes_d5-4) shows the query result arrival times for D5,
which is a query that does not specifically target resources of certain types.
This means that the type index leads to no significant performance benefit.

<figure id="figure-querytimes_d1-3">
<img src="img/experiments/querytimes_d1-3.svg" alt="Query result arrival times for D1">
<figcaption markdown="block">
Query result arrival times for D1 with different combinations of data vault discovery with cMatch.
</figcaption>
</figure>

<figure id="figure-querytimes_d2-3">
<img src="img/experiments/querytimes_d2-3.svg" alt="Query result arrival times for D2">
<figcaption markdown="block">
Query result arrival times for D2 with different combinations of data vault discovery with cMatch.
</figcaption>
</figure>

<figure id="figure-querytimes_d5-4">
<img src="img/experiments/querytimes_d5-4.svg" alt="Query result arrival times for D5">
<figcaption markdown="block">
Query result arrival times for D5 with different combinations of data vault discovery with cMatch.
</figcaption>
</figure>