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

`From` field should be set to your SP's FEVM address. Some clients may implement a whitelist which allows specific SPs to accept deal proposals from their contract. This field will help those clients identify your SP and match it to their whitelist.

### How contract deals work in Boost

1. A contract publishes a `DealProposalCreate` event on the chain.
2. Boost monitors the chain for such events from all the clients by default. When such an event is detected, we go and fetch the data for the deal.
3. Deal is then run through the basic deal validation filters like clients has enough funds, SP has enough funds etc.
4. Once deal passes the validation, we create a new deal handler in Boost and pass this deal for execution like other Boost deals.
