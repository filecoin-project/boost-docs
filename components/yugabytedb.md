---
description: YugabyteDB is the backend database that hosts the Local Index Directory
---

# YugabyteDB

Depending on the size of data your SP is holding, you can run [YugabyteDB](https://docs.yugabyte.com/preview/architecture/concepts/) in a variety of ways. You can find more information about how to run YugabyteDB [here](https://docs.yugabyte.com/preview/launch-and-manage/).

For production use, we highly recommend creating a highly available (HA) cluster of YugabyteDB. The cluster can be deployed on a [Kubernetes instance](https://docs.yugabyte.com/preview/deploy/kubernetes/) or as a [manual deployment](https://docs.yugabyte.com/preview/deploy/manual-deployment/). Please ensure to read all the [pre-requisites](https://docs.yugabyte.com/preview/deploy/checklist/) before proceeding with any deployments as switching DBs after deployment is very risky.

{% hint style="danger" %}
We DO NOT recommend using the \`yugabyted\` command to create a production grade cluster for LID. The command is only meant to be used as a learning tool.
{% endhint %}

You can then start `boostd-data` on `:8044` and connect it to the `yugabyte` with:

```
boostd-data run yugabyte --hosts 127.0.0.1 \
                         --connect-string="postgresql://postgres:postgres@127.0.0.1:5433" \
                         --addr 0.0.0.0:8044
```

## YugabyteDB FAQ

1. **What is YugabyteDB and why YugabyteDB?**\
   YugabyteDB is a high-performance distributed SQL database. Built using a unique combination of high-performance document store, per-shard distributed consensus replication and multi-shard ACID transactions (inspired by Google Spanner), YugabyteDB serves both scale-out RDBMS and internet-scale OLTP workloads with low query latency, extreme resilience against failures and global data distribution.\
   We tested multiple open source DBs for our use case and found YugabyteDB to be perfectly suited in terms of scalability and performance.\

2. **What do I need to know about YugabyteDB as an SP?**\
   YugabyteDB will be utilized by Lotus V2 architecture as well. We plan to allow SPs to connect all of their Boost instances to a single LID (Yugabyte) DB with Boost v2.1.0 release. This will also allow SPs to serve retrievals from any of their miners using a single booster-http or booster-bitswap process. \

3. **Which deployment should I choose?**\
   We recommend deployed either locally on bare metal or using a managed deployment. Users can choose to deploy on the cloud if they can guarantee that scaling up the DB will not be impacted by the network bandwidth and infrastructure. We recommend a local deployment for security reasons as well. As your deal making will scale, so will the DB requirements. We recommend that your DB is highly available so even if one of the DB nodes is not available, your operations are not impacted.\
   Users should enable Cassandra and PostgreSQL APIs on the DB(enabled by default).\

4. **How much time do I need to learn YugabyteDB and how do I learn it?**\
   We highly recommend SPs to spend a few hours reading through the YugabyteDB docs. We expect SPs to familiarize themselves with the “[Deploy and Manage](https://docs.yugabyte.com/preview/launch-and-manage/)” section of the [documentation](https://docs.yugabyte.com/preview/) along with the [architecture](https://docs.yugabyte.com/preview/architecture/concepts/) before running a single command to deploy the DB.\

5. **Can I convert YugabyteDB processes to systemd services?**\
   YugabyteDB does not ship as a systemd service by default. You will need to create a new service based on the commands you are running to start YT-Master and YT-Server processes. These commands can be customized based on user requirements and infrastructure.\

6. **Once the DB is deployed, do I need to perform any additional steps?**\
   Ideally, once the deployment is complete and DB can be reached over the network by \`boostd-data\` service, users do not need to perform any additional steps. If you wish to change the default username/password for SQL, you must also update the same on the connect string of \`boostd-data\` service.

## YugabyteDB Maintenance and Upgrades

Boost is not currently tied to a specific version of YugabyteDB. We recommend setting up the latest stable version of the YugabyteDB when creating a new LID instance. Over the time, users can decide to upgrade their YugabyteDB. When upgrading, please remember a few key points:

1. To upgrade YugabyteDB, all boost related process must be stopped beforehand. If YugabyteDB is being utilised by other services apart from Boost then those services must be stopped as well.
2. Read through the [upgrade guide](https://docs.yugabyte.com/preview/manage/upgrade-deployment/) thoroughly and reach out to [Yugabyte team on slack](https://communityinviter.com/apps/yugabyte-db/register) if you have any questions.

As per the industry best practices, YugabyteDB should be [regularly backed up](https://docs.yugabyte.com/preview/manage/backup-restore/) for redundancy. [YugabyteDB docs](https://docs.yugabyte.com/preview/) are your friend and first source of information if you need any help with DB management.

## YugabyteDB Troubleshooting

If you require help with YugabyteDB, we recommend checking out the YugabyteDB [troubleshooting guide](https://docs.yugabyte.com/preview/troubleshoot/) within their documentation. If the problems is not resolved then users should reach out to Yugabyte team via [Slack](https://communityinviter.com/apps/yugabyte-db/register) or [Github](https://github.com/yugabyte/yugabyte-db) for any support. You can also reach out to other users in #boost-help channel of filecoin slack to seek help from your peers.
