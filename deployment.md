---
description: >-
  Overview of Filecoin Storage Provider (SP) components and how Boost fits in an
  SP stack
---

# Components

| Component        | Description                                                               |
| ---------------- | ------------------------------------------------------------------------- |
| **lotus**        | Lotus Daemon following the Filecoin chain                                 |
| **lotus-miner**  | Lotus Miner handling sector storage, jobs scheduling, etc.                |
| **lotus-worker** | Lotus Worker handling various types of jobs (PC1, UNS, PC2, C1, DC, etc.) |

The **Lotus** stack is responsible for running a Filecoin full chain node, as well as handling sector lifecycle, sector sealing, sector proving. You can read more about the Lotus components at [https://docs.filecoin.io/storage-provider/architecture/lotus-components/](https://docs.filecoin.io/storage-provider/architecture/lotus-components/)

The **Boost** stack is responsible for handling storage and retrieval requests as well as indexing user data, so that clients can easily retrieve their data once it is store on the SP infrastructure

| Component           | Description                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| **boost**           | Boost client-side executable that can be used to send a deal proposal to a Boost Storage Provider.                                   |
| **boostd**          | Boost SP daemon handling storage deals processing, Boost UI server, GraphQL server, etc.                                             |
| **booster-http**    | Serve blocks and files over HTTP                                                                                                     |
| **booster-bitswap** | Serve blocks and files over Bitswap                                                                                                  |
| **boostd-data**     | Data service handling Boost SP daemon state, and providing interface to the Local Index Directory (hosted on YugabyteDB, or LevelDB) |
| **yugabyted**       | YugabyteDB backend for Local Index Directory                                                                                         |
