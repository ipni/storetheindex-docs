---
description: How to query an indexer provider
---

# Querying Indexer Provider

An indexer node can be queried over HTTP or libp2p for a multihash or a payload CID.

### Using payload CID

#### GET request

```
GET /cid/<cid>
```

#### Curl request

```
curl  https://<INDEXER NODE ADDRESS>/cid/<payload CID>
```

{% code overflow="wrap" %}
```
curl https://cid.contact/cid/bafybeigvgzoolc3drupxhlevdp2ugqcrbcsqfmcek2zxiw5wctk3xjpjwy
```
{% endcode %}

### Query using the multihash

#### GET request

```
GET /mh/<multihash
```

#### Curl request

```
curl  https://<INDEXER NODE ADDRESS>/mh/<multihash>
```

```
curl https://cid.contact/multihash/QmcgwdNjFQVhKt6aWWtSPgdLbNvULRoFMU6CCYwHsN3EEH
```

#### POST Request

```
POST /mh
```

The body for the POST request can be found [here](https://github.com/filecoin-project/storetheindex/blob/f824c7f1f08c606bdc069e9454dfcf157680841e/api/v0/finder/model/model.go#L14-L16) as a JSON.

### Find command

Both [`storetheindex`](https://github.com/filecoin-project/storetheindex) as well as [`index-provider`](https://github.com/filecoin-project/index-provider) binaries contain a `find` command that can be used to query the indexer nodes for a multihash or a payload CID.

```
provider find --help
```

```
storetheindex find --help
```

### Go client library

The [storetheindex](https://github.com/filecoin-project/storetheindex) repo also ships a client library that can be integrated in other programs to query the indexer nodes. The [client library](https://github.com/filecoin-project/storetheindex/tree/main/api/v0/finder/client) can be used to query the indexer nodes over **`HTTP`** as well as **`libp2p`** connections.&#x20;

This client library is also utilised by the `find` command in the `storetheindex` as well as `index-provider` binaries. Programmers can use these commands as reference implementations.
