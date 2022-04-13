---
description: Frequently asked questions about Boost
---

# FAQ

**Is there a way to stop `boostd` daemon?**\
You can use the regular Unix OS signals

**Is Boost compatible with the Lotus client? Can a client use `lotus client deal` to send a deal to Boost storage providers or do they have to use the boost client?**\
Yes, boost should work with any client given it supports the storage market protocol / default standard of Filecoin network today.

**Does Boost provide retrieval functionality or a client still need to use the Lotus client for retrievals?**\
Boost doesn't change anything about retrieval so far. We are working on new retrieval options.

**Can Boost make verified deals?**\
Yes, payments for deals can be made either from a regular wallet, or from DataCap. Deals that are paid for with DataCap are called `verified` deals.

**Can I run both Boost and markets at the same time?**\
****Boost replaces the legacy markets process. See [migrate-a-lotus-markets-service-process-to-boost.md](upgrade-from-lotus-to-boost/migrate-a-lotus-markets-service-process-to-boost.md "mention")
