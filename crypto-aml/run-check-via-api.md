# Run check via API

## Using the AML API

GetBlock Crypto AML screens crypto wallets and transactions for money-laundering and sanctions risk. Send an address or a transaction hash, get back a JSON risk report — a 0–100 score, the components that produced it, and attribution.

Two endpoints, one for each target:

| Target                                                        | Endpoint                    |
| ------------------------------------------------------------- | --------------------------- |
| [A wallet](run-check-via-api.md#screening-a-wallet)           | `POST /v1/aml/wallet-check` |
| [A transaction](run-check-via-api.md#screening-a-transaction) | `POST /v1/aml/tx-check`     |

Both are bearer-authenticated, billed per check from your prepaid balance, and return the report inside a `data` envelope.

### Authentication

The AML API uses bearer auth with a single token per account — the same token that powers UI billing.

```http
Authorization: Bearer YOUR_API_TOKEN
Content-Type: application/json
```

Generate a token in the dashboard: **API keys** → [account.getblock.io/settings/api-keys](https://account.getblock.io/settings/api-keys)

**Base URL:** `https://services.getblock.io`

### Supported networks

| Value | Network             |
| ----- | ------------------- |
| `eth` | Ethereum            |
| `trx` | TRON                |
| `btc` | Bitcoin             |
| `ltc` | Litecoin            |
| `bch` | Bitcoin Cash        |
| —     | TON _(coming soon)_ |

Values are case-insensitive. Any other value returns `400`:

```json
{ "data": null, "error": "Supported networks: bch, btc, eth, ltc, trx" }
```

***

## Screening a wallet

Covers every asset the address holds, in one report. Use it to check a counterparty before you transact, or to screen a wallet at onboarding.

#### Request

`POST /v1/aml/wallet-check`

| Field     | Type   | Required | Description                                                              |
| --------- | ------ | -------- | ------------------------------------------------------------------------ |
| `address` | string | yes      | Wallet address to screen. Must be valid for `network`.                   |
| `network` | string | yes      | See [supported networks](run-check-via-api.md#supported-networks) above. |

```bash
curl -X POST "https://services.getblock.io/v1/aml/wallet-check" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"address":"0x8589427373D6D84E98730D7795D8f6f8731FDA16","network":"eth"}'
```

#### Response

```json
{
  "data": {
    "overall_risk_score": 100,
    "risk_score": 100,
    "max_risk_score": 100,
    "max_risk_token": "eth",
    "balance": "101.80247578376705",
    "report_risk":  { "risk_score": 100 },
    "cluster_risk": { "risk_score": 100 },
    "reputation_risk": {
      "risk_score": 40.16,
      "risk_tags": [
        { "tag": "dex(de-fi)", "percent": 99.95 },
        { "tag": "hacking",    "percent": 0.05 }
      ]
    },
    "coins_risk": {
      "risk_score": 100,
      "risk_tags": [
        { "tag": "hacking",        "percent": 96.3 },
        { "tag": "sanction",       "percent": 1.96 },
        { "tag": "dex(de-fi)",     "percent": 1.73 },
        { "tag": "untrusted_vasp", "percent": 0.01 }
      ]
    },
    "fatf_flags": {
      "risk_score": 100,
      "flags": [
        { "direction": "out", "reason": "fpwmd",    "amount": 7, "risk_score": 100 },
        { "direction": "out", "reason": "hacking",  "amount": 5, "risk_score": 75 },
        { "direction": "in",  "reason": "phishing", "amount": 1, "risk_score": 75 }
      ]
    },
    "internal_flags": {
      "risk_score": 100,
      "flags": [
        { "direction": "in",  "reason": "100", "amount": 3,  "risk_score": 100 },
        { "direction": "out", "reason": "100", "amount": 17, "risk_score": 75 }
      ]
    },
    "token_flags": { "risk_score": 0 },
    "categories": ["blacklist-usdc", "blacklist-usdt"],
    "token_list": ["eth", "usdc"],
    "clusters": ["Ronin Bridge Exploiter", "OFAC SDN LAZARUS GROUP 13-09-2019"],
    "calculation_uid": "de08e914-d37a-4488-b5ca-df909136b0cb"
  }
}

```

| Field                | Type      | Description                                                     |
| -------------------- | --------- | --------------------------------------------------------------- |
| `overall_risk_score` | number    | The composite score, 0–100. **Your headline number.**           |
| `risk_score`         | number    | Base score before composition.                                  |
| `max_risk_score`     | number    | Highest score among tokens held.                                |
| `max_risk_token`     | string    | Which token that was.                                           |
| `balance`            | string    | Wallet balance. A string, not a number.                         |
| `cluster_risk`       | object    | Risk of the cluster the address belongs to.                     |
| `reputation_risk`    | object    | Historical exposure.                                            |
| `coins_risk`         | object    | Current-balance exposure.                                       |
| `report_risk`        | object    | Third-party reports.                                            |
| `fatf_flags`         | object    | FATF category matches.                                          |
| `internal_flags`     | object    | Directly flagged interactions.                                  |
| `token_flags`        | object    | Token-level flags.                                              |
| `owners`             | string\[] | Entities the address is attributed to.                          |
| `clusters`           | string\[] | Cluster memberships.                                            |
| `token_list`         | string\[] | Tokens the wallet holds.                                        |
| `categories`         | string\[] | Category slugs, including issuer blacklists (`blacklist-usdt`). |
| `calculation_uid`    | string    | Identifies this check. Store it with your decision.             |

Each `*_risk` and `*_flags` field is an object — read `cluster_risk.risk_score`, not `cluster_risk`. Only `overall_risk_score` and `risk_score` are guaranteed; treat the rest as optional.

**Read the blocks, not just the score.** In the example above the wallet is nearly empty and every block is `0` except `cluster_risk: 70` — the score comes entirely from the cluster it belongs to, which `owners` names as Tornado Cash.

***

## Screening a transaction

Screens one transferred asset within a transaction, with its senders and receivers.

#### Request

`POST /v1/aml/tx-check`

| Field     | Type   | Required | Description                                                    |
| --------- | ------ | -------- | -------------------------------------------------------------- |
| `tx`      | string | yes      | Transaction hash to screen.                                    |
| `network` | string | yes      | See supported networks above.                                  |
| `asset`   | string | no       | Which transferred asset to score. Defaults to the native coin. |

A transaction carries specific transfers — the native coin, a token, or both. Each has its own counterparties and is scored on its own, so `asset` selects which transfer to score:

| Network      | Native | Tokens         |
| ------------ | ------ | -------------- |
| Ethereum     | `eth`  | `usdt`, `usdc` |
| TRON         | `trx`  | `usdt`         |
| Bitcoin      | `btc`  | —              |
| Litecoin     | `ltc`  | —              |
| Bitcoin Cash | `bch`  | —              |

Omit `asset` and the native coin is screened. More tokens are in the pipeline.

**Name the asset you're screening** — a check on a USDT payment should carry `"asset": "usdt"`, so the report covers the transfer you actually care about.

```bash
curl -X POST "https://services.getblock.io/v1/aml/tx-check" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"tx":"YOUR_TX_HASH","network":"trx","asset":"usdt"}'
```

#### Response

```json
{
  "data": {
    "risk_score": 100,
    "addresses_in": {
      "0x1a2a1c938ce3ec39b6d47113c7955baa9dd454f2": 40,
      "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2": 34.85
    },
    "addresses_out": {
      "0x098b716b8aaf21512996dc57eb0615e2383e2f96": 100,
      "0x1a2a1c938ce3ec39b6d47113c7955baa9dd454f2": 40
    },
    "risk_tags": [
      { "tag": "dex(defi)",  "percent": 82.58 },
      { "tag": "exchange",    "percent": 9.39 },
      { "tag": "lending",     "percent": 4.35 },
      { "tag": "marketplace", "percent": 2.34 },
      { "tag": "hot_wallet",  "percent": 0.29 },
      { "tag": "mixer",       "percent": 0.15 },
      { "tag": "sanction",    "percent": 0.01 }
    ]
  }
}

```

.

***

### Reading the score

The headline number is 0–100, higher is riskier — `overall_risk_score` for wallets, `risk_score` for transactions.

Thresholds are your policy, not a verdict from the API. A common starting point: 0–24 proceed · 25–49 light review · 50–74 manual review · 75–100 block pending enhanced due diligence.

Scores move over time as a wallet keeps transacting. Store the score and the `calculation_uid` you acted on, so a decision can be reconstructed later.



{% hint style="info" %}
**How to use these scores**

Every check runs a deep analysis. We trace the wallet's transaction graph in both directions — inbound and outbound, across multiple hops — resolve cluster membership to establish who controls the funds, and score the wallet's history and its current holdings as separate questions. On top of that sit our own attribution work, machine learning models, and continuously updated sanctions and blacklist data. That is why a report tells you _where_ risk comes from, not just how much.

**The result is an estimate, and it moves.** Risk scoring is probabilistic by nature. The same address can score differently over time as it keeps transacting, as counterparties change, and as attribution and sanctions data are updated. A score is a considered judgement built from many weighted signals — not a fixed property of the address.

**Use it to decide how much scrutiny a case deserves.** The right question is rarely "is this wallet good or bad?" — it is "does this one need enhanced due diligence, or can it proceed?" Read the risk blocks, not just the headline number: a `100` driven by `cluster_risk` on a regulated exchange means something very different from a `100` driven by `coins_risk` and `fatf_flags`. Set your own thresholds, apply your own policy, and record the `calculation_uid` you acted on so the decision can be reconstructed later.

**KYT is coming.** We're building a Know Your Transaction tool — a visual graph of an address, its counterparties and their interconnections — for cases where you need to trace the origin of funds yourself. Stay tuned.
{% endhint %}

{% hint style="warning" %}
Risk scores are probabilistic and provided "as is." Use them to inform your own risk decisions — not as the sole basis for regulatory, legal or financial decisions.
{% endhint %}



***

**See also:** Using AML via the API · How risk scoring works · Using AML via the UI
