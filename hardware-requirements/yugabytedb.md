---
description: Hardware requirements for YugabyteDB
---

# YugabyteDB

{% hint style="info" %}
For detailed instructions, playbooks and hardware recommendations, see the YugabyteDB website - [https://docs.yugabyte.com](https://docs.yugabyte.com)
{% endhint %}

YugabyteDB is designed to run on bare-metal machines, virtual machines (VMs), and containers. CPU and RAM

You should allocate adequate CPU and RAM. YugabyteDB has adequate defaults for running on a wide range of machines, and has been tested from 2 core to 64 core machines, and up to 200GB RAM.

#### Minimum requirement

```
2 cores
2GB RAM
```

#### Production requirement

```
16+ cores
32GB+ RAM
Add more CPU (compared to adding more RAM) to improve performance.
```

#### Verify support for SSE2 and SSE4.2

YugabyteDB requires the SSE2 instruction set support, which was introduced into Intel chips with the Pentium 4 in 2001 and AMD processors in 2003. Most systems produced in the last several years are equipped with SSE2.

In addition, YugabyteDB requires SSE4.2.

To verify that your system supports SSE2, run the following command:

`cat /proc/cpuinfo | grep sse2`

To verify that your system supports SSE4.2, run the following command:

`cat /proc/cpuinfo | grep sse4.2`

#### Disks

```
SSDs (solid state disks) are required.
```

It is recommend that a minimum of 1TiB or more is allocated for YugabyteDB, depending on the amount of deal data you store and its average block size.

{% hint style="warning" %}
Assuming you've kept unsealed copies of all your data and have consistently indexed deal data, the size of your DAG store directory should be comparable with the requirements for YugabyteDB.
{% endhint %}
