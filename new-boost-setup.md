---
description: How setup a new Boost instance for a new lotus-miner
---

# New Boost Setup

{% hint style="danger" %}
If you are already running a Boost node then please follow the [migration tutorial](tutorials/how-to-migrate-boost-v1-to-boost-v2.md) and do not attempt the below steps. This can result in permanent data loss.
{% endhint %}

{% hint style="warning" %}
Make sure you have read the [**Components**](deployment.md) page before proceeding. Boost v2 introduces a new service called **boostd-data** which requires a database to be installed - YugabyteDB or LevelDB.
{% endhint %}

## Introduction

Boost v2 introduces the Local Index Directory as a replacement for the DAG store. It scales horizontally and provides a more intuitive experience for users, by surfacing problems in the UI and providing repair functionality.

<figure><img src=".gitbook/assets/Screenshot 2023-06-14 at 13.13.54.png" alt=""><figcaption></figcaption></figure>

## Prerequisites

### Install YugabyteDB

The Local Index Directory stores retrieval indices in a YugabyteDB database. Retrieval indices store the size and location of each block in the deal data.

We recommend running YugabyteDB on a dedicated machine with SSD drives. Depending on how many blocks there are in the user data, the retrieval indices may require up to 2% of the size of the unsealed data. e.g. 1 TiB of unsealed user data may require a 20 GiB index.

YugabyteDB should require about the same amount of space as your DAG store requires today.

You can find more information about YugabyteDB in the `Components` section:

{% content-ref url="components/yugabytedb.md" %}
[yugabytedb.md](components/yugabytedb.md)
{% endcontent-ref %}

## Instructions

Follow these instructions in order to setup a new Boost instance with `boostd-data` service:

1. Make sure you have a Lotus node and miner running
2. Create and send funds to two new wallets on the lotus node to be used for Boost. Boost currently uses two wallets for storage deals:

* The publish storage deals wallet - This wallet pays the gas cost when Boost sends the `PublishStorageDeals` message.
* The deal collateral wallet - When the Storage Provider accepts a deal, they must put collateral for the deal into escrow. Boost moves funds from this wallet into escrow with the `StorageMarketActor`.

{% code fullWidth="false" %}
```
export PUBLISH_STORAGE_DEALS_WALLET=`lotus wallet new bls`
export COLLAT_WALLET=`lotus wallet new bls`
lotus send --from mywallet $PUBLISH_STORAGE_DEALS_WALLET 10
lotus send --from mywallet $COLLAT_WALLET 10
```
{% endcode %}

3. Set the publish storage deals wallet as a control wallet

```
lotus-miner actor control set --really-do-it $PUBLISH_STORAGE_DEALS_WALLET
```

4. [Build the latest Boost V2 binaries](getting-started/#building-and-installing)
5. Run the `boostd-data` service

`boostd-data` is a data proxy service which abstracts the access to LID through an established API. It makes it easier to secure the underlying database and not expose it. `boostd-data` listens to a _websocket_ interface, which is the entrypoint which should be exposed to `boostd`, and`booster-http`

Start the `boostd-data` service with parameters to connect to YugabyteDB on its Cassandra and PostgreSQL interfaces:

```
./boostd-data run yugabyte \
  --hosts <yugabytedb-hosts> \
  --connect-string="postgresql://<username>:<password>@<yugabytedb>:5433" \
  --addr 0.0.0.0:8044
```

{% hint style="info" %}
The `boostd-data` service can run a separate machine from Boost as long as the service is reachable via the network from the boost node
{% endhint %}

6. Create and initialize the Boost repository

{% hint style="warning" %}
If you are already running a Lotus markets service process, you should\
run `boostd migrate` instead of `boostd init`

See section [Broken link](broken-reference "mention") for more details.
{% endhint %}

Boost keeps all data in a directory called the _repository_. By default the repository is at `~/.boost`. To use a different location pass the `--boost-repo` parameter (must precede any particular command verb, e.g. `boostd --boost-repo=<PATH> init`).

Export the environment variables needed for `boostd init` to connect to the lotus daemon and lotus miner.

```
export $(lotus auth api-info --perm=admin)
export $(lotus-miner auth api-info --perm=admin)
```

Export environment variables that point to the API endpoints for the sealing and mining processes. They will be used by the `boost` node to make JSON-RPC calls to the `mining/sealing/proving` node.

```
export APISEALER=`echo $MINER_API_INFO`
export APISECTORINDEX=`echo $MINER_API_INFO`
```

Run `boostd init` to create and initialize the repository:

```
boostd --vv init \
       --api-sealer=$APISEALER \
       --api-sector-index=$APISECTORINDEX \
       --wallet-publish-storage-deals=$PUBLISH_STORAGE_DEALS_WALLET \
       --wallet-deal-collateral=$COLLAT_WALLET \
       --max-staging-deals-bytes=50000000000
```

* `--api-sealer` is the API info for the lotus-miner instance that does sealing
* `--api-sector-index` is the API info for the lotus-miner instance that provides storage
* `--max-staging-deals-bytes` is the maximum amount of storage to be used for downloaded files (once the limit is reached Boost will reject subsequent incoming deals)

7. Update `ulimit` file descriptor limit if necessary. Boost deals will fail if the file descriptor limit for the process is not set high enough. This limit can be raised temporarily before starting the Boost process by running the command `ulimit -n 1048576`. We recommend setting it permanently by following the [Permanently Setting Your ULIMIT System Value](https://lotus.filecoin.io/kb/soft-fd-limit/) guide.
8. Make sure that the correct _peer id_ and _multiaddr_ for your SP is set on chain, given that `boost init` generates a new identity. Use the following commands to update the values on chain:

```
lotus-miner actor set-addrs <MULTIADDR>
lotus-miner actor set-peer-id <PEER_ID>
```

{% hint style="info" %}
\<MULTIADDR> should be the same as the `ListenAddresses` you set in the `Libp2p` section of the config.toml of Boost\
\<PEER\_ID> can be found in the output of `boostd net id` command
{% endhint %}

9. Update `boostd` repository config

Configure `boostd` repository config (located at `<boostd repo>/config.toml`) to point to the exposed `boostd-data` service endpoint. Note that the connection must be configured to go over a websocket.

For example:

```
[LocalIndexDirectory]
  ServiceApiInfo = "ws://<boostd-data>:8044"
```

10. Start the `boostd` process

```
boostd --vv run
```

## Conclusion

At this stage you should have the latest version of Boost running with the Local Index Directory. Go to the **Local Index Directory** page and review the number sections:

#### Pieces

<figure><img src=".gitbook/assets/Screenshot 2023-06-19 at 13.43.34.png" alt=""><figcaption></figcaption></figure>

Pieces section shows counters for total pieces of user data that your SP is storing as well as whether you are keeping _unsealed_ and _indexed_ copies of them.

#### Flagged pieces

<figure><img src=".gitbook/assets/Screenshot 2023-06-19 at 16.31.18.png" alt=""><figcaption></figcaption></figure>

_Flagged pieces_ are pieces that either lack an unsealed copy or are missing an index. For the sealed-only user data, you should make sure that you unseal individual sectors if you want this data to be retrievable.

_Sealed only_ copies of data are not retrievable and are only being proven on-chain within the corresponding _deadline_ / _window_. Typically sealed only data is considered as archival as it is not immediately retrievable. If the client requests it, the SP sealing pipeline must first unseal it, which typically takes 1-2 hours, and only then the data becomes available.

_Flagged (unsealed)_ pieces is user data that your SP is hosting, which is not indexed.

{% hint style="info" %}
We recommend that you trigger re-indexing for these pieces, so that data becomes retrievable. Check the [tutorial on re-indexing flagged unsealed pieces](tutorials/how-to-re-index-unsealed-pieces-that-are-flagged-by-lid-in-boost-v2.md) for more information.
{% endhint %}

#### Deal Sector Copies

<figure><img src=".gitbook/assets/Screenshot 2023-06-19 at 16.34.41.png" alt=""><figcaption></figcaption></figure>

Deal Sector Copies section displays counters of your sectors state - whether you keep unsealed copies for all sectors or not. Ideally the SP should keep unsealed copies for all data that should be immediately retrievable.

#### Sector Proving State

<figure><img src=".gitbook/assets/Screenshot 2023-06-19 at 16.34.46.png" alt=""><figcaption></figcaption></figure>

Sector Proving State section displays counters of your active and inactive sectors - active sectors are those that are actively proven on-chain, inactive sectors are those that you might have failed to publish a _WindowPoSt_ for, or are expired or removed.
