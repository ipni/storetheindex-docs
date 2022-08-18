---
description: General and hardware requirement to run a Indexer node
---

# Prerequisites

## Hardware Requirements

The hardware requirements for running an Indexer node the dependent on the ingestion and query rate of the node. The requirements are affected by the following:

1. Number of index-providers to ingest from
2. Expected number of queries to be served
3. Rate of growth of records

The hardware requirements can be recalculated based on the metrics provided by the node once it has been started.

{% hint style="danger" %}
An Indexer node should always have enough resources to avoid the scenario where rate of ingestion rate is higher than the processing rate for those ingestion. Otherwise, it will never be able to catch up with the ingestion rate.
{% endhint %}

### CPU

4 CPUs

CPU usage is generally proportional to the number of concurrent providers we are ingesting from. Typically, the higher the number of concurrent ingestions, the higher the CPU usage.

Another reason for high CPU usage could be slow IOPS where the indexer node is not able to process the incoming ad chain fast enough.

Based, on our indexer node, we recommend to start with 4 CPUs. This number can be adjusted based on the peak ingestion, number of providers and CPU usage at the peak ingestion instances.

In the index-provider run by Protocol Labs, we have seen CPU utilization shoot up by 300% to process a peak ingestion of 20 million records.

### Memory

25 GiB

The memory utilization on a indexer node is mainly dependent on the following:

1.  Sync sessions from providers\
    The sync sessions from providers via graphsync and HTTP partially stores the traversal state into memory until the ad chain is fully processed.&#x20;


2. Cache entries stored in the memory

In the index-provider run by Protocol Labs, we have seen memory utilization shoot up by 4 GiB to process a peak ingestion of 20 million records.

### Storage

The node should have enough storage to able able to storage all the ingested data and should account for future increase in the ingestion rate. The primary factors affecting the storage will be&#x20;

1. Number of index-providers connecting to the node
2. Number of advertisements per provider
3. Expected growth rate to account for any aberration in ingest rate

When calculating the storage requirement, please ensure to take 20% headroom when it comes to disk utilisation. Otherwise, storage might end up being 100% utilized and lead to problems with database.

We recommend using "sth" as the choice for `valuestore` because `go-storethehash` is configured to be very efficient for storing multihashes. In the index-provider run by Protocol Labs, we are storing 5.7+ billion records in 4.5 TiB space with "sth" `valuestore`.

### Storage IOPS

Ingest is one the primary operation of an indexer node and it is an IO bound operation.\
If the IOPS are not high enough, there would be more CPU wait time required to process the records. This will increase the CPU requirement of the node. If there is not enough CPU and IOPS then, the node will never be able to catch up with the advertisements published by all the index-providers.&#x20;

IOPS per ad depends on context ID of the advertisement, whether it is a removal or not, as well as its number of entries etc.
