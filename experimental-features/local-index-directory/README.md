---
description: >-
  This page describes the Local Index Directory component in Boost, what it is
  used for, how it works and how to start using it
---

# Local Index Directory

{% hint style="warning" %}
Local Index Directory is not yet released. This is a placeholder page for its documentation.
{% endhint %}

## Background

The Local Index Directory (_LID_) manages and stores indices of deal data so that it can be retrieved by a content identifier (_cid_).

Currently this task is performed by the _DAG store_ component. The DAG store keeps its indexes on disk on a single machine. LID replaces the DAG store and introduces a horizontally scalable backend database for storing the data - YugabyteDB.

LID is designed to provide a more intuitive experience for the user, by surfacing problems and providing various repair tools.

To summarize, LID is the component which keeps fine-grained metadata about all the deals on Filecoin that a given Storage Provider stores, and without it client would only be able to retrieve full pieces, which generally are between 8GiB and 32GiB in size.

## Storing data on Filecoin

When a client uploads deal data to Boost, LID records the sector that the deal data is stored in and scans the deal data to create an index of all its blocks indexed by block cid. This way cilents can later retrieve subsets of the original deal data, without retrieving the full deal data.

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-18 at 13.39.53.png" alt=""><figcaption><p>How Boost stores deal data from clients</p></figcaption></figure>

## Retrieving data

When a client makes a request for data by cid, LID:\
\- checks which piece the cid is in, and where in the piece the data is\
\- checks which sector the piece is in, and where in the sector the piece is\
\- reads the data from the sector

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-18 at 13.45.14.png" alt=""><figcaption><p>How clients retrieve their data from Boost</p></figcaption></figure>

## Use cases

The retrieval use cases that the Local Index Directory supports are:

#### Graphsync retrieval

_Request one root cid with a selector, receive many blocks_

LID is able to:\
\- look up which piece contains the root cid\
\- look up which sector contains the piece\
\- for each block, get the offset into the piece for the block

#### Bitswap retrieval

_Request one block at a time_

LID is able to:\
\- look up which piece contains the block\
\- get the size of the block (Bitswap asks for the size before getting the block data)\
\- look up which sector contains the piece\
\- get the offset into the piece for the block

#### HTTP retrieval

_Request a whole piece_

LID is able to look up which sector contains the piece.

_Request an individual block_

LID is able to:\
\- look up which piece contains the block\
\- look up which sector contains the piece\
\- get the offset into the piece for the block

_Request a file by root cid_

LID is able to:\
\- look up which piece contains the block\
\- look up which sector contains the piece\
\- for each block, get the offset into the piece for the block
