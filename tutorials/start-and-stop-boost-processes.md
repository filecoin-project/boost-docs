---
description: >-
  This pages describes the order in which different boost processes must be
  started and stopped
---

# Start and Stop Boost processes

## Start

1. Start YugabyteDB and wait for services to come online.
2. Start `boostd-data` process.
3. Start `boostd` process.
4. Start `booster-http` and `booster-bitswap` processes.

## Stop

1. Stop `booster-http` and `booster-bitswap` processes.
2. Stop `boostd` process.
3. Stop `boostd-data` process.
4. Finally, you can stop YugabyteDB services.



{% hint style="info" %}
YugabyteDB services are not required to be stopped if your maintenance work does not directly make changes to the DB itself. The DB can simply be disconnected from boost process by shutting down the `boostd-data` process.
{% endhint %}
