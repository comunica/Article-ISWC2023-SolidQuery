## Solid Ecosystem Data Examples
{:#appendix-listings}

[](#example-ldpcontainer) shows an example of such an LDP basic container in a data vault that contains one file and two directories.

<figure id="example-ldpcontainer" class="listing">
````/code/ldpcontainer.ttl````
<figcaption markdown="block">
An LDP container in a Solid data vault containing one file and two directories in the Turtle serialization.
</figcaption>
</figure>

An example of a WebID profile is shown in [](#example-webidprofile).

<figure id="example-webidprofile" class="listing">
````/code/webidprofile.ttl````
<figcaption markdown="block">
A simplified WebID profile in Turtle.
</figcaption>
</figure>

[](#example-typeindex) shows a type index with type registrations for posts and comments,
where the posts entry refers to a single posts file,
and the comments entry refers to a container with multiple comments files.
If an application wants to obtain all posts of a user,
it can do so by finding this type index and following the link within the type index entry that corresponds to the post class.

<figure id="example-typeindex" class="listing">
````/code/typeindex.ttl````
<figcaption markdown="block">
Example of a type index with entries for posts and comments in the Turtle serialization.
</figcaption>
</figure>
