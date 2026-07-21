# IP Allowlist

An Internet Protocol (IP) allowlist gives you the autonomy to define a list of IP addresses, which can be single IPs or Classless Inter-Domain Routing (CIDR) ranges, allowing requests and rejecting every request from an address that is not on your list.  The add-on also covers scoped keys and rotation policies.

### How it works

* The node accepts requests only from allowed IPs or CIDR blocks.
* You issue scoped keys. Each key gets the least access it needs.
* You rotate keys with an overlap window: create the new key, migrate your services, then revoke the old key. The service does not stop.
* An IP allowlist is stronger than a referrer check for server-to-server traffic. A client can spoof a referrer header. A client cannot easily spoof its source IP.

### Use Cases

* You work in a regulated field such as payments, custody, or exchange operations.
* Your backend runs from static egress IPs.
* You need to prove that only approved systems reach your node.

### When you can skip it

* Your traffic comes from end-user devices with changing IPs. An allowlist does not fit that shape. Use key auth and rate limits instead.
