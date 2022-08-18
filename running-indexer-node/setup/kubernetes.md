---
description: How to setup Indexer on Kubernetes
---

# Kubernetes

## Pre-requisites

1\. Clone the "storetheindex" repo to a Linux machine

```
git clone https://github.com/filecoin-project/storetheindex.git
```

2\. Checkout the [latest stable release](https://github.com/filecoin-project/storetheindex/releases)

```
git checkout <latest stable tag>
```

```
Example:
git checkout v0.4.17
```

3\. Compile the binaries

```
make build
```

4\. Initalize the "storetheindex" to generate libp2p keys

```
./storetheindex init
```

```
Initializing indexer node at /root/.storetheindex
generating ED25519 keypair...done
peer identity: 12D3KooWQZewrcDawxTVrDXWTncbLjUvZdmCr9gfommdiiiY3J4P
```

5\. Verify that `PeerID` and `Privkey` are part of the config

```
cat ~/.storetheindex/config
```

6\. We will use these keys for the Kubernetes config later

## Deployment

Create a separate tenant for the Indexer node to avoid and conflicts with existing applications. Download the [base manifest from the repo](https://github.com/filecoin-project/storetheindex/tree/main/deploy/manifests/base/storetheindex) and edit the based manifests to your needs. Once all the customization is complete, you can deploy the manifest using `Kustomization`.

### Docker Image

We recommend running only a stable release version of the docker image. You should use the same image tag used to generate the libp2p keys in the [prerequisites](kubernetes.md#pre-requisites). All docker images for the Indexer node can be found in the [docker hub](https://hub.docker.com/r/filecoin/storetheindex/tags).

You can also build your own local image if you do not wish to use the docker hub image.

* Clone the "storetheindex" repo to a Linux machine

```
git clone https://github.com/filecoin-project/storetheindex.git
```

* Checkout the [latest stable release](https://github.com/filecoin-project/storetheindex/releases)

```
git checkout <latest stable tag>
```

```
Example:
git checkout v0.4.17
```

* Make the local image

```
make docker
```

### Libp2p Keys

The libp2p keys generated in the prerequisites have be provided to the `indexer StatefulSet`. Without the keys, indexer pod will keep failing to start. There are 2 methods to provide these keys.

1. Edit the [indexer-config.yaml](https://github.com/filecoin-project/storetheindex/blob/main/deploy/manifests/base/storetheindex/indexer-config.yaml) manifest and add the keys directly to the config
2. Use a Kubernetes secret to mount the keys inside the pods. We recommend using the secret for security purposes and flexibility

### Resources

The base manifest does not contain any resource definition in the `indexer StatefulSet`. We recommend adding these definition to the StatefulSet.

Currently, a 5 TiB volume is requested by the manifest and this should be changed as per the initial requirement calculations. It is also recommended to add suitable storageClassName with high IOPS to avoid using a default storageClass.

### Ingress

The Indexer node will have to expose ingestion and finder services to the internet. The details of the ports can fe found in [indexer-service.yaml](https://github.com/filecoin-project/storetheindex/blob/main/deploy/manifests/base/storetheindex/indexer-service.yaml)

### Indexer Config

The default [indexer-config.yaml](https://github.com/filecoin-project/storetheindex/blob/main/deploy/manifests/base/storetheindex/indexer-config.yaml) should be edited to make the required adjustments in the indexer config before deployment. Some of these configuration like `valuestore` can only be changed during the initialization and would require a complete reset (data loss) to change.

