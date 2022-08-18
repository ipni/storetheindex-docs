---
description: What is filecoin network indexer?
---

# Overview

## Overview

An indexer is a network node that stores mapping of content multihahses to provider data records. A client that wants to know where a piece of information is stored can query the indexer, using the CID or multihash of the content, and receive a provider record that tells where the client can retrieve the content and how to retrieve it.

## Indexer Ecosystem

![Indexer ecosystem](.gitbook/assets/indexer\_ecosys.png)

## Why Network Indexer

Filecoin stores a great deal of data, but without proper indexing of that data, clients cannot perform efficient retrieval. To improve Filecoin content discoverability, Indexer nodes are developed to store mappings of CIDs to content providers for content lookup upon retrieval request.

To provide index to indexer node, there is an [Indexer Provider](https://lotus.filecoin.io/storage-providers/operate/index-provider/) service that runs alongside with Storage Provider, which tells the Indexer what contents this storage provider has. Index Provider sends updates to the Indexer via a series of [Advertisement](running-indexer-node/architecture.md#advertisement) messages. Each message references a previous advertisement so that as a whole it forms an advertisement chain. The state of the indexer is a function of consuming this chain from the initial Advertisement to the latest one, and delta of indexer content is updated periodically.

Indexer node enables content discovery within Filecoin network today, it is also a fundamental component toward the end goal of universal retrieval across IPFS/Filecoin, i.e. to be able to efficiently retrieve Filecoin content from IPFS. Combining Indexer work for content discovery with IPFS/Filecoin interoperability work for content transfer, we aim to reach the end state of efficient and universal retrieval across IPFS and Filecoin network.
