# Migrate a monolith lotus-miner to Boost

{% hint style="warning" %}
If you have already split a your lotus-miner into a separate markets process (MRA), follow the steps in [migrate-a-lotus-markets-service-process-to-boost.md](migrate-a-lotus-markets-service-process-to-boost.md "mention").
{% endhint %}

{% hint style="info" %}
Please note that a monolith miner can only split into boost(market)+miner on the same physical machine as it requires access to the miner repo to migrate the deal metadata.
{% endhint %}

### Prepare to migrate

1\. Make sure you have a Lotus node and miner running

2\. Create and send funds to two new wallets on the lotus node to be used for Boost

Boost currently uses two wallets for storage deals:

* The publish storage deals wallet - This wallet pays the gas cost when Boost sends the `PublishStorageDeals` message.

{% hint style="info" %}
If PublishStorageDeal control wallet is already setup then it can be reused in the boost for `PUBLISH_STORAGE_DEALS_WALLET`.
{% endhint %}

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

6\. Set the environment variable `LOTUS_FULLNODE_API` to allow access to the lotus node API.

```
lotus auth api-info -perm auth
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

{% hint style="info" %}
In your firewall you will need to ensure that the libp2p ports that Boost listens on are open, so that Boost can receive storage and retrieval deals.\
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

### API Access

Boost API can be accessed by setting the environment variable `BOOST_API_INFO` same as `LOTUS_MARKET_INFO`.&#x20;

```
export BOOST_API_INFO=<TOKEN>:<API Address>
boostd auth api-info -perm auth
```

### Migrating Boost from one machine to another

Once the Boost has been split from the monolith miner, it can be moved to another physical or virtual machine by following the below steps.

1. Build the boost binary on the new machine by following the [Getting Started](../getting-started/) step.
2. Copy the boost repo from the original monolith miner machine to the new dedicated boost machine.
3. Set the environment variable `LOTUS_FULLNODE_API` to allow access to the lotus node API.
4. Open the required port on the firewall on the monolith miner machine to allow connection to lotus-miner API.
5. In your firewall you will need to ensure that the libp2p ports that Boost listens on are open, so that Boost can receive storage and retrieval deals.\
   See the `Libp2p` section of `config.toml` in the [architecture.md](../boost-architecture/architecture.md "mention")
6. Start the `boostd` process.

```
boostd --vv run
```
