---
description: YugabyteDB is the backend database that hosts the Local Index Directory
---

# YugabyteDB

YugabyteDB is used to store the retrievals indexes. When a client makes a retrieval request, LID service is used to lookup the requested data within the Lotus miner. For more details see the [architecture of LID](../boost-architecture/local-index-directory/).

Depending on the size of data your SP is holding, you can run [YugabyteDB](https://docs.yugabyte.com/preview/architecture/concepts/) in a variety of ways. You can find more information about how to run YugabyteDB [here](https://docs.yugabyte.com/preview/launch-and-manage/).

For production use, YugabyteDB can be deployed as a highly available (HA) cluster. The cluster can be deployed on a [Kubernetes instance](https://docs.yugabyte.com/preview/deploy/kubernetes/) or as a [manual deployment](https://docs.yugabyte.com/preview/deploy/manual-deployment/). Please ensure to read all the [pre-requisites](https://docs.yugabyte.com/preview/deploy/checklist/) before proceeding.

{% hint style="danger" %}
YugabyteDB must be backed up regularly to avoid loosing deal metadata. Once this data is lost, there is no reliable way to recover it.
{% endhint %}

You can then start `boostd-data` on `:8044` and connect it to the `yugabyte` with:

```
boostd-data run yugabyte --hosts 127.0.0.1 \
                         --connect-string="postgresql://postgres:postgres@127.0.0.1:5433" \
                         --addr 0.0.0.0:8044
```

{% hint style="info" %}
The PGX driver from Yugabyte supports cluster aware Postgres connection out of the box. If you are deploying a multi-node YugabyteDB cluster, then please update your connect-string to use a cluster aware connection.



With Cluster Mode: "postgresql://postgres:postgres@127.0.0.1:5433?load\_balance=true"\


With Cluster Mode + No SSL: "postgresql://postgres:postgres@127.0.0.1:5433?sslmode=disable\&load\_balance=true"
{% endhint %}

## YugabyteDB FAQ

1. **What is YugabyteDB and why YugabyteDB?**\
   YugabyteDB is a high-performance distributed SQL database. Built using a unique combination of high-performance document store, per-shard distributed consensus replication and multi-shard ACID transactions (inspired by Google Spanner), YugabyteDB serves both scale-out RDBMS and internet-scale OLTP workloads with low query latency, extreme resilience against failures and global data distribution.\
   \
   We tested multiple open source databases for the Boost use case and found YugabyteDB to be well suited as it is highly performant and scales well horizonally.\

2. **How do I learn about YugabyteDB and what do I need to know about YugabyteDB as an SP?**\
   SPs should familiarize themselves with the “[Deploy and Manage](https://docs.yugabyte.com/preview/launch-and-manage/)” section of the [documentation](https://docs.yugabyte.com/preview/) along with the [architecture](https://docs.yugabyte.com/preview/architecture/concepts/) before deploying YugabyteDB.\
   \
   YugabyteDB will also be utilized by Lotus V2 architecture as well. We plan to allow SPs to connect all of their Boost instances to a single LID (YugabyteDB) with Boost v2.1.0 release. This will also allow SPs to serve retrievals from any of their miners using a single `booster-http` or `booster-bitswap` process. \

3. **Which deployment should I choose?**\
   We recommend deploying YugabyteDB either locally on bare metal or using a managed deployment. Users can choose to deploy on the cloud if they can guarantee that scaling up the DB will not be impacted by the network bandwidth and infrastructure. It is recommended that YugabyteDB is highly available so that if one of the nodes is not available, your SP operations will not be impacted.\
   You can find some of [the example YugabyteDB deployment ](https://github.com/filecoin-project/boost/discussions/1797)here. Please feel free to add your experience and deployment details to the discussion.\

4. **Can I convert YugabyteDB processes to systemd services?**\
   YugabyteDB does not ship as a systemd service by default. You will need to create a new service based on the commands you are running to start YT-Master and YT-Server processes. These commands can be customized based on user requirements and infrastructure.\

5. **Once YugabyteDB is deployed, do I need to perform any additional steps?**\
   Ideally, once the deployment is complete and can be reached over the network by `boostd-data` service, users do not need to perform any additional steps. If you wish to change the default username/password, you must also update the same on the `--connect-string` of `boostd-data` service.

{% hint style="info" %}
You can find some of [the example YugabyteDB deployment ](https://github.com/filecoin-project/boost/discussions/1797)here. Please feel free to add your experience and deployment details to the discussion.
{% endhint %}

## YugabyteDB Maintenance and Upgrades

Boost is not currently tied to a specific version of YugabyteDB. We recommend setting up the latest stable version of the YugabyteDB when creating a new LID instance. Over the time, users can upgrade their YugabyteDB. When upgrading:

1. Boost-related processes must be stopped beforehand. If YugabyteDB is being utilised by other services apart from Boost then those services must be stopped as well.
2. Read through and follow the [upgrade guide](https://docs.yugabyte.com/preview/manage/upgrade-deployment/) and reach out to [YugabyteDB team on Slack](https://communityinviter.com/apps/yugabyte-db/register) if you have any questions.

As per the industry best practices, YugabyteDB should be [regularly backed up](https://docs.yugabyte.com/preview/manage/backup-restore/) for redundancy. Check out [YugabyteDB docs](https://docs.yugabyte.com/preview/) if you need any help with management of the database.

## YugabyteDB Troubleshooting

If you require help with YugabyteDB, we recommend checking out the [troubleshooting guide](https://docs.yugabyte.com/preview/troubleshoot/). If the problems is not resolved then users should reach out to YugabyteDB team via [Slack](https://communityinviter.com/apps/yugabyte-db/register) or [Github](https://github.com/yugabyte/yugabyte-db) for any support. You can also reach out to other users in #boost-help channel of filecoin slack to seek help from your peers.
