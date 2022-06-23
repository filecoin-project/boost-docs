---
description: >-
  This section describes how to upgrade your lotus-miner markets service to
  boostd, as well as how to roll-back if you are not happy with boostd
---

# Migrate from Lotus to Boost

A storage provider can run the lotus as a monolith process where everything is handled by a single `lotus-miner` process or separate the mining and market subsystems on different machines.

Boost supports migration from both monolith and a split-market miner. You can follow the below guides to migrate to boost.

{% content-ref url="migrate-a-monolith-lotus-miner-to-boost.md" %}
[migrate-a-monolith-lotus-miner-to-boost.md](migrate-a-monolith-lotus-miner-to-boost.md)
{% endcontent-ref %}

{% content-ref url="migrate-a-lotus-markets-service-process-to-boost.md" %}
[migrate-a-lotus-markets-service-process-to-boost.md](migrate-a-lotus-markets-service-process-to-boost.md)
{% endcontent-ref %}

### **Rollback**

{% hint style="danger" %}
Please note that Boost uses a SQLite database for the deal metadata and logs. Once Boost has been enabled, the new deals cannot be rolled back to the Lotus market. If you decide to roll back after making Boost deals, you will lose all the metadata for the deal made with Boost. However, this will have no impact on the sealed data itself.
{% endhint %}

{% content-ref url="roll-back-to-legacy-markets.md" %}
[roll-back-to-legacy-markets.md](roll-back-to-legacy-markets.md)
{% endcontent-ref %}



\
