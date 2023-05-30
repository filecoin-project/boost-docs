---
description: Local Index Directory dependencies
---

# Dependencies

Local Index Directory depends on a backend database to store various indices. Currently we support two implementations - YugabyteDB or LevelDB - depending on the size of deal data and indices a storage provider holds.

**LevelDB** is an open source on-disk key-value store, and can be used when indices fit on a single host.

**YugabyteDB** is an open source modern distributed database designed to run in any public, private, hybrid or multi-cloud environment.

{% hint style="info" %}
Storage providers who hold more than 1PiB data are encouraged to use YugabyteDB as it is horizontally scalable, provides better monitoring and management utilities and could support future growth.
{% endhint %}
