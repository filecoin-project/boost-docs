---
description: How to backup and restore Boost
---

# Backup and Restore

### Backup

1. Shutdown `boostd` before taking a backup
2. Take a backup using the command line

```
boostd backup <backup directory>
```

{% hint style="info" %}
Boost backup does not include dagstore and user can copy dagstore directory to a bckup location manually. Dagstore can be reinitialized if there is no backup.
{% endhint %}

### Restore

1. Make sure that `--boost-repo` flag is set if you wish to restore to a custom location. Otherwise, it will be restored to `~/.boost` directory
2. Restore the boost repo using the command line

```
boostd restore <backup directory>
```

{% hint style="info" %}
Once restore is complete, dagstore can be manually copied inside the boost repo to restore it.
{% endhint %}
