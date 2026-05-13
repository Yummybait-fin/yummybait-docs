# Webhook Signals

!!! warning "Status: planned"

    The webhook delivery surface is **not yet shipped**. The
    [Metrics Catalog](../reference/metrics-catalog.md) is finalised at
    `v1` and already drives the bot's `/advice` LLM payload — webhook
    delivery will reuse the same schema. This page documents the
    planned subscription model so integrators can prepare.

YummyBait will let you subscribe **your own agent** to receive every
metric in the catalog whenever it would be sent to the LLM advisor.
Same data, same shape, no Telegram round-trip.

## Why use webhooks instead of `/advice`

- **Programmatic.** Route signals into your own automation, dashboards,
  or model.
- **No human in the loop.** No manual command invocation.
- **Same contract as the bot.** Every field you'd see in `/advice` is
  exactly what arrives in the payload — no parsing of bot messages.

## How it will work (planned)

1. Authenticate against the YummyBait API and register a webhook URL.
2. Choose which **wallets** you want signals for (yours, or any wallet
   you have read access to).
3. Choose a **cadence** — every advice cycle, OOR transitions only,
   etc.
4. Receive HTTP `POST` requests with the catalog-driven JSON payload.

See [Subscribing](subscribing.md) for the planned API, and
[Payload Schema](payload.md) for the message shape.
