# Dedicated Ingress Proxy

Your own routing layer. It sits in front of your node and serves only your traffic. Every request first reaches a proxy. The proxy handles TLS, authentication, rate limits, and routing. By default, this layer is shared. This add-on gives you a proxy that is isolated from all other customers.

### How it works

* Your requests pass through a proxy fleet that serves you alone.
* No other customer's traffic competes for the proxy at your gateway.
* You can apply your own rate-limit and routing rules.
* The isolated path gives you a predictable gateway latency, because a noisy neighbor cannot slow it down.

### When you need it

* Your app runs at high request rates, and the shared gateway is your limit.
* Your compliance rules require an isolated traffic path from edge to node.
* You need custom rate-limit or routing behavior that shared policy cannot give.

### When you can skip it

* Your traffic is moderate, and the shared gateway keeps up.
* You do not have an isolation requirement.
