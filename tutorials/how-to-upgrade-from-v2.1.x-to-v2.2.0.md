---
description: This is a step by step guide to upgrade from Boost v2.1.x to Boost v2.2.0
---

# How to upgrade from v2.1.x to v2.2.0

Boost v2.2.0 enables support for Direct Data Onbording (DDO) and no longer support legacy deals in any form. These changes resulted in some breaking changes in the configuration. Please follow the below steps to upgrade.

1. Please save you current configuration to a file with `boostd config update --diff`
2. Pull the new stable release v2.2.0 or RC candidate v2.2.0-rcx
3. Rebuild the binaries with the new version
4. Stop `booster-http`, `booster-bitswap`, `boostd` and `boostd-data` in that order
5. Start `boostd-data` service first. If you are using systemd service files to manage the process, then please start it manually without using the systemd files.
6. Start `boostd` after upgrade and shutdown after a successful start
7. Review the `<boost repo>/config.toml` for configuration parameters saved in step 1 and update them if they have been reset to default values.
8. Start `boostd`, `booster-http` and `booster-bitswap` services.
