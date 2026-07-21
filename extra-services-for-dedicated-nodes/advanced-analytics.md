---
description: >-
  A private Grafana workspace for your dedicated node. It shows how your node is
  used, in real time.
---

# Advanced Analytics

Advanced Analytics gives real-time Grafana dashboards for node health, request patterns, and cluster performance, and Alchemy tracks per-method compute unit consumption. It shows request counts, latencies, and errors.  It groups the data three ways:&#x20;

1. by RPC method
2. by access key
3. by region.&#x20;

You see the data as time-series charts, which gives you a more readable and understandable in-depth analysis. You can tell which requests consume more compute units(CU).

### How it works

* The dashboard tracks request volume, latency, and error rate.
* Latency shows as percentiles. You see p50, p95, and p99, not one average that hides the slow requests.
* The per-method view shows which calls cost you the most. `debug_traceTransaction` and `eth_getLogs` are heavy. This view proves it.
* The per-key view shows which client or service drives your traffic.
* The per-region view shows where your users connect from.

### Use Cases

* You want to attribute cost to a team, a customer, or a feature.
* You plan capacity and need real traffic data, not a guess.
* You debug a latency spike and need to find the method that caused it.
* You suspect one key sends abnormal traffic and you want proof.

### When you can skip it

* Your traffic is low and steady.
* The standard dashboard already answers your questions.
