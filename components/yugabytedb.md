---
description: YugabyteDB is the backend database that hosts the Local Index Directory
---

# YugabyteDB

Depending on the size of data your SP is holding, you can run YugabyteDB in a variety of ways. You can find more information about how to run YugabyteDB [here](https://docs.yugabyte.com/preview/launch-and-manage/).

For production use, we highly recommend creating a 3 node cluster of YugabyteDB. The cluster can be deployed on a [Kubernetes instance](https://docs.yugabyte.com/preview/deploy/kubernetes/) or as a [manual deployment](https://docs.yugabyte.com/preview/deploy/manual-deployment/). Please ensure to read all the [pre-requisites](https://docs.yugabyte.com/preview/deploy/checklist/) before proceeding with any deployments as switching DBs after deployment is very risky.

For test purposes, you could run a Docker container with the following command:

```
docker run -d --name yugabyte \
           -p7000:7000 \
           -p9000:9000 \
           -p5433:5433 \
           -p9042:9042 \
           -v $(pwd)/yb-home/yb_data:/home/yugabyte/yb_data \
           yugabytedb/yugabyte:latest bin/yugabyted start \
           --base_dir=/home/yugabyte/yb_data \
           --daemon=false
```

Admin interface is available on http://localhost:7000

You can then start `boostd-data` on `:8044` and connect it to the `yugabyte` container with:

```
boostd-data run yugabyte --hosts 127.0.0.1 \
                         --connect-string="postgresql://postgres:postgres@127.0.0.1:5433" \
                         --addr 0.0.0.0:8044
```

