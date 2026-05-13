# Subscribing

!!! warning "Status: planned"

    Endpoints below are **provisional**. Field names, auth model, and
    URL paths may change before v1 ships. Track the
    [changelog](../reference/changelog.md) for breaking updates.

## Auth

Webhook subscriptions will be authenticated with a per-user **API key**
issued from the YummyBait dashboard. Pass it in the `Authorization`
header of every management request:

```
Authorization: Bearer ybt_live_…
```

## Create a subscription

```http
POST /v1/webhooks
Content-Type: application/json
Authorization: Bearer ybt_live_…
```

```json
{
  "url": "https://your-agent.example.com/yummybait",
  "wallets": ["0xabc…", "0xdef…"],
  "events": ["advice_cycle", "out_of_range"],
  "secret": "whsec_your_signing_secret"
}
```

Returns the created subscription with an `id`.

## Verifying webhook signatures

Every delivery includes an `X-YummyBait-Signature` header containing an
HMAC-SHA256 of the raw request body, keyed by the `secret` you
provided. Reject any request whose signature doesn't match.

## Retries

Failed deliveries (non-2xx, timeout >10s) will be retried with
exponential backoff for up to 24 hours, then dropped. A `replay`
endpoint will be available for manual re-delivery.

## Delete a subscription

```http
DELETE /v1/webhooks/{id}
Authorization: Bearer ybt_live_…
```
