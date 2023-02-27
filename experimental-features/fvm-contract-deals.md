---
description: >-
  This page explains how to start monitoring and accepting deals published
  on-chain on the FVM
---

# FVM Contract Deals

With the release of FVM, it is now possible for smart contracts to make deal proposals on-chain. This is made possible though the DealProposal FRC.

DataDAOs, as well as other clients who want to store data on Filecoin, can now deploy a smart contract on the FVM which adheres to the DealProposal FRC, and make deal proposals that are visible to every storage provider who monitors the chain.

## How to enable FVM monitoring in order to process storage deal proposals published on-chain?

Boost already has support for the DealProposal FRC.

The code for FVM monitoring resides in the `main` and `ntwk/hyperspace` branch of the Boost and should not be used for production use.

To build for mainnet:

```
git clone https://github.com/filecoin-project/boost.git
cd boost
make build
```

To build for hyperspacenet:

```
git clone https://github.com/filecoin-project/boost.git
cd boost
git checkout ntwk/hyperspace
make hyperspacenet
```

In order to enable DealProposal FRC, you have to edit your `config.toml` and enable contract deal monitoring. By default it is disabled. Here is an example configuration:

```
[ContractDeals]
  Enabled = true
  AllowlistContracts = []
  From = "0x0000000000000000000000000000000000000000"
```

`AllowlistContracts` field could be left empty if you want to accept deals from any client. If you only want to accept deals from some clients, you can specify their contract addresses in the field.

`From` field has to be set to your SP's FEVM address, in case you want to accept deals from clients that check and have allowlist for the SPs that can publish their deal proposals.
