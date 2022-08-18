---
description: How to setup an Indexer node
---

# Setup

## Supported Platforms

* Baremetal
* Cloud Platforms (Recommended over Baremetal)
* [Kubernetes](kubernetes.md) (Any distribution)

We recommend running the Indexer node in a cloud platform or Kubernetes to allow for flexibility. The Indexer node may require change in hardware resources and a physical machines does not allow a quick change in resources.&#x20;

## Setup

1\. Once a Linux machine has been provisioned on a cloud or virtualization or baremetal platform, create a dedicated user to run the Indexer node. Please do not use the `root` user for security purposes.

2\. Add the required storage space to the home directory of the user or to the location that will be used to host the storetheindex repo.

3\. Clone the "storetheindex" repo

```
git clone https://github.com/filecoin-project/storetheindex.git
```

4\. Checkout the [latest stable release](https://github.com/filecoin-project/storetheindex/releases)

```
git checkout <latest stable tag>
```

```
Example:
git checkout v0.4.17
```

5\. Compile the binaries

```
make build
```

6\. Export the `STORETHEINDEX_PATH` variable to set the repo location. You can skip this step to use the default location `~/.storetheindex`

7\. Initalize the repo with appropriate options.&#x20;

```
./storetheindex init --help
NAME:
   indexer init - Initialize or upgrade indexer node config file

USAGE:
   indexer init [command options] [arguments...]

OPTIONS:
   --cachesize value        Maximum number of multihashes that result cache can hold, -1 to disable cache (default: 0)
   --listen-admin value     Admin HTTP API listen address [$STORETHEINDEX_LISTEN_ADMIN]
   --listen-finder value    Finder HTTP API listen address [$STORETHEINDEX_LISTEN_FINDER]
   --listen-ingest value    Ingestion and discovery HTTP API listen address [$STORETHEINDEX_LISTEN_INGEST]
   --lotus-gateway value    Address for a lotus gateway to collect chain information [$STORETHEINDEX_LOTUS_GATEWAY]
   --no-bootstrap           Do not configure bootstrap peers (default: false) [$NO_BOOTSTRAP]
   --pubsub-topic value     Subscribe to this pubsub topic to receive advertisement notification [$STORETHEINDE_PUBSUB_TOPIC]
   --store value, -s value  Type of value store (sth, pogreb). Default is "sth" [$STORETHEINDEX_VALUE_STORE]
   --upgrade, -u            Upgrade the config file to the current version, saving the old config as config.prev, and ignoring other flags  (default: false)
```

```
Initializing indexer node at /home/indexer/.storetheindex
generating ED25519 keypair...done
peer identity: 12D3KooWQZewrcDawxTVrDXWTncbLjUvZdmCr9gfommdiiiY3J4P
```

5\. Verify that `PeerID` and `Privkey` are part of the config

```
cat ~/.storetheindex/config
```

## Start the Indexer node

1\. Once the repo has been initalized, we can start the daemon

```
./storetheindex daemon
```
