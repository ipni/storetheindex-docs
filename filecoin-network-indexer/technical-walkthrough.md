# Technical Walkthrough

### Overview

Filecoin stores a great deal of data, but without proper indexing of that data, clients cannot perform efficient retrieval. To improve Filecoin content discoverability, Indexer nodes are developed to store mappings of CID multi-hashes to content provider records. A client uses a CID or multihash to lookup a provider record, and then uses that provider record to retrieve data from a storage provider. In short, the indexer works like a specialized key-value store for the following two groups of users:

* **Storage providers** advertise their available content by storing data in the indexer. This is handled by the indexer’s _ingest_ logic.
* **Retrieval clients** query the indexer to find which storage providers have the content, and how to retrieve it (i.e. graphsync, bitswap, etc.). This is handled by the indexer’s _find_ logic.

In the post, we will look into how indexer components interact with each other, how are index ingested, stored, and shared across network.

### Indexer Interactions

Storage providers and retrieval clients are the two group of users that network indexer is designed for, how do they interact with indexer?

First, a storage deal is made and data is stored by a storage provider. Storage provider will announce that new content is available by publishing the CID of the advertisement record over gossip pubsub, generally relayed by mainnet nodes. Alternatively, it can be sent directly to indexer via HTTP. Indexer nodes can also relay to each other.

Then, Indexer would sync the new content from storage provider, by getting advertisements in the chain up to last already seen or end of the chain. It would also get contextID, metadata, and content multi-hash chunk chain for each new advertisement.

Once the content is indexed, retrieval client can find provider data using the CID or multi-hash of the data that they want to retrieve for. Indexer would respond with list of provider records for each CID looked up, along with the latest provider addresses.

Client then retrieves content from storage provider using the protocol indicated in the provider record (e.g. graphsync or bitswap). Client would send the provider record to storage provider, who uses contextID and metadata to find the content.

This diagram summarizes the different actors in indexer ecosystem, and how they interact with each other:

![https://github.com/filecoin-project/storetheindex/blob/main/doc/indexer\_ecosys.png?raw=true](https://github.com/filecoin-project/storetheindex/blob/main/doc/indexer\_ecosys.png?raw=true)

### Index Ingestion

Index ingestion consists of two parts:

1. **Publish** - Announcing the availability of index advertisements by publishing an announce message to indexer(s).
2. **Sync** - Fetching index data from the publisher of that data. Synchronizes the indexer with the published index data.

Announce Message informs indexers about the availability of an advertisement. It is sent from publisher to indexer, via gossip pubsub (in most cases), or via HTTP. Announcement message contains the CID of the advertisement, and the publisher’s address (where to retrieve the advertisement from). Indexer ignores the announce if it has already indexed the advertisement.

Advertisements are processed in order, and are signed (including links) to create a blockchain-like structure. It must be signed by provider or allowed publisher.

In advertisement, _ContextID_ uniquely identifies a specific metadata for a provider. A ContextID is used to update metadata, add multi-hash mappings to a provider record, or delete a provider record and multi-hash mappings to it. _Metadata_ contains protocol identifier plus additional data that is forwarded to storage provider when retrieving data. The SP uses the metadata to find the data to retrieve, it may contain a deal ID, internal record keys, etc.

The following diagram illustrates the structure of advertisements and multihash data:

![https://github.com/filecoin-project/storetheindex/blob/main/doc/index-ad-ipld-graph.png?raw=true](https://github.com/filecoin-project/storetheindex/blob/main/doc/index-ad-ipld-graph.png?raw=true)

### Data Storage

In Indexer data store design, many multihashes map to relatively few provider records. A multihash is used to lookup the provider records describing where the content identified by that multihash is available. A provider ID and context ID are used to lookup a unique provider record.

Provider data can be updated and removed independently from the multihashes that map to it. Provider data is uniquely identified by a provider ID and a context ID. The context ID is given to the indexer core as part of the provider data value. When a provider data object is updated, all subsequent multihash queries will return the new value for that data.

Unique provider records are looked up by a provider key, which is a hash of the provider ID and the record's context ID. A multihash can map to multiple provider records, since the same content may be stored by different provider and be part of separate deals at the same provider. The indexer core maps each multihash to a list of provider keys, and maps each provider key to a provider record.

This diagram shows the 2-level mapping design of indexer data store:

![https://github.com/filecoin-project/go-indexer-core/raw/main/doc/indexer-core-data.png](https://github.com/filecoin-project/go-indexer-core/raw/main/doc/indexer-core-data.png)

### Data Sharing

Additionally, Indexers can share various types of data with each other, when configured to do so:

* Indexers can discover providers and publishers from other indexers.
* Indexers can re-publish HTTP announcements to other indexers.
* Indexers can relay gossip pubsub announcements.

### Resources

If you are interested in participating in Indexer network or learning more about Indexers, you may find these resources helpful:

* [cid.contact](http://cid.contact) (content routing homepage)
* [docs.cid.contact](http://docs.cid.contact) (Indexer gitbook)
* [storetheindex](https://filecoinproject.slack.com/archives/C02T827T9N0) (Filecoin slack channel)
* [Indexer Implementation](https://github.com/filecoin-project/storetheindex)
* [Valuestore Implementation](https://github.com/ipld/go-storethehash)
* [Library for Indexer Synchronization](https://github.com/filecoin-project/go-legs)
