---
description: Local Index Directory architecture and index types
---

# Architecture

When designing the Local Index Directory we considered the needs of various Storage Providers (SPs) and the operational overhead LID would have on their systems. We built a solution for:\
&#x20;small- SPs - holding up to 1PiB), and\
\- mid- and large- size SPs - holding anywhere from 1PiB, up to 100PiB data

Depending on underlying block size and data format, index size can vary in size. Typically block sizes are between 16KiB and 1MiB.

At the moment there are two implementations of LID:\
\- a simple LevelDB implementation, for small SPs who want to keep all information in a single process database.\
\- a scalable YugabyteDB implementation, for medium and large size SPs with tens of thousands of deals.

## Index types

In order to support the described retrieval use cases, LID maintains the following indexes:

#### multihash → \[]piece cid

To look up which pieces contain a block

#### piece cid → sector information {sector ID, offset, size}

To look up which sector a piece is in

#### piece cid → map\<mulithash → block offset / size>

To look up where in the piece a block is and the block’s size

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-18 at 14.01.13.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-18 at 14.01.23.png" alt=""><figcaption></figcaption></figure>
