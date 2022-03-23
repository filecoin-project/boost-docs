# As a storage provider

### Initialization and Running

1\. Make sure you have a Lotus fullnode and miner running

2\. Create wallets

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

3\. Create Boost repository

Boost keeps all data in a directory called the repository. By default the repository is at `~/.boost`. To use a different location pass the `--boost-repo` parameter.

Run `boostd init` to create and initialize the repository:

```
export $(lotus auth api-info --perm=admin)
export $(lotus-miner auth api-info --perm=admin)

boostd --vv init \
       --api-sealer=`lotus-miner auth api-info --perm=admin` \
       --api-sector-index=`lotus-miner auth api-info --perm=admin` \
       --wallet-publish-storage-deals=$PUBLISH_STORAGE_DEALS_WALLET \
       --wallet-collateral-pledge=$PLEDGE_COLLAT_WALLET \
       --max-staging-deals-bytes=50000000000
```

* `--api-sealer` is the API info for the lotus-miner instance that does sealing
* `--api-sector-index` is the API info for the lotus-miner instance that provides storage
* `--max-staging-deals-bytes` is the maximum amount of storage to be used for downloaded files (once the limit is reached Boost will reject subsequent incoming deals)

{% hint style="warning" %}
If you are already running a split markets process (MRA) don't run `boostd init`

Instead see the section [migrate-from-a-split-markets-process-to-boost.md](migrate-from-a-split-markets-process-to-boost.md "mention")
{% endhint %}

4\. Run Boost service

Run the `boostd` service to start

* libp2p listeners for storage and retrieval
* the JSON RPC API
* the graphql interface (used by the react front-end)
* the web server for the react front-end

```
export $(lotus auth api-info --perm=admin)

boostd --vv run
```

{% hint style="info" %}
In your firewall you will need to open the ports that libp2p listens on so that Boost can receive storage and retrieval deals. See the `Libp2p` section of `config.toml` in the [architecture.md](architecture.md "mention")
{% endhint %}

### Web UI

1. Build the react front-end

```
cd react

# Download and install npm packages needed by the react front-end
npm install

# Build the optimized javascript and css in boost/react/build
npm run build
```

1. Open the Web UI

Open http://localhost:8080 in your browser.

{% hint style="info" %}
To access a web UI running on a remote server you can open an SSH tunnel from your local machine:

```
ssh -L 8080:localhost:8080 myserver
```
{% endhint %}

##
