# Migrate a monolith lotus-miner to Boost

{% hint style="warning" %}
If you have already split a your lotus-miner into a separate markets process (MRA), follow the steps in [migrate-a-lotus-markets-service-process-to-boost.md](migrate-a-lotus-markets-service-process-to-boost.md "mention").
{% endhint %}

### Prepare to migrate

1\. Make sure you have a Lotus node and miner running

2\. Create and send funds to two new wallets for Boost

Boost currently uses two wallets for storage deals:

* The publish storage deals wallet - This wallet pays the gas cost when Boost sends the `PublishStorageDeals` message.
* The deal collateral wallet - When the Storage Provider accepts a deal, they must put collateral for the deal into escrow. Boost moves funds from this wallet into escrow with the `StorageMarketActor`.

```
export PUBLISH_STORAGE_DEALS_WALLET=`lotus wallet new bls`
export PLEDGE_COLLAT_WALLET=`lotus wallet new bls`
lotus send --from mywallet $PUBLISH_STORAGE_DEALS_WALLET 10
lotus send --from mywallet $PLEDGE_COLLAT_WALLET 10
```

3\. Set up environment variables needed for Boost migration

Export the environment variables needed for `boostd migrate-monolith` to connect to the lotus daemon and lotus miner.

```
export $(lotus auth api-info --perm=admin)
export $(lotus-miner auth api-info --perm=admin)
```

Export environment variables that point to the API endpoints for the sealing and mining processes. They will be used by the `boost` node to make JSON-RPC calls to the `mining/sealing/proving` node.

```
export APISEALER=`lotus-miner auth api-info --perm=admin`
export APISECTORINDEX=`lotus-miner auth api-info --perm=admin`
```

### Shut down lotus-miner

1\. Stop accepting incoming deals

2\. Wait for incoming deals to complete

3\. Shutdown the lotus-miner

4\. Backup the lotus-miner repository

5\. Backup the lotus-miner datastore (in case you decide to roll back from Boost to Lotus) with:

```
lotus-shed market export-datastore --repo <repo> --backup-dir <backup-dir>
```

### Migrate from the lotus-miner repo to the Boost repo

Run `boostd migrate-monolith` to create and initialize the boost repository:

```
boostd --vv migrate-monolith \
       --import-miner-repo=<lotus-miner repo path> \
       --api-sealer=$APISEALER \
       --api-sector-index=$APISECTORINDEX \
       --wallet-publish-storage-deals=$PUBLISH_STORAGE_DEALS_WALLET \
       --wallet-collateral-pledge=$PLEDGE_COLLAT_WALLET \
       --max-staging-deals-bytes=50000000000 
```

The `migrate-monolith` command

* Initializes a Boost repository
* Migrates markets datastore keys to Boost
  * Storage and retrieval deal metadata
  * Storage and retrieval ask data
* Migrates markets libp2p keys to Boost
* Migrates markets config to Boost (libp2p endpoints, settings etc)
* Migrates the markets DAG store to Boost

### Update the lotus-miner config

1\. Backup lotus-miner's `config.toml`

```
cp <miner repo>/config.toml <miner repo>/config.toml.backup
```

2\. Disable the markets subsystem in miner config:

Boost replaces the markets subsystem in the lotus-miner, so we need to disable the subsystem in config:

Under the `[Subsystems]` section set `EnableMarkets = false`

3\. Change the miner's libp2p port

Boost replaces the markets subsystems, and listens on the same libp2p port, so we need to change the libp2p port that the miner is listening on.

Under the `[Libp2p]` section change the port in `ListenAddresses`

### Restart lotus-miner

Start lotus-miner up again so that Boost can connect to the miner when it starts.

### Run the `boostd` service

The `boostd` service will start:

* libp2p listeners for storage and retrieval
* the JSON RPC API
* the graphql interface (used by the react front-end)
* the web server for the react front-end

```
boostd --vv run
```

In your firewall you will need to ensure that the libp2p ports that Boost listens on are open, so that Boost can receive storage and retrieval deals.

{% hint style="info" %}
See the `Libp2p` section of `config.toml` in the [architecture.md](../boost-architecture/architecture.md "mention")
{% endhint %}
