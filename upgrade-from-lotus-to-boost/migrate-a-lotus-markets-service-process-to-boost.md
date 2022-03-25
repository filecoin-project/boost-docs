---
description: >-
  This section describes how to upgrade your lotus-miner markets service to
  boostd
---

# Migrate a Lotus markets service process to Boost

{% hint style="warning" %}
If you are running a `monolith` `lotus-miner`and have not yet split the `markets` service into an individual process, refer to the [Lotus documentation](https://lotus.filecoin.io/storage-providers/configure/split-markets-miners/) and first do that, before upgrading to Boost.
{% endhint %}

If you are running a `markets` service as a separate `lotus-miner` process:

1\. Stop accepting incoming deals

2\. Wait for incoming deals to complete

3\. Shutdown the markets process

4\. Backup the markets repository

5\. Backup the markets datastore (in case you decide to roll back from Boost to Lotus) with:

```
lotus-shed market export-datastore --repo <repo> --backup-dir <backup-dir>
```

6\. Make sure you have a Lotus node and miner running

7\. Create and send funds to two new wallets for Boost

&#x20;   Boost currently uses two wallets for storage deals:

* The publish storage deals wallet - This wallet pays the gas cost when Boost sends the `PublishStorageDeals` message.
* The deal collateral wallet - When the Storage Provider accepts a deal, they must put collateral for the deal into escrow. Boost moves funds from this wallet into escrow with the `StorageMarketActor`.

```
PUBLISH_STORAGE_DEALS_WALLET=`lotus wallet new bls`
PLEDGE_COLLAT_WALLET=`lotus wallet new bls`
lotus send --from mywallet $PUBLISH_STORAGE_DEALS_WALLET 10
lotus send --from mywallet $PLEDGE_COLLAT_WALLET 10
```

8\. Boost keeps all data in a directory called the repository. By default the repository is at `~/.boost`. To use a different location pass the `--boost-repo` parameter.

9\. Create authentication tokens for the `boost` nodes. They will be used by the `boost` node to make JSON-RPC calls to the `mining/sealing/proving` node.

```
export APISEALER=`lotus-miner auth api-info --perm=admin`
export APISECTORINDEX=`lotus-miner auth api-info --perm=admin`
```

10\. Run `boostd migrate` to initialize the repository and start the migration:

```
boostd --vv migrate \
       --import-markets-repo=~/.my-markets-repo \
       --wallet-publish-storage-deals=$PUBLISH_STORAGE_DEALS_WALLET \
       --wallet-collateral-pledge=$PLEDGE_COLLAT_WALLET \
       --max-staging-deals-bytes=50000000000
```

The `migrate` command

* Initializes a Boost repository
* Migrates markets datastore keys to Boost
  * Storage and retrieval deal metadata
  * Storage and retrieval ask data
* Migrates markets libp2p keys to Boost
* Migrates markets config to Boost (libp2p endpoints, settings etc)
* Migrates the markets DAG store to Boost

11\. Run the `boostd` service, which will start:

* libp2p listeners for storage and retrieval
* the JSON RPC API
* the graphql interface (used by the react front-end)
* the web server for the react front-end

```
boostd --vv run
```

{% hint style="info" %}
In your firewall you will need to open the ports that libp2p listens on, so that Boost can receive storage and retrieval deals.

See the `Libp2p` section of `config.toml` in the [architecture.md](../boost-architecture/architecture.md "mention")
{% endhint %}

### Web UI

1\. Build the React frontend

```
cd react

# Download and install npm packages needed by the React frontend
npm install

# Build the optimized JavaScript and CSS in boost/react/build
npm run build
```

2\. Open the Web UI

Open http://localhost:8080 in your browser.

{% hint style="info" %}
To access a web UI running on a remote server, you can open an SSH tunnel from your local machine:

```
ssh -L 8080:localhost:8080 myserver
```
{% endhint %}
