---
description: Local Index Directory index types
---

# Index types

In order to support the described retrieval use cases, LID maintains the following indexes:

#### multihash → \[]piece cid

To look up which pieces contain a block

#### piece cid → sector information {sector ID, offset, size}

To look up which sector a piece is in

#### piece cid → map\<mulithash → block offset / size>

To look up where in the piece a block is and the block’s size

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-18 at 14.01.13.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-18 at 14.01.23.png" alt=""><figcaption></figcaption></figure>
