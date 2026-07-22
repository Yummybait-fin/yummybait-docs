# Versioning

The metric surface you write rules against — and the response envelope —
are versioned in lockstep with the
[Metrics Catalog](../reference/metrics-catalog.md). The current version
is `v1`.

## Compatibility guarantees within a major version

Within a single major version (e.g. all `v1.x`):

- **No breaking changes** — fields are not renamed, removed, or
  re-typed.
- **Additive only** — new metrics and new groups may be introduced.
  Your rules and response parsing must ignore unknown fields.
- **Nullability is stable** — a field documented as non-nullable in
  v1.0 stays non-nullable for the rest of v1.x.

Because rules are validated against the catalog at request time, a rule
that references a field which exists in `v1.x` keeps compiling for the
life of `v1.x`.

## Breaking changes (`v1` → `v2`)

A new major version means at least one of:

- A field was renamed, removed, or re-typed.
- A field's nullability tightened.
- The request or response envelope shape changed.

When a major bump is planned:

1. The new version is announced in the
   [changelog](../reference/changelog.md) at least **30 days** before
   the cutover.
2. During the transition window both `v1` and `v2` are served, and you
   choose per request (see [Pinning](#pinning)).
3. After the window, `v1` stops. Rules that only reference fields whose
   meaning is unchanged keep working against `v2`; rules referencing a
   changed field start returning a compile error in `rule_errors` so you
   can fix them.

## Pinning

Because every request is self-contained, you pin a version **per
request** rather than per subscription — there is no stored
subscription to migrate. Until a second major version ships there is
nothing to pin: all requests are served as `v1`. The pinning mechanism
will be documented here when `v2` enters its transition window.
