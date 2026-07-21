# Zero-hop Node Access

Zero-hop Node Access connects you to your node directly by removing the proxy layer entirely. Removing the hurdles of a normal request passing through the gateway and the load balancer before it reaches the node. With Zero-hop Node Access, this reduces the latency and variance that each layer adds.

### How it works

* You connect straight to your dedicated node, not through the shared routing layer.
* You always reach the same node. This gives you a consistent view of the mempool and chain state, which matters for trading.
* The direct path lowers latency and makes it more predictable.

{% hint style="warning" %}
The trade-off is clear. You give up the automatic failover and load balancing that the proxy layer provides. For the best result, pair Zero-Hop with a node in the region closest to your systems.
{% endhint %}

### Use Cases

* You run a high-frequency trading bot or an arbitrage engine.
* You run an MEV searcher and every millisecond changes your outcome.
* You send transactions where a consistent node view decides success.

### When you can skip it

* Your app reads and writes at a normal pace.
* Reliability matters to you more than the last few milliseconds. In that case, keep the proxy and its failover.
