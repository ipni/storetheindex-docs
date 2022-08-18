---
description: Frequently asked questions
---

# FAQ

* **How do I provider index to indexer?**\
  ****Index provider runs along with storage provider which sends advertisement to Indexer when deals are made. Indexer then syncs the advertisement to store the multi-hashes to provider record mappings. See more technical details in _Ingestion_ section and _Index Provider_ section.
* **Will indexing impact my deal making process and performance?**\
  ****Not really, because the index providing process is not in the same process with deal making process. The index provider uses indices already generated as part of deal making and the impact is minimal and is not computationally heavy - only storing a tiny bit of information per deal. The Index generated are stored on [dagstore](https://github.com/filecoin-project/dagstore), and index provider code will generate ads from it to announce over pubsub to indexer.
* **I found peerID through** [**https://cid.contact/**](https://cid.contact/)**, how to find minerId through peerID?**\
  ****[StateMarketParticipants](https://filecoin-shipyard.github.io/js-lotus-client/api/full-node-api/state.html#statemarketparticipants) will give you the list of providers by minerID.[ \
  StateMinerInfo](https://filecoin-shipyard.github.io/js-lotus-client/api/full-node-api/state.html#stateminerinfo) will give you its markets peerID. \
  Note that this table (or the reverse lookup) would need to be calculated periodically, rather than each time it’s needed.
* **Once I get the PeerID and Multiaddress from the cid.contact, what should I do to get the data? Through lotus retrieval or others?** \
  Retrieval clients such as [https://github.com/ipfs-shipyard/w3rc](https://github.com/ipfs-shipyard/w3rc) could connect to that peerID and attempt Graphsync retrieval.
* **What’s the requirement to run an indexer node? How do I run one?**\
  ****[StoretheIndex Github Repo](https://github.com/filecoin-project/storetheindex) has the implementation PL maintains for running Indexer node. It has the Kubernetes containers and docker config info.
* **How much storage capacity is required for running indexer node?**\
  ****A couple TB at the moment and growing, it could grow to 1PB in the future. You could also index a portion of the data instead of full copy of index.
* **How much does it cost to run Indexer nodes?**\
  ****We are in the processing of gathering data for cost. A rough estimate from PL implementation is about $4k / month right now. We do not require partners to run full replication of index data though, if cost is a concern, you could also store a subset of index data from specific providers.
* **Where is index data stored?**\
  ****For PL’s implementation, we leverage Amazon EBS, we welcome different implementations.
* **How are Indexer data stored across Indexer nodes?**\
  ****We are storing full replication of data with federation at the moment. We are looking into sharded / scaled design across indexer nodes to improve performance in the near future.
* **What is the request volume?**\
  ****At this time our retrieval request volume is about 10k QPS coming from Hydra (IPFS DHT), and AutoRetrieve also has \~1K QPS.
* **What is the bandwidth and hardware requirement, is SSD good enough for running indexer node?**\
  ****We recommend about \~1GB connection; no specific hardware requirement as it is specific to your choice of implementation, but SSD is good enough for storage.
* **How do we get requests routed to our indexer if we run an indexer node?**\
  ****As for how to get requests routed to indexer node, you would need to give us the public endpoint of your indexer node, then we can route the request traffic from services like autoretrieve and IPFS gateway to yours.
