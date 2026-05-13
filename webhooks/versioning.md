# Versioning

The webhook payload is versioned in lockstep with the
[Metrics Catalog](../reference/metrics-catalog.md).

## Version field

Every payload carries `schema_version` in the envelope. The current
version is `v1`.

## Compatibility guarantees within a major version

Within a single major version (e.g. all `v1.x`):

- **No breaking changes** — fields are not renamed, removed, or
  re-typed.
- **Additive only** — new metrics and new groups may be introduced.
  Consumers must ignore unknown fields.
- **Nullability is stable** — a field documented as non-nullable in
  v1.0 will remain non-nullable for the rest of v1.x.

## Breaking changes (`v1` → `v2`)

A new major version means at least one of:

- A field was renamed, removed, or re-typed.
- A field's nullability tightened.
- The envelope shape changed.

When a major bump is planned:

1. The new version is announced in the [changelog](../reference/changelog.md)
   at least **30 days** before the cutover.
2. During the transition window, both `v1` and `v2` deliveries are
   available — subscribers opt in to `v2` per subscription.
3. After the window, `v1` deliveries stop. Existing `v1` subscriptions
   are auto-migrated to `v2` only if no fields they depend on changed
   meaning; otherwise they are paused and surfaced for manual
   migration.

## Pinning

Subscriptions can pin to a specific schema version on creation:

```json
{ "schema_version": "v1", "...": "..." }
```

Omitting the field opts you in to the latest stable version.
