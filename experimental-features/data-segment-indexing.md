---
description: What is data segment indexing and how is affects storage providers
---

# Data Segment Indexing

A large majority of users onboard data onto the Filecoin network via an Aggregator, a third party combining small pieces of data into a singular large deal. Today the work done by aggregators is unverifiable and unprovable. The user relies on the Aggregator to perform the work correctly and at the same time, it is impossible to prove to a third party that a given piece of data was included in a deal which is a highly requested functionality for user-programmable data use cases.

[FRC 58](https://github.com/filecoin-project/FIPs/blob/master/FRCs/frc-0058.md) enables the data aggregators to produce a Proof of Data Segment Inclusion certifying proper aggregation of Client's data. The produced proof assures:

* an inclusion of Client's data within the on-chain deal
* the Client's data can be trivially discovered within the deal to enable retrieval
* malicious behaviour of an Aggregator or another user, whose data was aggregated, does not interfere with retrievability of Client's data

<figure><img src="../.gitbook/assets/Screenshot 2023-10-18 at 12.25.40 PM.png" alt=""><figcaption><p>An aggregated Filecoin piece</p></figcaption></figure>

This is a critical link in enabling and exposing small pieces of data to the FEVM ecosystem. In the majority of cases, small pieces of data undergo an aggregation process, combing them into a large deal for acceptance by a Storage Provider. Without the proposed proof, data within aggregated deals becomes a second class citizen in Filecoin ecosystem. A significant portion of the F(E)VM use-case is enabling the ability to process and reason about the data stored by Filecoin Storage Providers. The Proof of Data Segment Inclusion allows to apply this new capability on segments of data which are too small to be on-boarded in their own deals due to economic constraints.

### How to enable Data Segment Indexing on Storage Provider?

After upgrading to Boost v2.1.0-rc2 or higher, this feature is automatically enabled on the storage provider side.

### Retrievals for aggregated car deals

The attached index at the end of the aggregated cars allow Boost to index the aggregated deals correctly. Once the deals are indexed, client can retrieve any payload CIDs from that deal using one of the 3 available data transfer protocols.&#x20;
