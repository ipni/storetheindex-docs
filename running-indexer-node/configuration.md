---
description: Indexer node configuration
---

# Configuration

## Configuration File

The storetheindex config file is a JSON document located at `$STORETHEINDEX_PATH/config`. It is read once at indexer instantiation, either for an offline command, or when starting the daemon. Commands that execute on a running daemon do not read the config file at runtime.

### Sample Config file

```
{
  "Version": 2,
  "Identity": {
    "PeerID": "12D3KooWJZSPZN7cudwBJ6UdTm8V5FmgWhAd2oVa8qFagU7bZSm1",
    "PrivKey": "CAESQKP70L69Q7An97xPH6g3PgSypws6mHYcAZ77t9pC2a0/geY+Kh3gaSzfPqHpSyjy7Fd3hQPweU+BNbhhKHSHrJw="
  },
  "Addresses": {
    "Admin": "/ip4/127.0.0.1/tcp/3002",
    "Finder": "/ip4/0.0.0.0/tcp/3000",
    "Ingest": "/ip4/0.0.0.0/tcp/3001",
    "P2PAddr": "/ip4/0.0.0.0/tcp/3003",
    "NoResourceManager": false
  },
  "Bootstrap": {
    "Peers": [
      "/dns4/bootstrap-1.mainnet.filops.net/tcp/1347/p2p/12D3KooWCwevHg1yLCvktf2nvLu7L9894mcrJR4MsBCcm4syShVc",
      "/dns4/bootstrap-3.mainnet.filops.net/tcp/1347/p2p/12D3KooWKhgq8c7NQ9iGjbyK7v7phXvG6492HQfiDaGHLHLQjk7R",
      "/dns4/bootstrap-8.mainnet.filops.net/tcp/1347/p2p/12D3KooWScFR7385LTyR4zU1bYdzSiiAb5rnNABfVahPvVSzyTkR",
      "/dns4/bootstrap-0.ipfsmain.cn/tcp/34721/p2p/12D3KooWQnwEGNqcM2nAcPtRR9rAX8Hrg4k9kJLCHoTR5chJfz6d",
      "/dns4/bootstrap-0.mainnet.filops.net/tcp/1347/p2p/12D3KooWCVe8MmsEMes2FzgTpt9fXtmCY7wrq91GRiaC8PHSCCBj",
      "/dns4/bootstrap-2.mainnet.filops.net/tcp/1347/p2p/12D3KooWEWVwHGn2yR36gKLozmb4YjDJGerotAPGxmdWZx2nxMC4",
      "/dns4/bootstrap-5.mainnet.filops.net/tcp/1347/p2p/12D3KooWLFynvDQiUpXoHroV1YxKHhPJgysQGH2k3ZGwtWzR4dFH",
      "/dns4/bootstrap-6.mainnet.filops.net/tcp/1347/p2p/12D3KooWP5MwCiqdMETF9ub1P3MbCvQCcfconnYHbWg6sUJcDRQQ",
      "/dns4/bootstrap-1.starpool.in/tcp/12757/p2p/12D3KooWQZrGH1PxSNZPum99M1zNvjNFM33d1AAu5DcvdHptuU7u",
      "/dns4/lotus-bootstrap.ipfsforce.com/tcp/41778/p2p/12D3KooWGhufNmZHF3sv48aQeS13ng5XVJZ9E6qy2Ms4VzqeUsHk",
      "/dns4/bootstrap-0.starpool.in/tcp/12757/p2p/12D3KooWGHpBMeZbestVEWkfdnC9u7p6uFHXL1n7m1ZBqsEmiUzz",
      "/dns4/bootstrap-4.mainnet.filops.net/tcp/1347/p2p/12D3KooWL6PsFNPhYftrJzGgF5U18hFoaVhfGk7xwzD8yVrHJ3Uc",
      "/dns4/bootstrap-7.mainnet.filops.net/tcp/1347/p2p/12D3KooWRs3aY1p3juFjPy8gPN95PEQChm2QKGUCAdcDCC4EBMKf",
      "/dns4/node.glif.io/tcp/1235/p2p/12D3KooWBF8cpp65hp2u9LK5mh19x67ftAam84z9LsfaquTDSBpt",
      "/dns4/bootstrap-1.ipfsmain.cn/tcp/34723/p2p/12D3KooWMKxMkD5DMpSWsW7dBddKxKT7L2GgbNuckz9otxvkvByP"
    ],
    "MinimumPeers": 4
  },
  "Datastore": {
    "Dir": "datastore",
    "Type": "levelds"
  },
  "Discovery": {
    "LotusGateway": "https://api.chain.love",
    "Policy": {
      "Allow": true,
      "Except": ["12D3KooWEbhQxDZpDwvqBVPbxUXz8AquMziyUv2HT77YNKQYPiDx"],
      "Publish": true,
      "PublishExcept": null
    },
    "PollInterval": "24h0m0s",
    "PollRetryAfter": "5h0m0s",
    "PollStopAfter": "168h0m0s",
    "PollOverrides": [
      {
        "ProviderID": "12D3KooWRYLtcVBtDpBZDt5zkAVFceEHyozoQxr4giccF7fquHR2",
        "Interval": "12h0m0s",
        "RetryAfter": "45m0s",
        "StopAfter": "3h0m0s"
      }
    ],
    "RediscoverWait": "5m0s",
    "Timeout": "2m0s"
  },
  "Indexer": {
    "CacheSize": 300000,
    "ConfigCheckInterval": "30s",
    "GCInterval": "30m0s",
    "ShutdownTimeout": "10s",
    "ValueStoreDir": "valuestore",
    "ValueStoreType": "sth"
  },
  "Ingest": {
    "AdvertisementDepthLimit": 33554432,
    "EntriesDepthLimit": 65536,
    "HttpSyncRetryMax": 4,
    "HttpSyncRetryWaitMax": "30s",
    "HttpSyncRetryWaitMin": "1s",
    "HttpSyncTimeout": "10s",
    "IngestWorkerCount": 10,
    "PubSubTopic": "/indexer/ingest/mainnet",
    "RateLimit": {
      "Apply": false,
      "Except": null,
      "BlocksPerSecond": 100,
      "BurstSize": 500
    },
    "ResendDirectAnnounce": true,
    "StoreBatchSize": 4096,
    "SyncSegmentDepthLimit": 2000,
    "SyncTimeout": "2h0m0s"
  },
  "Logging": {
    "Level": "info",
    "Loggers": {
      "basichost": "warn",
      "bootstrap": "warn",
      "dt-impl": "warn",
      "dt_graphsync": "warn",
      "graphsync": "warn"
    }
  }
  "Peering": {
    "Peers": [
      "/ip4/10.11.12.13/3003/p2p/12D3KooWH1cT2UxrKYikmrksmCsdekvb6yuhxvNMup68DLpFEKZ3"
    ]
  }
}
```

### Version

| Parameter | Example | Description                                                                                                                                                          |
| --------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Version   | 0       | Current version of configuration file. This is used to defermine if an existing configuration should be upgraded. No default. This is written during initialization. |

### Identity

| Parameter | Example                                                                                      | Description                                                                                                                                                                                                                                       |
| --------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PeerID    | 12D3KooWJZSPZN7cudwBJ6UdTm8V5FmgWhAd2oVa8qFagU7bZSm1                                         | PeerID is the peer ID of the indexer that must match the given PrivKey. If unspecified, it is automatically generated from the PrivKey. There is no default value. This is generated during initialization.                                       |
| PrivKey   | CAESQKP70L69Q7An97xPH6g3PgSypws6mHYcAZ77t9pC2a0/geY+Kh3gaSzfPqHpSyjy7Fd3hQPweU+BNbhhKHSHrJw= | PrivKey represents the peer identity of the indexer. If unset, the key is loaded from the file at the path specified via STORETHEINDEX\_PRIV\_KEY\_PATH environment variable. There is no default value. This is generated during initialization. |

### Addresses

| Parameter         | Example                | Description                                                                                                            |
| ----------------- | ---------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| Admin             | ip4/127.0.0.1/tcp/3002 | Admin is the admin http listen address. Set to "none" to disable this server for both http and libp2p.                 |
| Finder            | /ip4/0.0.0.0/tcp/3000  | Finder is the finder http listen address. Set to "none" to disable this server for both http and libp2p.               |
| Ingest            | /ip4/0.0.0.0/tcp/3001  | Ingest is the index data ingestion http listen address. Set to "none" to disable this server for both http and libp2p. |
| P2PAddr           | /ip4/0.0.0.0/tcp/3003  | P2PMaddr is the libp2p host multiaddr for all servers. Set to "none" to disable libp2p hosting.                        |
| NoResourceManager | false                  | NoResourceManager disables the libp2p resource manager when true.                                                      |

### Bootstrap

| Parameter    | Example                                                                                                         | Description                                                                                                                                                                                             |
| ------------ | --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Peers        | \[/"dns4/bootstrap-1.mainnet.filops.net/tcp/1347/p2p/12D3KooWCwevHg1yLCvktf2nvLu7L9894mcrJR4MsBCcm4syShVc", ""] | It is an array of multiaddr strings. It defaults to the multiaddrs of the filecoin mainnet bootstrap nodes.                                                                                             |
| MinimumPeers | 4                                                                                                               | MinimumPeers governs whether to bootstrap more connections. If the node has less open connections than this number, it will open connections to the bootstrap nodes. Set to 0 to disable bootstrapping. |

### Datastore

| Parameter | Example   | Description                                                                                                                                    |
| --------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Dir       | datastore | Dir is the directory where the datastore is kept. If this is not an absolute path then the location is relative to the indexer repo directory. |
| Type      | levelds   | Type is the type of datastore.                                                                                                                 |

### Discovery

| Parameter      | Example                                                                        | Description                                                                                                                                                                                                                                                                   |
| -------------- | ------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| LotusGateway   | [https://api.chain.love](https://api.chain.love)                               | LotusGateway is the host or host:port for a lotus gateway used to verify providers on the blockchain.                                                                                                                                                                         |
| Policy         | [#discovery.policy](configuration.md#discovery.policy "mention")               | Policy configures which providers are allowed and blocked, rate-limited, and allow to publish on behalf of others.                                                                                                                                                            |
| PollInterval   | 24h0m0s                                                                        | PollInterval is the amount of time to wait without getting any updates for a provider, before sending a request for the latest advertisement. Values are a number ending in "s", "m", "h" for seconds. minutes, hours.                                                        |
| PollRetryAfter | 5h0m0s                                                                         | PollRetryAfter is the amount of time from one poll attempt, without a response, to the next poll attempt, and is also the time between checks for providers to poll. This value must be smaller than PollStopAfter for there to be more than one poll attempt for a provider. |
| PollStopAfter  | 168h0m0s                                                                       | PollStopAfter is the amount of time, from the start of polling, to continuing polling for the latest advertisment without getting a response.                                                                                                                                 |
| PollOverrides  | [#discovery.polloverrides](configuration.md#discovery.polloverrides "mention") | PollOverrides configures polling for specific providers.                                                                                                                                                                                                                      |
| RediscoverWait | 5m0s                                                                           | RediscoverWait is the amount of time that must pass before a provider can be discovered following a previous discovery attempt. A value of 0 means there is no wait time.                                                                                                     |
| Timeout        | 2m0s                                                                           | Timeout is the maximum amount of time that the indexer will spend trying to discover and verify a new provider.                                                                                                                                                               |

#### Discovery.Policy

| Parameter     | Example                                                                                                           | Description                                                                                                                                                                                                                                                                                                                                                |
| ------------- | ----------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Allow         | true                                                                                                              | Allow is either false or true, and determines whether a peer is allowed (true) or is blocked (false), by default.                                                                                                                                                                                                                                          |
| Except        | \["12D3KooWCwevHg1yLCvktf2nvLu7L9894mcrJR4MsBCcm4syShVc", "12D3KooWKhgq8c7NQ9iGjbyK7v7phXvG6492HQfiDaGHLHLQjk7R"] | Except is a list of peer IDs that are exceptions to the Allow policy. If Allow is true, then all peers are allowed except those listed in Except. If Allow is false, then no peers are allowed except those listed in Except. in other words, Allow=true means that Except is a deny-list and Allow=false means that Except is an allow-list.              |
| Publish       | true                                                                                                              | Publish determines whether or not peers are allowed to publish advertisements for a provider with a different peer ID.                                                                                                                                                                                                                                     |
| PublishExcept | \["12D3KooWCwevHg1yLCvktf2nvLu7L9894mcrJR4MsBCcm4syShVc", "12D3KooWKhgq8c7NQ9iGjbyK7v7phXvG6492HQfiDaGHLHLQjk7R"] | PublisherExcept is a list of peer IDs that are exceptions to the Publish policy. If Publish is false, then all allowed peers cannot publish advertisements for providers with a different peer ID, unless listed in PublishExcept. If Publish is true, then all allowed peers can publish advertisements for any provider, unless listed in PublishExcept. |

#### Discovery.PollOverrides

There are no default `Discovery.PollOverrides` elements. These are created manually.

| Parameter     | Example                                                                                                                                                              | Description                                |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| PollOverrides | "PollOverrides": \[ { "ProviderID": "12D3KooWRYLtcVBtDpBZDt5zkAVFceEHyozoQxr4giccF7fquHR2", "Interval": "12h0m0s", "RetryAfter": "45m0s", "StopAfter": "3h0m0s" } ], | Polling policy override for specific peers |

### Indexer

| Parameter           | Example    | Description                                                                                                                                            |
| ------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| CacheSize           | 300000     | Maximum number of CIDs that cache can hold. Setting to -1 disables the cache.                                                                          |
| ConfigCheckInterval | 30s        | ConfigCheckInterval is the time between config file update checks.                                                                                     |
| GCInterval          | 30m0s      | GCInterval configures the garbage collection interval for valuestores that support it.                                                                 |
| ShutdownTimeout     | 10s        | ShutdownTimeout is the duration that a graceful shutdown has to complete before the daemon process is terminated.                                      |
| ValueStoreDir       | valuestore | Directory where value store is kept. If this is not an absolute path then the location is relative to the indexer repo directory. ValueStoreDir string |
| ValueStoreType      | sth        | Type of valuestore to use, such as "sth" or "pogreb".                                                                                                  |

### Ingest

| Parameter               | Example                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ----------------------- | ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AdvertisementDepthLimit | 33554432                                                         | AdvertisementDepthLimit is the total maximum recursion depth limit when syncing advertisements. The value -1 means no limit and zero means use the default value. Limiting the depth of advertisements can be done if there is a need to prevent an indexer from ingesting long chains of advertisements. A chain of advertisements is synced by separate requests to the provider for each advertisement. These requests are done in groups (segments) of size set by SyncSegmentDepthLimit. AdvertisementDepthLimit sets the limit on the total number of advertisements across all segments. |
| EntriesDepthLimit       | 65536                                                            | EntriesDepthLimit is the total maximum recursion depth limit when syncing advertisement entries. The value -1 means no limit and zero means use the default value. The purpose is to prevent overload from extremely long entry chains resulting from publisher misconfiguration. A chain of multihash entries chunks is synced by separate requests to the provider for each chunk. These requests are done in groups (segments) of size set by SyncSegmentDepthLimit. EntriesDepthLimit sets the limit on the total number of entries chunks across all segments.                             |
| HttpSyncRetryMax        | 4                                                                | HttpSyncRetryMax sets the maximum number of times HTTP sync requests should be retried.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| HttpSyncRetryWaitMax    | 30s                                                              | HttpSyncRetryWaitMax sets the maximum time to wait before retrying a failed HTTP sync.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| HttpSyncRetryWaitMin    | 1s                                                               | HttpSyncRetryWaitMin sets the minimum time to wait before retrying a failed HTTP sync.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| HttpSyncTimeout         | 10s                                                              | HttpSyncTimeout sets the time limit for HTTP sync requests.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| IngestWorkerCount       | 10                                                               | IngestWorkerCount sets how many ingest worker goroutines to spawn. This controls how many concurrent ingest from different providers we can handle.                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| PubSubTopic             | /indexer/ingest/mainnet                                          | PubSubTopic sets the topic name to which to subscribe for ingestion announcements.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| RateLimit               | [#ingest.ratelimit](configuration.md#ingest.ratelimit "mention") | RateLimit contains rate-limiting configuration.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ResendDirectAnnounce    | false                                                            |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| StoreBatchSize          | 4096                                                             | StoreBatchSize is the number of entries in each write to the value store. Specifying a value less than 2 disables batching. This should be smaller than the maximum number of multihashes in an entry block to write concurrently to the value store.                                                                                                                                                                                                                                                                                                                                           |
| SyncSegmentDepthLimit   | 2000                                                             | SyncSegmentDepthLimit is the depth limit of a single sync in a series of calls that collectively sync advertisements or their entries. The value -1 disables the segmentation where the sync will be done in a single call and zero means use the default value.                                                                                                                                                                                                                                                                                                                                |
| SyncTimeout             | 2h0m0s                                                           | SyncTimeout is the maximum amount of time allowed for a sync to complete before it is canceled. This can be a sync of a chain of advertisements or a chain of advertisement entries. The value is an integer string ending in "s", "m", "h" for seconds. minutes, hours.                                                                                                                                                                                                                                                                                                                        |

#### Ingest.RateLimit

| Parameter       | Example                                                                                                           | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| --------------- | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Apply           | false                                                                                                             | Apply is either false or true, and determines whether a peer is subject to rate limiting (true) or not (false), by default.                                                                                                                                                                                                                                                                                                                                                   |
| Except          | \["12D3KooWCwevHg1yLCvktf2nvLu7L9894mcrJR4MsBCcm4syShVc", "12D3KooWKhgq8c7NQ9iGjbyK7v7phXvG6492HQfiDaGHLHLQjk7R"] | Except is a list of peer IDs that are exceptions to the Apply rule. If Apply is false then peers are not rate-limited unless they appear in the Except list. If Apply is true, then only the peers listed in Except are not rate-limited.                                                                                                                                                                                                                                     |
| BlocksPerSecond | 100                                                                                                               | BlocksPerSecond is the number of blocks allowed to be transferred per second. An advertisement and a block of multihashes are both represented as a block, so this limit applies to both. Setting a value of 0 disables rate limiting, meaning that the rate is infinite.                                                                                                                                                                                                     |
| BurstSize       | 500                                                                                                               | BurstSize is the maximum number of blocks that can be received at once. After this, BlocksPerSecond additional blocks maybe received each second, and any more results in rate limiting. With HTTP ingestion, rate limiting waits until more blocks are allowed to be received. With graphsync, rate limiting terminates the session and resumes it when sufficient time has passed to be able to receive BurstSize blocks. A value of 0 results in 10 times BlocksPerSecond. |

### Logging

| Parameter | Example                                                                                                                  | Description                                                                                                     |
| --------- | ------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| Level     | info                                                                                                                     | Level sets the log level for all loggers that do not have a setting in // Loggers. The default value is "info". |
| Loggers   | "Loggers": { "basichost": "warn", "bootstrap": "warn", "dt-impl": "warn", "dt\_graphsync": "warn", "graphsync": "warn" } | Loggers sets log levels for individual loggers.                                                                 |

### Peering

| Parameter | Example | Description |
| --------- | ------- | ----------- |
| Peers     | null    |             |

## Runtime Reloadable Items

The storetheindex daemon can reload some portions of its config without restarting the entire daemon. This is done by editing the config file and then using the admin sub-command `reload-config` or sending the daemon process a `SIGHUP` signal. The daemon will automatically reload the edited config after 30 seconds when the daemon is run with the `--watch-config` flag or with the environ variable `STORETHEINDEX_WATCH_CONFIG=true`. The reloadable portions of the config files are:

* Discovery.Policy
* Indexer.ConfigCheckInterval
* Indexer.ShutdownTimeout
* Ingest.IngestWorkerCount
* Ingest.RateLimit
* Ingest.StoreBatchSize
* Logging
* Peering
