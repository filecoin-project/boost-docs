---
description: >-
  Overview of all components and how Boost fits in a Filecoin Storage Provider
  (SP) deployment
---

# Components

| Component           | Description                                                                                                                |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| **lotus**           | Lotus Daemon following the Filecoin chain                                                                                  |
| **lotus-miner**     | Lotus Miner handling sector storage, jobs scheduling, etc.                                                                 |
| **lotus-worker**    | Lotus Worker handling various types of jobs (PC1, UNS, PC2, C1, DC, etc.)                                                  |
| **boostd**          | Boost Daemon handling storage deals processing, Boost UI server, GraphQL server, etc.                                      |
| **booster-http**    | Serve blocks and files over HTTP                                                                                           |
| **booster-bitswap** | Serve blocks and files over Bitswap                                                                                        |
| **boostd-data**     | Data service handling Boost state, and providing interface to the Local Index Directory (hosted on YugabyteDB, or LevelDB) |
| **yugabyted**       | YugabyteDB backend for Local Index Directory                                                                               |
