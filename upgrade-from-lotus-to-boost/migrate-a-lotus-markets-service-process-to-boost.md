---
description: >-
  This section describes how to upgrade your lotus-miner markets service to
  boostd
---

# Migrate a Lotus markets service process to Boost

{% hint style="warning" %}
If you are running a `monolith` `lotus-miner`and have not yet split the `markets` service into an individual process, follow the steps in [migrate-a-monolith-lotus-miner-to-boost.md](migrate-a-monolith-lotus-miner-to-boost.md "mention").
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

7\. Create and send funds to two new wallets on the lotus node to be used for Boost

&#x20;   Boost currently uses two wallets for storage deals:

* The publish storage deals wallet - This wallet pays the gas cost when Boost sends the `PublishStorageDeals` message.

{% hint style="info" %}
If you already have a PublishStorageDeal control wallet setup then it can be reused in boost as the `PUBLISH_STORAGE_DEALS_WALLET`.
{% endhint %}

* The deal collateral wallet - When the Storage Provider accepts a deal, they must put collateral for the deal into escrow. Boost moves funds from this wallet into escrow with the `StorageMarketActor`.

{% hint style="info" %}
If you already have a wallet that you want to use as the source of funds for deal collateral, then it can be reused in boost as the `COLLAT_WALLET`.
{% endhint %}

```
PUBLISH_STORAGE_DEALS_WALLET=`lotus wallet new bls`
COLLAT_WALLET=`lotus wallet new bls`
lotus send --from mywallet $PUBLISH_STORAGE_DEALS_WALLET 10
lotus send --from mywallet $COLLAT_WALLET 10
```

8\. Boost keeps all data in a directory called the repository. By default the repository is at `~/.boost`. To use a different location pass the `--boost-repo` parameter.

9\. Export the environment variables needed for `boostd migrate-markets` to connect to the lotus daemon and lotus miner.

```
export $(lotus auth api-info --perm=admin)
export $(lotus-miner auth api-info --perm=admin)
```

Export environment variables that point to the API endpoints for the sealing and mining processes. They will be used by the `boost` node to make JSON-RPC calls to the `mining/sealing/proving` node.

```
export APISEALER=`echo $MINER_API_INFO | awk '{split($0,a,"="); print a[2]}'`
export APISECTORINDEX=`echo $MINER_API_INFO | awk '{split($0,a,"="); print a[2]}'
```

10\. Set the publish storage deals wallet as a control wallet.

```
export OLD_CONTROL_ADDRESS=`lotus-miner actor control list  --verbose | grep -v owner | grep -v worker | grep -v beneficiary | awk '{print $3}' | grep -v key | tr -s '\n'  ' '`
lotus-miner actor control set --really-do-it $PUBLISH_STORAGE_DEALS_WALLET $OLD_CONTROL_ADDRESS
```

11\. Run `boostd migrate-markets` to initialize the repository and start the migration:

```
boostd --vv migrate-markets \
       --import-markets-repo=~/.my-markets-repo \
       --wallet-publish-storage-deals=$PUBLISH_STORAGE_DEALS_WALLET \
       --wallet-deal-collateral=$COLLAT_WALLET \
       --max-staging-deals-bytes=50000000000
```

The `migrate-markets` command

* Initializes a Boost repository
* Migrates markets datastore keys to Boost
  * Storage and retrieval deal metadata
  * Storage and retrieval ask data
* Migrates markets libp2p keys to Boost
* Migrates markets config to Boost (libp2p endpoints, settings etc)
* Migrates the markets DAG store to Boost

12\. Run the `boostd` service, which will start:

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

Open http://localhost:8080 in your browser.

{% hint style="info" %}
To access a web UI running on a remote server, you can open an SSH tunnel from your local machine:

```
ssh -L 8080:localhost:8080 myserver
```
{% endhint %}

### API Access

Boost API can be accessed by setting the environment variable `BOOST_API_INFO` same as `LOTUS_MARKET_INFO`.&#x20;

```
export BOOST_API_INFO=<TOKEN>:<API Address>
boostd auth api-info -perm auth
```
