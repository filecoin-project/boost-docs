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

1. If you haven't made any legacy deals with Boost:
   1. Stop `boostd`&#x20;
   2. Run your `lotus-miner` `markets` service process as you previously did\

2. If you have made new legacy deals with Boost, and want to migrate them back:
   1. Stop `boostd`
   2. Copy the `dagstore` directory from `boost` repository to `markets` repository.
   3. Export Boost deals datastore keys/values:\
      `lotus-shed market export-datastore --repo <repo> --backup-dir <backup-dir>`\
      ``\
      `Wrote backup file to <backup-dir>/markets.datastore.backup`
   4. Import the exported deals datastore keys/values from `boost` to `lotus markets`:\
      `lotus-shed market import-datastore --repo <repo> --backup-path <backup-path>`\
      ``\
      `Completed importing from backup file <backup-path>`
