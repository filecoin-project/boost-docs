---
description: >-
  This tutorial goes through the steps required to run our Docker monitoring
  setup to collect and visualize metrics for various Boost processes
---

# Setting up a monitoring stack for Boost

## Background <a href="#docs-internal-guid-09f21b17-7fff-fabd-2034-91b1f0465aa1" id="docs-internal-guid-09f21b17-7fff-fabd-2034-91b1f0465aa1"></a>

The monitoring stack we will use includes:

* Prometheus - collects metrics and powers dashboards in Grafana&#x20;
* Tempo - collects traces and powers traces search in Grafana with Jaeger
* Grafana - provides visualization tools and dashboards for all metrics and traces

Lotus and Boost are already instrumented to produce traces and stats for Prometheus to collect.

The Boost team also packages a set of Grafana dashboards that are automatically provisioned as part of this setup.

## Prerequisites

This setup has been tested on macOS and on Linux. We haven’t tested it on Windows, so YMMV.

All the monitoring stack containers run in Docker.

## Steps

1. ### Install Docker

We have tested this setup with Docker 20.10.23 on macOS and Ubuntu.

[https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)

2. ### DNS resolution for Prometheus

Update `extra_hosts` in `docker-compose.yaml` for `prometheus`, so that the Prometheus container can reach all its targets - `boostd`, `lotus-miner`, `booster-bitswap`, `booster-http`, etc.\
\
[https://github.com/filecoin-project/boost/blob/main/docker/monitoring/docker-compose.yaml#L54-L58](https://github.com/filecoin-project/boost/blob/main/docker/monitoring/docker-compose.yaml#L54-L58)

Depending on where your Filecoin processes (`boostd`, `lotus`, `lotus-miner`, `booster-bitswap`, etc.) are running, you need to confirm that they are reachable from Prometheus so that it can scrape their metrics.

By default the setup expects to find them within the same Docker network, so if you are running them elsewhere (i.e. on the \`host\` network), make sure to update the docker-compose file for the same.

Start the monitoring stack using docker-compose.

```
cd docker/monitoring
docker-compose up -d
```

3. ### Prometheus targets

Confirm that Prometheus targets are scraped at [http://localhost:9190](http://localhost:9190) Targets

![](https://lh4.googleusercontent.com/sUGTMb0S5lp2sog0DdG9ecSRmCPDtMSmyAIwQ-k-91Pw1IUahhCiudKXkmKUJTHajXrPMR6RGLMmFEU9szwRlvcf9B4DgAIBuAaLTPB7-XaGPvtCrg2A9XGdhDIaTxZYi5wdbpTLdpsC98YcrorNVA2R1K7NLlK2xlk6RJRZ0jJ6qZ28QW543N0Imsrbzg)

If you are running software firewall like \`ufw\`, you might need to modify your iptables and allow access from the Prometheus container / network to the Filecoin stack network, for example:

`sudo docker network inspect monitoring`\
`# note the Subnet for the network`\
`sudo ufw allow from 172.18.0.0/16`

4. ### Grafana dashboards

Go to Grafana at [http://localhost:3333](http://localhost:3333) and inspect the dashboards:

![](https://lh4.googleusercontent.com/Uat6-bsTHiMvzwm854YT2W9M7fN5uiqdvQ8oFs1WsWqn98716AO56VCWD8Pok98W019mrjZVRM6fFv2qZ\_hY18OR0GmCMa5eVR45mh0OT8ZbX47HT3\_lLhuEvNt3CRy9eJ-t2O0De2hf3UDOsYERCgSaDR\_atH9czc1gkdUbo6dDC5ZYP75xaLJugAo8VQ)
