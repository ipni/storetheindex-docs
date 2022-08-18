# Usage

To become a index provider involves three main parts:

1. A DagStore migration
2. Indexing announcement of available content
3. Content routing verification

### DagStore Migration

The DagStore migration is required to roll out the new **CARv2 indexing format**, [`MultihashIndexSorted`](https://ipld.io/specs/transport/car/carv2/#format-0x0401-multihashindexsorted). It regenerates the indices with the latest CARv2 index format that includes the full multihash of CIDs in each DagStore shard. For more inform see [DagStore Lotus documentation](https://lotus.filecoin.io/docs/storage-providers/dagstore).

### Indexing Announcement

The indexing announcement publishes advertisements about the content available for **retrieval** onto a **gossipsub** topic, which is listened to by a set of **indexer nodes**.

The advertisements are then processed by the indexer in order to provide an endpoint that allows clients to lookup where and how to retrieve data for a given multihash. For more information, see [Indexer Node Design](https://www.notion.so/pl-strflt/Indexer-Node-Design-fbd1e7d3110c4b1fb154b31f2585e6ff).

### Content Routing Verification

The content routing verification involves checking that the advertisements published by the Lotus instance are ingested by the indexer nodes and for a given stored multihash the correct provider is returned by the indexer node.

Note that ingestion of advertisements by the indexer nodes is progressive; total ingestion time depends on the number of multhihashes being advertised. Therefore, verification needs to be performed with some delay after indexing announcement is made.

## Usage Expectations:

### New CAR index format in DagStore

The new index format stores the multihash code as well as the digest. The size of DagStore index files as a result are slightly larger. The size increase is **negligible**.

### Index Provider GossipSub Announcements

The index provider integration announces changes to the chain of advertisements onto a gossipsub topic, named `/indexer/ingest/mainnet`, which is propagated through the Lotus daemon onto the indexer nodes.

### Index Provider GraphSync Server

The index provider integration exposes a GraphSync server which serves requests from indexer nodes to sync the list of advertised multihashes provided by the SP.

Note that the server is exposed on the address configured under `IndexProvider` configuration section, with key `ListenAddresses`. The advertisements will include the address configured under the same section with key `AnnounceAddresses`. You must make sure both sets of address are reachable publicly. For more information see [Indexer Provider Configuration](https://github.com/filecoin-project/lotus/discussions/8087#index-provider-configuration).

### Index Provider Storage Usage

The index provider integration shares a datastore with `markets` process, wrapped under the namespace `index-provider`. The datastore entries stored include:

1. internal mappings used by the index provider engine, and
2. cache of chained multihash chunks.

The storage used by the internal mappings is negligible.

The storage used by caching is bound by an LRU cache, the maximum size of which is configured to `1024`, i.e. the number of chunks cached. The maximum length of each chunk configured to `16,384`.

The exact storage usage, then, depends on the number of multihashes stored in a single chunk and the size of each multihash, and can be calculated as:

```
1024 * 16384 * <multihash-length>
```

For example, caching 128-bit long multihashes will result in chunk sizes of 0.25MiB with maximum cache growth of 256 MiB.

Note that the LRU cache may grow beyond its max size if the generated chain of chunks is longer than the configured `LinkChunkSize`. This is to avoid partial caching of chunks within a single advertisement. The cache expansion is logged in `INFO` level at `provider/engine` logging subsystem and can be monitored for diagnosis purposes.

## Steps to Become an Index Provider

1.  **Stop the `daemon`  `miner` and `markets` processes**

    Stop all lotus processes to suspend any changes to the state of DagStore during the rollout.

    The daemon needs to be stoped in order to roll out a change to the API that protects connections between markets and daemon libp2p node.
2.  **Back up the existing DagStore repository**

    The DagStore repository is located at `$LOTUS_MARKETS_PATH/dagstore` by default. Make a copy of that folder. This is necessary for:

    1. verifying that the expected shard indices are re-generated after migration, and
    2. ❗rolling back the changes in case of an error.
3.  **Delete the existing DagStore repository**

    Delete the DagStore repository located at `$LOTUS_MARKETS_PATH/dagstore` by default. The absence of the repository signals to the Lotus instance that a DagStore migration is needed and will automatically trigger one upon `markets` instance start-up.
4.  **Rotate any existing Lotus log files and adjust log level**

    For easier debugging rotate any existing logs so that the new logs only include output generated by the target release.
5.  **Deploy `daemon` and `miner` processes**

    Deploy the index provider tag - [`master-spx.idxprov.rc-1`](https://github.com/filecoin-project/lotus/releases/tag/master-spx.idxprov.rc-1) to the `daemon` and `miner` processes and await until they are fully started and ready.

    Note: This tag is based off release/v1.15.0, thus it also supports the upcoming OhSnap network v15 upgrade!

    They are ready when the following commands succeed:

    `lotus-miner storage-deals list`

    `lotus-miner sectors list`
6.  **Deploy the target release on `markets` process**

    Download and deploy [`master-spx.idxprov.rc-1`](https://github.com/filecoin-project/lotus/releases/tag/master-spx.idxprov.rc-1)to the market process.
7.  **Start the `markets` process**

    Start only the `markets` process and wait for the following log line in the markets process logs:

    `dagstore migration completed successfully`

    This indicates that the list of shards that require initialisation have been queued for processing. See \[[DagStore First-time Migration](https://lotus.filecoin.io/docs/storage-providers/dagstore/#first-time-migration)]\([https://lotus.filecoin.io/docs/storage-providers/dagstore/#first-time-migration](https://lotus.filecoin.io/docs/storage-providers/dagstore/#first-time-migration)) for more information. See [Indexer Provider Config](https://www.notion.so/SPX-Index-Provider-Test-Plan-32ebb5c695224ea7bf6c7cedf7da739a) to customize the configuration of the subsystem that announces indexes.
8.  **Configure logging subsystems**

    Make sure your lotus installation persists the log files for future debugging.\
    Set the log level for the following subsystems on market node to `INFO` :

    * `go-legs-gpubsub`
    * `provider/engine`
    * `dagstore`

    To do this run the following command:

    ```
    lotus-miner --call-on-markets log set-level --system provider/engine --system go-legs-gpubsub --system dagstore info
    ```
9.  **Initialise the the DagStore shards**

    To start the initialisation of DagStore shards, run:

    `lotus-miner dagstore initialize-all --concurrency=N` if you run a monolith miner process or `lotus-miner --call-on-market dagstore initialize-all --concurrency=N` if you have split your market subsystem.

    ⚠️ Initialization places IO workload on your storage system. `N` controls the number of deals that are concurrently initialised. See [DagStrore Force Bulk Initialisation](https://lotus.filecoin.io/docs/storage-providers/dagstore/#forcing-bulk-initialization) docs for more information.

    Wait for the initialisation to complete. The initialisation time is a factor of the volume of data stored, since it involves re-indexing the data blocks.
10. **Verify re-creation of DagStore repository**

    The successful completion of the previous step should recreate the DagStore repository, located at `$LOTUS_MARKETS_PATH/dagstore` . Navigate to that director. Under the subfolder `index` verify that matching `*.full.idx` files can be found for all files under the same sub-directory in the backup of DagStore taken in step 2.
11. ✨ **Announce all indices to the indexers**

    To announce all the indices in bulk to the indexers, run:

    `lotus-miner index announce-all` if you run a monolith miner process or `lotus-miner --call-on-market index announce-all` if you have split your market subsystem.

    This command generates advertisements and publishes indices onto the indexer gossipsub channel. In the markets logs look for a series of logs that include `deal announcement sent to index provider` . You should see one such log per deal. The log line also includes advertisement CID, the deal proposal CID to which it belongs and the shardKey from which its multihash entries are generated. The logs should also include logs that provide information about the number of multihash entries each advertisement includes. For example:

    ```
    deal announcement sent to index provider	{"advertisementCid": "baguqeeqqvr2irdrq45d7npj7elogzpaaam", "shard-key": "baga6ea4seaqegic2h4qoao4urcwhin7tgwlb4cguqymtriheqoyjjaabz6viegq", "proposalCid": "bafyreihhqszkcv3egsb7xkuhyswqjdy3oa2kboi2zjvrbkuj3jgq2g4d4m"}
    Generated linked chunks of multihashes	{"totalMhCount": 32449, "chunkCount": 2}
    ```

    Note that the bulk advertisement only announces deals that are not expired and handed over to the sealing subsystem. The expired deals will not be advertised. For any remaining deals the advertisement will occur after they are handed over to the sealing subsystem.

    Wait for the bulk indexing announcement to complete. The bulk announcement is complete when `finished announcing active deals to index provider` is logged.
12. **Verify indices in DagStore repository are ingested by indexer nodes**

    To verify ingestion, download and install the latest `provider` CLI tool from:

    * [https://github.com/filecoin-project/index-provider/releases/latest](https://github.com/filecoin-project/index-provider/releases/latest)

    The built binaries can be found under assets attached for each target platform.

    Once installed, download the following script.

    _Script below to be provided as a downloadable `.sh` file; for now pasted below for review purposes._

    ```
    #!/usr/bin/env sh

    MINER_ID="${1:?miner peer ID must be specified as the first argument}" 
    DAGSTORE_REPO="${2:?dagstore repo location must be specified as the second argument}" 
    SAMPLING_PROB="${3-0.05}"

    echo "MINER_ID:      ${MINER_ID}"
    echo "DAGSTORE_REPO: ${DAGSTORE_REPO}"
    echo "SAMPLING_PROB: ${SAMPLING_PROB}"
    echo ""

    for idx in ${DAGSTORE_REPO}/index/*.full.idx
    do
       echo "Verifying ${idx}"
       provider verify-ingest \
          --to cid.contact:80 \
          --print-unindexed-mhs \
          --provider-id "${MINER_ID}" \
          --from-car-index "${idx}" \
          --sampling-prob "${SAMPLING_PROB}" 
       echo ""
    done
    ```

    The script takes two mandatory argument:

    * Miner peer ID as the first argument, and
    * Path to dagstore repository as the second argument, e.g. `$LOTUS_MARKETS_PATH/dagstore`

    A third argument may optionally be specified as a number between `0.0>=1.0` which sets the selection probability of the multihash sample that is verified for ingestion, set to 5% by default.

    You can find out what your peer ID is using `lotus-miner net id`

    This script will:

    * iterate over all index files that match `*.full.idx` name,
    * selects 5% of the multihashes at random from each index, and
    * verifies that the indexer node has those multihash associated with the given `MINER_ID` as the provider.

    A verification result is printed for each file. Verify that verification is successful for each of the files. See `provider verify-ingest -h` for example output.

    ### Indexer Provider Configuration

    You can adjust the values under the `IndexProvider`session in the config.toml of your market process to configure indexes announcement to the indexer.

    If the session doesn't exist, you can manually add it:

    ```
    [IndexProvider]
      # The maximum number of multihash chunk links that index provider cache can store before
      # LRU eviction. If chunks belonging to a single advertisement are larger than the cache can
      # hold, the cache is resized to be able to hold all links. The actual disk usage depends on
      # LinkedChunkSize and the length of multihashes. For example, for 128-bit long multihashes
      # with the default LinkedChunkSize, and LinkCacheSize the cache size can grow to 256MiB. 
      #
      # type int
      # env var: LOTUS_INDEXPROVIDER_LINKCACHESIZE
      #LinkCacheSize = 1024

      # The number of multihashes in each chunk of the
      # advertised multihash entries linked list.  If multihashes are 128-bit, then
      # setting LinkedChunkSize = 16384 will result in blocks of 0.25MiB when
      # full.
      #
      # type int
      # env var: LOTUS_INDEXPROVIDER_LINKEDCHUNKSIZE
      #LinkedChunkSize = 16384

      # The gossipsub topic name used to publish change to the advertised content.
      #
      # env var: LOTUS_INDEXPROVIDER_PUBSUBTOPIC
      #PubSubTopic = "/indexer/ingest/mainnet"

      # Whether to purge all cached entries on start-up.
      #
      # env var: LOTUS_INDEXPROVIDER_PURGELINKCACHE
      #PurgeLinkCache = false

      # Binding address for the libp2p host contacted by indexer nodes to sync the list of advertised 
      # multihashes. Note that when port is set to 0 a random port is generated at runtime and may be 
      # different on every restart. The format of the strings specified must conform to multiaddress; 
      # see https://multiformats.io/multiaddr/
      #
      # type: []string
      # env var: LOTUS_INDEXPROVIDER_LISTENADDRESSES
      #ListenAddresses = ["/ip4/0.0.0.0/tcp/0", "/ip6/::/tcp/0"]

      # The address the endpoints at which the data associated to the advertised
      # multihashes can be retrieved. If not specified, the ListenAddresses are used instead. The format
      # of the strings specified must conform to multiaddress; see https://multiformats.io/multiaddr/
      #
      # type: []string
      # env var: LOTUS_INDEXPROVIDER_ANNOUNCEADDRESSES
      #AnnounceAddresses = []

      # The maximum number of simultaneous requests syncing the list of advertised multihashes between 
      # the indexers and the index provider.
      #
      # type: uint64
      # env var: LOTUS_INDEXPROVIDER_MAXSIMULTANEOUSTRANSFERS
      #MaxSimultaneousTransfers = 20
    ```
