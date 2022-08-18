---
description: Indexer node architecture
---

# Architecture

![Indexer Node Architecture](<../.gitbook/assets/Screenshot 2022-08-01 at 10.28.38 PM.png>)

### **Datastore**

Datastore is an IPFS abstraction that defines an interface for persistence and retrieval of arbitrary bytes identified by a key. The datastore in an indexer node contains the ingestion state data.

### **ValueStore**

Valuestore refers to the storetheindex persistence layer that sores the entire state of storetheindex. The state typically includes mapping of multihashes to providers, as well as other internal information used by the underlying libraries.&#x20;

The ValueStore is essentially a concrete configuration for the abstract DataStore interface mentioned earlier.

### **Admin Server**

The Admin Server exposes a HTTP API to interact with a running storetheindex daemon, typically via the `storetheindex` CLI. The Admin Server is intended to be exposed locally only.

### **Query/Finder Server**

The Query Server eposes a HTTP API for looking up providers for a given a CID or multihash via GET`/cid/<cid>` and `/mh/<multihash>` endpoints respectively.

### **Ingestion Server**

The Ingestion Server is responsible for handling announcements from the index providers and process the published advertisements such that the advertised multihashes are queryable via the Query Server. The Ingestion Server supports two protocols: HTTP and Libp2p.
