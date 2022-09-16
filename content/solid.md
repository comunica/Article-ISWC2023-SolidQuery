## The Solid Ecosystem
{:#solid}

In this section, we provide an analysis of the structural properties within the Solid ecosystem
that are relevant for query execution.
We first start by explaining the concept of data vaults and its implications on applications.
Next, we explain the WebID, which is used for identifying users.
Then, we discuss the Solid type index that improves data discovery.
Finally, we list requirements for enabling query execution within the Solid ecosystem.

### Data Vault

The primary element within the [Solid protocol](cite:cites spec:solidprotocol) is
the data vault (also known as *data pod*), is a user-owned space in which any kind of data can be stored.
Users can choose where and how their vault is stored,
by either [hosting it themselves](cite:cites solidcommunityserver),
or by obtaining service-provided space by [a company](cite:cites inruptpodspaces) or [government](cite:cites flandersdatautility).
Data vaults are loosely coupled to applications,
where applications must request explicit access to the user for interacting with specific data.
This loose coupling enables different applications to make use of the same data in an interoperable manner.

Currently, data vaults are primarily file-based, and are exposed on the Web as a REST API
using elements of the [Linked Data Platform (LDP) specification](spec:ldp).
Directories are represented using *LDP Basic Containers*,
which can contain any number of resources that correspond to RDF or non-RDF documents,
or other nested basic containers.
For the rest of this article, we will only consider the processing RDF documents within vaults.
Resources within vaults can be read by sending HTTP GET requests to their URLs,
with optional content negotiation to return the documents in different RDF serializations.
If the vault supports this, resources can be modified or created using HTTP PATCH and POST requests.
[](#example-ldpcontainer) shows an example of such a basic container in a data vault that contains one file and two directories.

<figure id="example-ldpcontainer" class="listing">
````/code/ldpcontainer.ttl````
<figcaption markdown="block">
Example in the Turtle serialization of a LDP container in a Solid data vault containing one file and two directories.
</figcaption>
</figure>

Data vaults are meant to contain not only public data, but also private data.
Therefore, users can configure who can access or modify files within their vault in a fine-grained manner
using techniques such as [ACL](cite:cites spec:wac) and [ACP](spec:acp).
This configuration is usually done by referring to the *WebID* of users,
which will be elaborated upon in the next section.

### WebID Profile

Any agent (person or organization) within the Solid ecosystem can establish their identity by a URI, called a WebID.
These agents can authenticate themselves using the [Solid OIDC protocol](cite:cites spec:solidoidc),
which is required for authorization during reading and writing of resources behind access control.
The Solid OIDC protocol is fully decentralized, which means that anyone is free to choose or set up their own identity provider.

According to the [WebID profile specification](cite:cites spec:webidprofile),
each WebID URI should be dereferenceable, and return a WebID profile document.
Next to basic information of the agent such as its name and contact details,
this document should contain several links:

- Identity provider to authenticate the WebID.
- Root LDP container of its data vault.
- Public and private type indexes.

A WebID profile is allowed to be split up into multiple document,
which are referred to each other using the `rdfs:seeAlso` predicate.
There are more WebID profiles elements, but we omit these due to their irrelevance in this work,
such as the link to the preferences file and notifications inbox.
[](#example-webidprofile) contains a simplified example of such a WebID profile.

<figure id="example-webidprofile" class="listing">
````/code/webidprofile.ttl````
<figcaption markdown="block">
Simplified example of a WebID profile in the Turtle serialization.
</figcaption>
</figure>

### Type Index

The [Type Index](cite:cites spec:typeindex) is a document that enables type-based resource discovery within a vault.
Users may have public or private type indexes, which respectively refer to data that are and are not publicly discoverable.
A type index can contain type registration entries for different classes,
where each registration has a link to resources containing instances of the corresponding class.
For example, [](#example-typeindex) shows a type index with type registrations for posts and comments,
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

### Requirements for query engines

Instead of requiring all application developers to reinvent the wheel by manually discovering application-relevant elements within data vaults,
this can be abstracted away behind declarative queries that are to be executed by reusable query engines.
This also makes applications robust against changes or additions within the Solid protocol,
where this only requires changes within the underlying query engine,
as the application's declarative query can remain unchanged.
As such, for the remainder of this article, we consider query engines taking up the task of finding data within and across data vaults.

The [Solid protocol](cite:cites spec:solidprotocol) only establishes a minimal set of ground-rules to make data vaults and applications interoperable.
Below, we list the requirements that query agents have if they want to provide query execution over one or more data vaults.

Figure of all relevant Solid elements and query as abstraction layer?
{:.todo}

1. **Mapping query to a sequence of HTTP requests**:
The query engine MUST have the ability to convert a declarative query into a sequence of one or more HTTP requests within or across data vaults.

2. **Discovery and usage of LDP storage**:
When given a link to a WebID profile, the query engine MUST be able discover and follow the `pim:storage` link to the storage root of the vault.
Additionally, the query engine MUST be able to identify an LDP basic container, and follow (a subset of) links towards the resources within this container.

3. **Discovery and usage of type indexes**:
The query engine MUST be able to discover and follow type index links from the WebID profile, and handle (a subset of) the type registration links.

4. **Variability of vault structures**:
The query engine SHOULD NOT make assumptions about the location of certain data within specific vaults
without having an explicit and discoverable link path to it, e.g. via LDP storage or type indexes.

5. **Authenticated requests**:
To enable queries over private resources, the agent SHOULD be able to authenticate itself to the query engine using its WebID. The query engine can then use the authenticated session on behalf of the user to perform authorized HTTP requests over private resources.

While these requirements apply to both read and write queries,
we will focus on read-only queries for the remainder of this article.
Furthermore, given the Linked Data nature of Solid,
we will focus on queries using the [SPARQL query language](cite:cites spec:sparqllang) for the remainder of this article.
Nevertheless, these concepts can also be applied to write queries and other query languages.
