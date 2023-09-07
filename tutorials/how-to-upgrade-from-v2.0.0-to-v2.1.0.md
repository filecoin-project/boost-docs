---
description: This is a step by step guide to upgrade from Boost v2.0.0 to Boost v2.1.0
---

# How to Upgrade from v2.0.0 to v2.1.0

Boost v2.1.0 supports connecting multiple miners to a single LID instance. This would enable the SP to serve retrievals from all the connected miners via a single `boostd`, `booster-bitswap` or `booster-http` instance. To enable this feature, we need to update our existing LID tables to add minerID to the piece metadata.

1. Pull the new stable release v2.1.0 or RC candidate v2.1.0-rcx
2. Rebuild the binaries with the new version
3. Stop `booster-http`, `booster-bitswap`, `boostd` and `boostd-data` in that order
4. Start `boostd-data` service first. If you are using systemd service files to manage the process, then please start it manually without using the systemd files.

{% hint style="warning" %}
The Postgres \<CONNECT\_STRING> will need to be updated to not use SSL mode. Otherwise, you might see error connecting to the DB. The updated connect string should like below

\
`postgresql://<username>:<password>@<yugabytedb>:5433?sslmode=disable`
{% endhint %}

6. Once `boostd-data` service starts, it will throw the below error and quit the process

{% code overflow="wrap" %}
```
You must set the miner-address flag to do the migration. Set it to the address of the storage miner eg f1234
```
{% endcode %}

7. If you do not see the above error and process does not exit, then you do not require a migration. At this point, please skip to step 9.
8. Run the one time migration.

{% code overflow="wrap" %}
```
boostd-data run yugabyte-migrate --hosts <HOSTS> --connect-string <CONNECT-STRING> --miner-address <MinerID of connected miner>
```
{% endcode %}

{% hint style="info" %}
Please ensure to use the minerID of already connected miner. Other miners can only be connected once the migration is complete.
{% endhint %}

9. Once the migration is finished (few seconds to 2 minutes), SPs using systemd to maintain `boostd-data` service should also update their `connect-string` in the systemd service files.
10. Start the `boostd-data` process (or service) and start your `boostd` instance. Go to the UI and confirm that you can see your minerID on the top left side of the page and the LID page is being populated correctly.
11. Start `booster-http` and `booster-bitswap` services.
12. If you wish to connected other miners to the upgraded LID service, then please follow the [migration guide for each miner](how-to-migrate-boost-v1-to-boost-v2.md).
