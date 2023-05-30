---
description: Frequently asked questions about Boost
---

# FAQ

**Is there a way to stop `boostd` daemon?**\
You can use the regular Unix OS signals

**Is Boost compatible with the Lotus client? Can a client use `lotus client deal` to send a deal to Boost storage providers or do they have to use the boost client?**\
Yes, boost should work with any client given it supports the storage market protocol / default standard of Filecoin network today.

**Does Boost provide retrieval functionality?**\
Yes, Boost provides 3 protocols for retrievals as of now. By default, Boost has Graphsync retrieval enabled. SPs can run Bitswap and HTTP retrievals by running [`booster-bitswap`](retrieving-data-from-filecoin/bitswap-retrieval.md) and [`booster-http`](retrieving-data-from-filecoin/http-retrieval/) respectively.

**Does Boost client have retreival functionality?**\
Yes, Boost client supports retrieval over graphsync protocol. But we highly recommend, using [`Lassie`](https://docs.filecoin.io/basics/how-retrieval-works/basic-retrieval/#lassie) client for Filecoin/IPFS retrievals.

**Can Boost make verified deals?**\
Yes, payments for deals can be made either from a regular wallet, or from DataCap. Deals that are paid for with DataCap are called `verified` deals.

**Can I run both Boost and markets at the same time?**\
No, Boost replaces the legacy markets process. See [migrate-a-lotus-markets-service-process-to-boost.md](tutorials/upgrade-from-lotus-to-boost/migrate-a-lotus-markets-service-process-to-boost.md "mention")
