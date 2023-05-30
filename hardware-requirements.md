---
description: Hardware requirements for Storage Providers running Boost
---

# Hardware requirements

The hardware requirements for Boost are tied to the Lotus stack in a Filecoin SP deployment.

Depending on how much data you need to onboard, and how many deals you need to make with clients, hardware requirements in terms of _CPU_ and _Disk_ will vary.

## General hardware requirements

### CPU

A miner will need an **8+ core CPU**.

We strongly recommend a CPU model with support for _Intel SHA Extensions_: AMD since Zen microarchitecture, or Intel since Ice Lake. Lack of SHA Extensions results in a very significant slow down.

The most significant computation that Boost has to do is the _Piece CID calculation (also known as Piece Commitment or CommP)_. When Boost receives data from a client, it calculates the _Merkle root_ out of the hashes of the _Piece_ (padded .car file). The resulting root of the clean binary Merkle tree is the _Piece CID._

### RAM

**2 GiB of RAM** are needed at the very least.

### Disk

Boost stores all data received from clients before Piece CID is calculated and compared against deal parameters received from clients. Next, deals are published on-chain, and Boost waits for a number of epoch confirmations before proceeding to pass data to the Lotus sealing subsystem. This means that depending on the throughput of your operation, you must have disk space for at least a few staged sectors.

For small deployments **100 GiB of disk are needed at the very least** if we assume that Boost is to keep three 32 GiB sectors before passing them to the sealing subsystem.

We recommend using **NVME disk** for Boost. As Dagstore grows in size, the overall performance might slow down due to slow disk.

