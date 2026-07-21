# Nonstandard Client Support

A default node exposes a standard set of RPC namespaces on a standard client build. With **Nonstandard Client Support**, this add-on handles exceptions: extra namespaces, a private or patched client, or an exotic configuration while we operate and manage the infrastructure for you

### How it works

* We enable non-default namespaces, such as `debug`, `trace`, `txpool`, or `admin`, where the chain supports them.
* We run a specific client version, a private fork, or a patched build.
* We apply custom flags, such as an archive sync mode or a custom tracer configuration.
* We manage upgrades, monitoring, and incident response.

### Use Cases

* You run MEV or search workloads that need `txpool` access and custom tracers.
* Your security or forensics work needs `debug_traceTransaction` with a custom tracer.
* You need a patched client, a private fork, or a niche chain client that no shared node runs.

### When you can skip it

* The default namespaces and client build cover your calls.

{% hint style="info" %}
This add-on costs more than the others because it needs bespoke operations, not a config toggle.
{% endhint %}
