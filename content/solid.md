## The Solid Ecosystem
{:#solid}

In this section, we provide an analysis of the structural properties within the Solid ecosystem
that are relevant for query execution.
We first start by explaining the concept of data vaults and its implications on applications.
Next, we explain the WebID, which is used for identifying users.
Then, we discuss the Solid type index that improves data discovery.
Finally, we list requirements for enabling query execution within the Solid ecosystem.

### Data Vault

A primary element within the [Solid protocol](cite:cites spec:solidprotocol) is
the data vault (also known as *data pod*), which is a user-owned space in which any kind of data can be stored.
Users can choose where and how their vault is stored on the Web,
by either [hosting it themselves](cite:cites solidcommunityserver),
or by obtaining service-provided space by [a company](cite:cites inruptpodspaces) or [government](cite:cites flandersdatautility).
Data vaults are loosely coupled to applications,
where applications must request explicit access to the user for interacting with specific data.
This loose coupling enables different applications to use the same data interoperably.

Currently, data vaults are primarily file-based, and are exposed on the Web as a REST API
using elements of the [Linked Data Platform (LDP) specification](spec:ldp).
Directories are represented using *LDP Basic Containers*,
which can contain any number of resources that correspond to RDF or non-RDF documents,
or other nested basic containers.
For the remainder of this article, we will only consider the processing RDF documents within vaults.
Resources within vaults can be read by sending HTTP GET requests to their URLs,
with optional content negotiation to return the documents in different RDF serializations.
If the vault supports this, resources can be modified or created using HTTP PATCH and POST requests.
An example of such a basic container can be found in the [appendix](#appendix-listings).

Data vaults can contain not only public data, but also private data.
Therefore, users can configure who can access or modify files within their vault
using techniques such as [ACL](cite:cites spec:wac) and [ACP](cite:cites spec:acp).
This configuration is usually done by referring to the *WebID* of users,
which will be elaborated upon in the next section.

### WebID Profile

Any agent (person or organization) within the Solid ecosystem can establish their identity through a URI, called a *WebID*.
These agents can authenticate themselves using the decentralized [Solid OIDC protocol](cite:cites spec:solidoidc),
which is required for authorizing access during the reading and writing of resources.

According to the [WebID profile specification](cite:cites spec:webidprofile),
each WebID URI should be dereferenceable, and return a WebID profile document.
Next to basic information of the agent such as its name and contact details,
this document should contain links to 1) the root LDP container of its data vault, and
2) public and private type indexes.
An example is shown in [appendix](#appendix-listings).
We omit further details on WebID profiles due to their irrelevance within this work.

### Type Index

The [Type Index](cite:cites spec:typeindex) is a document that enables type-based resource discovery within a vault.
Users may have public or private type indexes, which respectively refer to data that are and are not publicly discoverable.
A type index can contain type registration entries for different classes,
where each registration has a link to resources containing instances of the corresponding class.
An example of such a type index can be found in the [appendix](#appendix-listings).

### Requirements for query engines

Instead of requiring all application developers to reinvent the wheel by manually discovering application-relevant elements within data vaults,
this can be abstracted away behind declarative queries.
This also makes applications robust against changes or additions within the Solid protocol,
where this only requires changes within the underlying query engine,
as the application's declarative query can remain unchanged.
As such, we consider query engines for finding data within and across data vaults.

The [Solid protocol](cite:cites spec:solidprotocol) only establishes a minimal set of ground-rules to make data vaults and applications interoperable.
Below, we list the requirements that query agents need to have for enabling query execution over data vaults.

1. **Mapping query to a sequence of HTTP requests**:
Convert a query into a sequence of HTTP requests across data vaults.

2. **Discovery and usage of LDP storage**:
Discover and follow the `pim:storage` link in a WebID profile to the storage root of the vault,
identify an LDP basic container, and follow (a subset of) links towards the resources within this container.

3. **Discovery and usage of type indexes**:
Discover and follow type index links from the WebID profile, and handle (a subset of) the type registration links.

4. **Variability of vault structures**:
Make no assumptions about the location of certain data within vaults
without an explicit and discoverable link path to it, e.g. via LDP storage or type indexes.
This is important for the interoperability of Solid apps because different apps or user preferences
may lead to the storage of similar data in different locations within vaults.

5. **Authenticated requests**:
Perform authenticated HTTP requests on behalf of the user after authentication with a WebID, to enable querying over private resources.

While these requirements apply to both read and write queries,
we will focus on read-only queries for the remainder of this article.
Furthermore, given the Linked Data nature of Solid,
we will focus on queries using the [SPARQL query language](cite:cites spec:sparqllang) for the remainder of this article.
Nevertheless, these concepts can also be applied to write-queries and other query languages.
