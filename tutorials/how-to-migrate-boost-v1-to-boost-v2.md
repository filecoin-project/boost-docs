# How to upgrade from Boost v1 to Boost v2

{% hint style="warning" %}
Make sure you have read the [**Components**](../deployment.md) page before proceeding. Boost v2 introduces a new service called **boostd-data** which requires a database to be installed - YugabyteDB or LevelDB.
{% endhint %}

## Introduction

Boost v2 introduces the Local Index Directory as a replacement for the DAG store. It scales horizontally and provides a more intuitive experience for users, by surfacing problems in the UI and providing repair functionality.

<figure><img src="../.gitbook/assets/Screenshot 2023-06-14 at 13.13.54.png" alt=""><figcaption></figcaption></figure>

## Prerequisites

### Install YugabyteDB database

The Local Index Directory stores retrieval indexes in a YugabyteDB database.

We recommend running YugabyteDB on a dedicated machine with SSD drives. Depending on the structure of the user data, indices may require up to 2% of the size of the user data, e.g. 20 GiB index for 1 TiB of raw data.

Review the size of your existing DAG store and account for similar disk space requirements for YugabyteDB.

## Instructions

Follow these instructions in order to migrate your existing DAG store into the new Local Index Directory and upgrade from Boost v1 to Boost v2:

**1. Clone the Boost repository to a temporary directory**

Note: Don’t overwrite your existing boost instance at this stage

```
cd /tmp
git clone git@github.com:filecoin-project/boost.git boostv2
cd boostv2
```

**2. Check out the Boost v2 branch**

```
git checkout release/v2
```

**3. Build from source**

```
make
```

**4. Migrate dagstore indices**

Depending on the amount of data your SP is storing, this step could take anywhere from a few minutes to a few hours. You can run it even while Boost v1 continues to run. The command can be stopped and restarted. It will continue from where it left off.

Run the migration with parameters to connect to YugabyteDB on its Cassandra and PostgreSQL interfaces:

```
./migrate-lid yugabyte \
  --hosts <yugabytedb-hosts> \
  --connect-string="postgresql://<username>:<password>@<yugabytedb>:5433" \
  dagstore
```

It will output a progress bar, and also a log file with detailed migration information at `migrate-yugabyte.log`

**5. Run the `boostd-data` service**

`boostd-data` is a data proxy service which abstracts the access to LID through an established interface. It makes it easier to secure the underlying database and not expose it. `boostd-data` listens to a websocket interface, which is the entrypoint which should be exposed to `boostd`, and`booster-http`

Start the `boostd-data` service with parameters to connect to YugabyteDB on its Cassandra and PostgreSQL interfaces:

```
./boostd-data run yugabyte \
  --hosts <yugabytedb-hosts> \
  --connect-string="postgresql://<username>:<password>@<yugabytedb>:5433" \
  --addr 0.0.0.0:8044
```

**6. Update `boostd` repository config**

Configure `boostd` repository config (located at `<boostd repo>/config.toml`) to point to the exposed `boostd-data` service endpoint. Note that the connection must be configured to go over a websocket.

For example:

```
[LocalIndexDirectory]
  ServiceApiInfo = "ws://<boostd-data>:8044"
```

**7. Install Boost v2**

```
make install
```

Note that in v2 `booster-http` and `booster-bitswap` take slightly different parameters (see below).

**8. Stop `boostd`, `booster-http` and `booster-bitswap`**

You need to stop `boostd` before migrating `piece info` data.

**9. Migrate piece info data (information about which sector each deal is stored in)**

This should take no more than a few minutes.

```
./migrate-lid yugabyte \
  --hosts <yugabytedb-hosts> \
  --connect-string="postgresql://<username>:<password>@<yugabytedb>:5433" \
  pieceinfo
```

**10. Start the upgraded versions of `boostd`, `booster-http` and `booster-bitswap`**

Note that `booster-http` and `booster-bitswap` take slightly different parameters:

* `--api-boost` is removed
* There is a new parameter `--api-lid` that points to the `boostd-data` service (which hosts LID), e.g. `--api-lid="ws://<boostd-data>:8044"`
