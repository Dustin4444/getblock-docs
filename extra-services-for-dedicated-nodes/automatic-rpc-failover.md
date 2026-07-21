# Automatic RPC failover

Automatic RPC failover is a safety net for your dedicated node. When your node is down or degraded, traffic moves to the GetBlock shared RPC. This Failover watches the health of your dedicated node at a set interval. The switch is automatic. Your endpoint URL does not change.

### How it works

* The system checks node health at a set interval.
* A node fails the check when it lags the chain head, returns errors, or exceeds a latency limit.
* On failure, traffic moves to the shared pool. No request is dropped.
* When the node recovers, traffic moves back.
* The shared pool applies its own rate limits. Failover keeps you online. It does not give you unlimited dedicated capacity during an outage.

### When you need it

* You run a production app that cannot drop requests.
* Your node needs maintenance windows or client upgrades.
* Your traffic sometimes spikes past your dedicated capacity.
* You have an uptime commitment to your own users.

### When you can skip it

* Your workload is a batch job that can retry later.
* A short gap in service does not affect your users.
