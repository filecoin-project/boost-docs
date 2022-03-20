# What is Boost?

## Overview

Boost is a tool for Storage Providers to manage data storage and retrievals on filecoin. It replaces the go-fil-markets package in lotus with a standalone binary that runs alongside a lotus daemon and lotus miner. Boost exposes libp2p interfaces for making storage and retrieval deals, a web interface for managing storage deals, and a graphql interface for accessing and updating real time deal information.

![](<.gitbook/assets/Boost Interfaces.png>)

## Running Boost

The Boost source code repository is hosted at [github.com/filecoin-project/boost](https://github.com/filecoin-project/boost)

### Building

```
git clone https://github.com/filecoin-project/boost
cd boost
make build
```

### Initialisation and Running

1. Compile and install (move binaries to $PATH)

```
make build
make install
```

1. Make sure you have a Lotus fullnode and miner running
2. Create wallets

Boost uses two wallets for storage deals:

*   The publish storage deals wallet

    This wallet pays the gas cost when Boost sends the PublishStorageDeals message.
*   The deal collateral wallet

    When the Storage Provider accepts a deal, they must put collateral for the deal into escrow. Boost moves funds from this wallet into escrow with the StorageMarketActor.

Create the wallets and send funds to the wallets:

```
PUBLISH_STORAGE_DEALS_WALLET=`lotus wallet new bls`
PLEDGE_COLLAT_WALLET=`lotus wallet new bls`
lotus send --from mywallet $PUBLISH_STORAGE_DEALS_WALLET 10
lotus send --from mywallet $PLEDGE_COLLAT_WALLET 10
```

1. Create Boost repository

Boost keeps all data in a directory called the repository. By default the repository is at `~/.boost`. To use a different location pass the `--boost-repo` parameter.

Run `boost init` to create and initialize the repository:

```
export $(lotus auth api-info --perm=admin)
export $(lotus-miner auth api-info --perm=admin)

boost --vv init \
      --api-sealer=`lotus-miner auth api-info --perm=admin` \
      --api-sector-index=`lotus-miner auth api-info --perm=admin` \
      --wallet-publish-storage-deals=$PUBLISH_STORAGE_DEALS_WALLET \
      --wallet-collateral-pledge=$PLEDGE_COLLAT_WALLET \
      --max-staging-deals-bytes=50000000000
```

* `--api-sealer` is the API info for the lotus-miner instance that does sealing
* `--api-sector-index` is the API info for the lotus-miner instance that provides storage
* `--max-staging-deals-bytes` is the size of the space where Boost stores downloaded files

1. Run Boost service

Run the boost service to start libp2p listeners for storage and retrieval, and to start the web interface.

```
export $(lotus auth api-info --perm=admin)

boost --vv run
```

1. Open the Web UI

Open http://localhost:8080 in your browser.

{% hint style="info" %}
To access a web UI running on a remote server you can open an SSH tunnel from your local machine:

```
ssh -L 8080:localhost:8080 myserver
```
{% endhint %}

## Migrate from a split markets process to Boost

If you are running markets as a separate lotus-miner instance, use `boost migrate`:

```
boost --vv migrate \
      --import-markets-repo=~/.my-markets-repo \
      --wallet-publish-storage-deals=$PUBLISH_STORAGE_DEALS_WALLET \
      --wallet-collateral-pledge=$PLEDGE_COLLAT_WALLET \
      --max-staging-deals-bytes=50000000000
```

The migrate command

* Initializes a Boost repository
* Migrates markets datastore keys to Boost
  * Storage and retrieval deal metadata
  * Storage and retrieval ask data
* Migrates markets libp2p keys to Boost
* Migrates markets config to Boost (libp2p endpoints, settings etc)
* Migrates the markets DAG store to Boost
