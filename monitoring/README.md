---
description: How to setup monitoring for Boost services
---

# Monitoring

Boost provides multiple metrics for monitoring the services and APIs. All the metrics are emitted in Prometheus format and can be used to monitor and identify bottlenecks.

The metrics endpoint for all Boost services is `/metrics`. The full list of metrics emitted by `boostd`, `booster-http` and `booster-bitswap` are here\<INSERTLINK>. The `boostd-data` (LID) metrics are separate from the other metrics and their list can be found here\<INSERTLINK>.

Apart from the prometheus metrics, all Boost services also provides tracing spans. These tracing spans can be useful to debug the bottleneck and low performance sections of the execution. You can enable tracing using `--tracing` flag.

{% hint style="info" %}
The default URL to export tracing is [http://tempo:14268/api/traces](http://tempo:14268/api/traces), which is not correct outside of a Kubernetes or docker environment. Users must set `--tracing-endpoint` flag to correct IP/Hostname pointing to their `tempo` instance.
{% endhint %}

By default, Boost ships a fully configured monitoring stack. This monitoring stack can be deployed on docker and allows storage provider to get started with monitoring in less than 10 minutes. We highly recommend using this stack to monitor your Boost services unless you are familiar with how to setup monitoring manually and create dashboards.
