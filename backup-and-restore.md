---
description: How to backup and restore Boost
---

# Backup and Restore

## Backup

Boost now supports both online and offline backups. The backup command will output a backup directory containing the following files.

1. `metadata` - contains backup of leveldb
2. `boostd.db` - backup of deals database
3. `keystore` - directory containing libp2p keys
4. `token` - API token
5. `config` - directory containing all config files and `config.toml` link
6. `storage.json` - file containing storage details

Backup does not backs up the deal logs and dagstore.

### Online backup

You can take an online backup with the below command

```
boostd backup <backup directory>
```

The online backup supports running only once instance at a time and you might see a locking error if another instance of backup is already running.

### Offline backup

1. Shutdown `boostd` before taking a backup
2. Take a backup using the command line

```
boostd backup --offline <backup directory>
```

{% hint style="info" %}
Boost offline backup does not include Dagstore and user can copy dagstore directory to a backup location manually. Dagstore can be reinitialized if there is no backup.
{% endhint %}

## Restore

1. Make sure that `--boost-repo` flag is set if you wish to restore to a custom location. Otherwise, it will be restored to `~/.boost` directory
2. Restore the boost repo using the command line

```
boostd restore <backup directory>
```

{% hint style="info" %}
Once restore is complete, Dagstore can be manually copied inside the boost repo to restore it.
{% endhint %}
