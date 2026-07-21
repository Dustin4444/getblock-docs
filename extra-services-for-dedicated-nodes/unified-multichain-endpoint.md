# Unified Multichain Endpoint

Traditionally, each chain gives you a separate endpoint and a separate key. With a Unified Multichain Endpoint, you get a single base endpoint that lets you interact with multichains at once. You select the chain with a subdomain, a path, or a chain-id parameter. You manage one URL template instead of many.

### How it works

* You call one base endpoint and name the target chain in the request.
* One credential authenticates across all your chains.
* You set your rate limits and your IP allowlist once. The policy applies to every chain.
* Your client code holds one URL template, not one URL per network.

### Use Cases

* You build a cross-chain app, a wallet, or an aggregator.
* You run an indexer that reads many chains.
* You are tired of managing one endpoint and one key per network.

### When you can skip it

* You use a single chain and expect to stay on it.
