---
description: This page details the Boost data service - boostd-data
---

# boostd-data

The `boostd-data` service is a proxy to the underlying backend database that hosts the Local Index Directory (LID). Currently there is support for two stores:

* LevelDB
* YugabyteDB

## Usage

```
boostd-data run yugabyte --hosts <HOSTS> \
                         --connect-string <CONNECT-STRING> \
                         --addr <LISTEN-ADDR>
```

We recommend creating a systemd file for this service and utilising it to easily start and stop the `boostd-data` service. Please note that this service is an independent process and is not controlled by any other Boost process.

{% hint style="info" %}
The PGX driver from Yugabyte supports cluster aware Postgres connection out of the box. If you are deploying a multi-node YugabyteDB cluster, then please update your connect-string to use a cluster aware connection.



With Cluster Mode: "postgresql://postgres:postgres@127.0.0.1:5433?load\_balance=true"\


With Cluster Mode + No SSL: "postgresql://postgres:postgres@127.0.0.1:5433?sslmode=disable\&load\_balance=true"
{% endhint %}

{% hint style="info" %}
\--hosts takes the IP addresses of the YugabyteDB YT-Servers.\
Example:

&#x20;\--hosts=10.0.0.1 --hosts=10.0.0.2 --hosts=10.0.0.3

\
\--addr is the \<IP>:\<PORT> where `boostd-data` service should be listening on. The IP here can be a private one (recommended) and should reachable by all boost related processes. Please ensure to update your firewall configuration accordingly.
{% endhint %}
