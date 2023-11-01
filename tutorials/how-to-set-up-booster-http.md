---
description: >-
  Steps to configure booster-http and monitoring for retrievals using
  booter-http
---

# How to set up booster-http

## Security Considerations

1. We highly recommend running a reverse proxy solution (Ex: Ngnix) to safeguard your booster-http instance.
2. We also recommend running your booster-http instance on a private interface (ListenAddress). You can redirect the traffic from your public address (reverse proxy) to this private interface.
3. In case you are planning to run the reverse proxy on the same server as `booster-http` then, we would recommend running `booster-http` on `localhost`

## Prerequisites

1. Go to your Boost UI and find a deal that has finished sealing. The status should be "IndexedAndAnnounced".
2. Open the deal detail page and click on the PieceCID. It will redirect you to LID page.
3. Copy the data root CID and PieceCid from the LID page. We will not use this CID to fetch the data at different stages of the setup.

## Setup booster-http

1. [Build the `booster-http` binary and start a new `booster-http` process](../retrieving-data-from-filecoin/http-retrieval.md#local-setup) with additional flag `--serve-files` to start serving files.
2. Try to fetch the [piece](../retrieving-data-from-filecoin/http-retrieval.md#retrieving-a-full-filecoin-piece), [car](../retrieving-data-from-filecoin/http-retrieval.md#retrieving-a-car-file) and [block](../retrieving-data-from-filecoin/http-retrieval.md#retrieving-ipld-blocks). We will not try to fetch a file at this point.
3. Protect your booster-http with a reverse proxy (Ex: [Ngnix](../retrieving-data-from-filecoin/serving-files-with-booster-http.md#protecting-booster-http-with-nginx)).
4. Try to fetch the [piece](../retrieving-data-from-filecoin/http-retrieval.md#retrieving-a-full-filecoin-piece), [car](../retrieving-data-from-filecoin/http-retrieval.md#retrieving-a-car-file) and [block](../retrieving-data-from-filecoin/http-retrieval.md#retrieving-ipld-blocks). Please make sure that your are using the address and port exposed by the reverse proxy and retrieval is done from internet (not local network).
5. Make sure that `booster-http` is not directly accessible from internet.
6. Use a browser to try to browse all files in the CAR archive. Please use the address and port exposed by the reverse proxy like `https://<address>:port/ipfs/<data root cid>/`
7. At this point, our `booster-http` instance is ready to serve the data.
8. If you are going to use a domain name instead of an IP, then please edit your domain configuration at this stage. Your domain should get redirected to the public address and port of the reverse proxy.
9. Try to fetch all types of data and browse the car archive using your domain name. You can skip this step if you are not planning to use a domain name.
10. Now, [we are going to update our `boostd` config to start advertising](../retrieving-data-from-filecoin/http-retrieval.md#making-http-retrieval-discoverable) the fact that we are serving the data over HTTP and how can client access the data over HTTP.
11. Install docker on a machine that you will use for monitoring purpose.
12. [Setup the monitoring](../monitoring/setting-up-a-monitoring-stack-for-boost.md) and go to the Grafana dashboards for HTTP retrievals.
13. One last time, fetch the data and browse a car archive using a web browser. You should see the retrievals in the monitoring dashboards.

