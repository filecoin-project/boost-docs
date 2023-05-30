---
description: This page details the Boost data service - boostd-data
---

# boostd-data

The `boostd-data` service is a proxy to the underlying backend database that hosts the Local Index Directory (LID). Currently there is support for two stores:

* LevelDB
* YugabyteDB

## Usage

```
boostd-data run yugabyte --hosts <HOSTS> \
                         --connect-string <CONNECT-STRING> \
                         --addr <LISTEN-ADDR>
```

