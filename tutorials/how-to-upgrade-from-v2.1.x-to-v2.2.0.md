---
description: This is a step by step guide to upgrade from Boost v2.1.x to Boost v2.2.0
---

# How to upgrade from v2.1.x to v2.2.0

Boost v2.2.0 enables support for Direct Data Onbording (DDO) and no longer support legacy deals in any form. These changes resulted in some breaking changes in the configuration. Please follow the below steps to upgrade.

1. Please save you current configuration to a file with `boostd config update --diff`
2. Note down the asking price setting from the `boostd` UI.
3. Pull the new stable release v2.2.0 or RC candidate v2.2.0-rcx
4. Rebuild the binaries with the new version
5. Stop `booster-http`, `booster-bitswap`, `boostd` and `boostd-data` in that order
6. Start `boostd-data` service first.&#x20;
7. Start `boostd` after upgrade and shutdown after a successful start
8. Review the `<boost repo>/config.toml` for configuration parameters saved in step 1 and update them if they have been reset to default values.
9. Start `boostd`, `booster-http` and `booster-bitswap` services.
10. Restore the asking price setting from UI.
