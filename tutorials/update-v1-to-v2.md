---
description: >-
  Steps to migrate Boost from v1 to v2
---

# Migrate Boost from v1 to v2

## Introduction
Boost v2 introduces the Local Index Directory as a replacement for the DAG store. It scales horizontally and provides a more intuitive experience for users, by surfacing problems in the UI and providing repair functionality.

## Instructions

### 1. Install Yugabyte database

The Local Index Directory stores retrieval indexes in a [Yugabyte](https://www.yugabyte.com) database.

We recommend running Yugabyte DB on a dedicated machine with SSD drives.
The indexes require about 2% of the size of the data, eg 20 GiB index for 1 TiB of raw data.

### 2. Migrate DAG store to Local Index Directory

#### 1. Clone the boost repo to a temporary directory

Note: Don’t overwrite your existing boost instance at this stage

```
cd /tmp
git clone git@github.com:filecoin-project/boost.git
cd boost
```

#### 2. Check out the boost v2 branch
```
git checkout release/v2
```

#### 3. Build the Local Index Directory migration tool
```
cd cmd/migrate-lid
make
```

#### 4. Migrate dagstore indexes.

This step will likely take a few hours.
You can run it even while boost v1 continues to run.
The command can be stopped and restarted. It will continue from where it left off.

TODO: Help SP estimate how long per GiB of dagstore data

```
./migrate-lid yugabyte dagstore --help
```

For example:

```
./migrate-lid yugabyte \
    --connect-string yugabyte://<yugabyte-ip> \
    --hosts <yugabyte-ip> \
    dagstore
```

It will output a progress bar, and also a log file with detailed migration information at `migrate-yugabyte.log`

#### 5. Update boostd config

Add yugabyte config to `<boost repo>/config.toml

For example:
```
[LocalIndexDirectory]
  [LocalIndexDirectory.Yugabyte]
    ConnectString = "yugabyte://127.0.0.1"
    Hosts = "127.0.0.1"
```

#### 6. Install boostd v2

```
make install
make booster-http
make booster-bitswap
```

Note that in v2 booster-http and booster-bitswap take slightly different parameters (see below).

#### 7. Stop boostd, booster-http and booster-bitswap

Note: You need to stop boostd before migrating piece info data.

#### 8. Migrate piece info data (information about which sector each deal is stored in)

This should take no more than a few minutes.
```
./migrate-lid yugabyte \
    --connect-string yugabyte://<yugabyte-ip> \
    --hosts <yugabyte-ip> \
    piecestore
```

#### 9. Start v2 of boostd, booster-http and booster-bitswap

Note that booster-http and booster-bitswap take slightly different parameters:
- The parameter `api-boost` is removed
- There is a new parameter `api-lid` that points to the LID service
- By default the LID service is started with boostd, on the same machine, at port 8042
- The connection must be made over a web socket (use `ws` in the url instead of `http`)
- eg `ws://localhost:8042`

