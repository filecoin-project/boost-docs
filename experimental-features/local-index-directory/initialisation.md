---
description: >-
  This page explains how to initialise LID and start using it to provide
  retrievals to clients
---

# Initialisation

Considering that the Local Index Directory is a new feature, Storage Providers should initialise it after upgrading their Boost deployments.

There are two ways a Storage Provider can do that:

1. **Migrate existing indices from the DAG store into LID**: this solution assumes that the Storage Provider has been keeping an _unsealed_ copy for every sector they prove on-chain, and has already indexed all their deal data into the DAG store.\
   \
   Typically index sizes for a given sector range between 100KiB up to 1GiB, depending on deal data and its blocks sizes. The DAG store keeps these indices in the repository directory of Boost under the `./dagstore/index` and `./dagstore/datastore` directories. This data should be migrated to LID with the `migrate-lid` utility.\

2. **Recreate indices for deal data based on unsealed copies of sectors**: this solution assumes that the Storage Provider has _unsealed copies_ for every sector they prove on-chain. If this is not the case, then the SP should first trigger an _unseal (UNS)_ job on their system for every sector that contains user data and produce an unseal copy.\
   \
   SPs can use the `boostd recover lid` utility to produce an index for all deal data within an unsealed sector and store it in LID so that they enable retrievals for the data. Depending on SPs deployment and where unsealed copies are hosted (NFS, Ceph, external disks, etc.) and the performance of the hosting system, producing an index for a 32GiB sector can take anywhere from a few seconds up to a few minutes, as the unsealed copy needs to be processed by the utility.

## Migrate existing indices from the DAG store into LID

TODO

## Recreate indices for deal data based on unsealed copies of sectors

TODO
