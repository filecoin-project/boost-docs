---
description: >-
  This section describes how to roll back to Lotus markets service process if
  you are not happy with boostd
---

# Roll back to Lotus markets service process

{% hint style="warning" %}
Before you begin migration from Lotus markets service process to Boost, make sure you have a backup of your Lotus repository, by following the [Lotus documentation](https://lotus.filecoin.io/storage-providers/operate/backup-and-restore/).\
\
You can also do a full backup of the Lotus markets repository directory.
{% endhint %}

1. If you haven't made any legacy deals with Boost, you can stop `boostd` and switch back to using your `lotus-miner` `markets` service process.
2. If you have made new legacy deals with Boost, and want to migrate them back:
   1. Copy the backup `config.toml` to `markets` repository.
   2. Copy the `dagstore` directory from `boost` repository to `markets` repository.
   3. TODO: Copy relevant datastore keys/values from `boost` to `markets`.
   4. Copy the `keystore` directory from `boost` repository to `markets` repository.
   5. Copy the `indexer-provider` directory from `boost` repository to `markets` repository.
   6. Copy the `storage.json` file from `boost` repository to `markets` repository.
   7. Copy the `token` file from `boost` repository to `markets` repository.
