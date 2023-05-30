# YugabyteDB

YugabyteDB is the backend database that hosts the Local Index Directory

Depending on the size of data your SP is holding, you can run YugabyteDB in a variety of ways. Find more information about how to run YugabyteDB at [https://docs.yugabyte.com/preview/launch-and-manage/](https://docs.yugabyte.com/preview/launch-and-manage/)

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
