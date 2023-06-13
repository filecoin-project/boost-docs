# How to migrate from Boost v1 to Boost v2

### Introduction

Boost v2 introduces the Local Index Directory as a replacement for the DAG store. It scales horizontally and provides a more intuitive experience for users, by surfacing problems in the UI and providing repair functionality.

### Instructions

#### 1. Install Yugabyte database

The Local Index Directory stores retrieval indexes in a [Yugabyte](https://www.yugabyte.com) database.

We recommend running Yugabyte DB on a dedicated machine with SSD drives.
The indexes require up to 2% of the size of the data, eg 20 GiB index for 1 TiB of raw data.
Review the size of your existing DAG store and account for similar disk requirements.

#### 2. Migrate DAG store to Local Index Directory

**1. Clone the boost repo to a temporary directory**

Note: Don’t overwrite your existing boost instance at this stage

```
cd /tmp
git clone git@github.com:filecoin-project/boost.git boostv2
cd boostv2
```

**2. Check out the boost v2 branch**

```
git checkout release/v2
```

**3. Build the Local Index Directory migration tool**

```
make deps
cd cmd/migrate-lid
make
```

**4. Migrate dagstore indexes.**

This step will likely take a few hours. You can run it even while boost v1 continues to run. The command can be stopped and restarted. It will continue from where it left off.

TODO: Help SP estimate how long per GiB of dagstore data

```
./migrate-lid yugabyte dagstore --help
```

Run the migration with parameters to connect to Yugabyte DB on its Cassandra and Postgres interfaces:

```
./migrate-lid yugabyte \
  --hosts 127.0.0.1 \
  --connect-string="postgresql://postgres:postgres@127.0.0.1:5433" \
  dagstore
```

It will output a progress bar, and also a log file with detailed migration information at `migrate-yugabyte.log`

**5. Run the boostd-data service**

boostd, booster-http, etc interface with LID through the boostd-data service.
Start the boostd-data service with parameters to connect to Yugabyte DB on its Cassandra and Postgres interfaces:

```
cd /tmp/boostv2/extern/boostd-data
make
./boostd-data run yugabyte \
  --hosts 127.0.0.1 \
  --connect-string="postgresql://postgres:postgres@127.0.0.1:5433" \
  --addr 0.0.0.0:8044
```

**6. Update boostd config**

Add the boostd-data service config to `<boost repo>/config.toml`.
Note that the connection must be configured to go over a web socket, and the host and port must point to the boostd-data service.

For example:

```
[LocalIndexDirectory]
  ServiceApiInfo = "ws://1.2.3.4:8044"
```

**7. Install boost v2**

```
cd /tmp/boostv2
make install
make booster-http
make booster-bitswap
```

Note that in v2 booster-http and booster-bitswap take slightly different parameters (see below).

**8. Stop boostd, booster-http and booster-bitswap**

Note: You need to stop boostd before migrating piece info data.

**9. Migrate piece info data (information about which sector each deal is stored in)**

This should take no more than a few minutes.

```
cd /tmp/boostv2/cmd/migrate-lid
./migrate-lid yugabyte \
  --hosts 127.0.0.1 \
  --connect-string="postgresql://postgres:postgres@127.0.0.1:5433" \
  pieceinfo
```

**10. Start v2 of boostd, booster-http and booster-bitswap**

Note that booster-http and booster-bitswap take slightly different parameters:

* The parameter `api-boost` is removed
* There is a new parameter `api-lid` that points to the LID service
* eg `--api-lid="ws://1.2.3.4:8044"`
