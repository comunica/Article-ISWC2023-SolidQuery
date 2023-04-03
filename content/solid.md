## The Solid Ecosystem
{:#solid}

In this section, we provide an analysis of the structural properties within the Solid ecosystem
that are relevant for query processing.
We start by explaining the concept of data vaults and its implications on applications.
Next, we explain the WebID, which is used for identifying users.
Finally, we discuss the Solid type index; a structural property that improves data discovery.

### Data Vault

A primary element within the [Solid Protocol](cite:cites spec:solidprotocol) is
the data vault (also known as *data pod*), which is a user-controlled space in which any kind of data can be stored.
Users can choose where and how their vault is stored on the Web,
by [hosting it themselves](cite:cites solidcommunityserver),
obtaining service-provided space by [a company](cite:cites inruptpodspaces) or [government](cite:cites flandersdatautility).
Data vaults are intended to be loosely coupled to applications,
and applications must request explicit access to the user for interacting with specific data.
This loose coupling enables different applications to use the same data interoperably.

Current data vaults are primarily document-oriented, and are exposed on the Web as a REST API
using elements of the [Linked Data Platform (LDP) specification](cite:cites spec:ldp).
Directories are represented using *LDP Basic Containers*,
which can contain any number of resources that correspond to RDF or non-RDF documents (via `ldp:contains` links),
or other nested basic containers.
For the remainder of this article, we will only consider the processing of RDF documents within vaults.
Resources within vaults can be read by sending HTTP `GET` requests to their URLs,
with optional content negotiation to return the documents in different RDF serializations.
Vaults may also support creation and modification using HTTP `PATCH` and `POST` requests.
An example of such a basic container can be found in the [](#example-ldpcontainer).

<figure id="example-ldpcontainer" class="listing">
````/code/ldpcontainer.ttl````
<figcaption markdown="block">
An LDP container in a Solid data vault containing one file and two directories in the Turtle serialization.
</figcaption>
</figure>

Data vaults can contain public as well as private data.
Users can configure who can access or modify files within their vault
using mechanisms such as [ACL](cite:cites spec:wac) and [ACP](cite:cites spec:acp).
This configuration is usually done by referring to the *WebID* of users.

### WebID Profile

<figure id="example-webidprofile" class="listing">
````/code/webidprofile.ttl````
<figcaption markdown="block">
A simplified WebID profile in Turtle.
</figcaption>
</figure>

Any agent (person or organization) within the Solid ecosystem can establish their identity through a URI, called a *WebID*.
These agents can authenticate themselves using the decentralized [Solid OIDC protocol](cite:cites spec:solidoidc),
which is required for authorizing access during the reading and writing of resources.
According to the [WebID profile specification](cite:cites spec:webidprofile),
each WebID URI should be dereferenceable, and return a WebID profile document.
Next to basic information of the agent such as its name,
this document contains links to
1) the vault's LDP container (via `pim:storage`), and
2) public and private type indexes.
An example is shown in [](#example-webidprofile).

### Type Index

The [Type Index](cite:cites spec:typeindex) is a document that enables type-based resource discovery within a vault.
Users may have public or private type indexes, which respectively refer to data that are and are not publicly discoverable.
A type index can contain type registration entries for different classes,
where each registration has a link to resources containing instances of the corresponding class.
[](#example-typeindex) shows a type index example with type registrations for posts and comments,
where the posts entry refers to a single posts file,
and the comments entry refers to a container with multiple comments files.
If an application wants to obtain all posts of a user,
it can follow the link within the type index entry corresponding to the post class.

<figure id="example-typeindex" class="listing">
````/code/typeindex.ttl````
<figcaption markdown="block">
Example of a type index with entries for posts and comments in Turtle.
</figcaption>
</figure>
