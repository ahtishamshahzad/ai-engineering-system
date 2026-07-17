---
name: realtime-communication
description: Use to decide whether realtime is needed and design it — SSE vs WebSockets vs polling, authentication on connect, room/channel authorization, reconnection with missed-event recovery, and multi-instance fan-out.
---

# Realtime Communication

## Purpose

Evaluate whether the product needs realtime push at all, choose the lightest transport that serves it (polling → SSE → WebSockets), and design connection auth, channel authorization, reconnection, and scaling.

## When to Use

- For live updates: notifications, chat, presence, collaborative state, progress feeds.
- **Not** when periodic refetch serves the UX — evaluate first; realtime is operationally expensive.

## Inputs

- Realtime use cases with direction (server→client only vs bidirectional), frequency, and staleness tolerance.
- Auth model (`backend-authentication`, `backend-authorization`), deployment shape.

## Discovery Questions

- What staleness is actually acceptable per feature (30s polling often passes)?
- Server-push only (SSE fits) or client→server messages too (WebSockets)?
- What may each user subscribe to — and what proves it (`ownership-authorization`)?
- Multiple instances — how do events reach connections on other nodes?

## Responsibilities

- Make the transport decision explicit: **polling** (simplest, often enough), **SSE** (server→push, HTTP-native, auto-reconnect), **WebSockets** (bidirectional, most infrastructure); managed providers vs self-hosted as a stack decision.
- Authenticate the connection at establishment (cookie/token at handshake) and re-verify on sensitive subscriptions; connections outlive tokens — define expiry behavior (disconnect or re-auth).
- Authorize **per channel/room at subscribe time**: channel names are claims ("order-123's channel") that must pass the same ownership/tenant checks as REST access to that resource.
- Design reconnection: client backoff, and **missed-event recovery** (event IDs + replay window, or refetch-on-reconnect) — a reconnect that silently loses events is a data bug.
- Scale across instances: pub/sub backplane (e.g. Redis) so events reach every node's connections; sticky sessions where the transport needs them.
- Keep realtime as a **delivery channel, not the source of truth**: state changes commit to the database first; events notify (`../../database/transactions` ordering).

## Required Workflow

1. Record the fit decision per use case (polling/SSE/WebSockets), with staleness reasoning.
2. Design handshake auth + token-expiry behavior.
3. Design channel model + subscribe-time authorization (negative tests: cross-user/cross-tenant subscribe rejected).
4. Design reconnection + missed-event recovery.
5. Design multi-instance fan-out.
6. Specify tests: unauthorized subscribe rejected, reconnect recovers missed events, events follow commits.

## Decision Rules

- Choose the lightest transport that meets staleness needs; upgrading later is easier than operating WebSockets you didn't need.
- A channel subscription is an authorization decision — same rigor, same negative tests, as the resource itself.
- Emit events after commit; an event about a rolled-back change is a lie.
- Presence/typing may drop silently; domain events may not — classify each event type.

## Rules

- No unauthenticated socket accepts subscriptions.
- Channel naming never encodes secrets; names are guessable, authorization is the gate.
- Realtime infrastructure additions are stack decisions (approval).

## Anti-Patterns

- WebSockets for a dashboard that could poll every 30s.
- Auth at handshake only, then honoring any channel name sent later.
- Reconnects that resume "from now," silently dropping the gap.
- Emitting events inside a transaction that later rolls back.
- Single-node event emit that misses connections on other instances.

## Validation Checklist

- [ ] Fit decision per use case recorded (lightest sufficient transport).
- [ ] Handshake auth + expiry behavior designed.
- [ ] Subscribe-time channel authorization + negative tests.
- [ ] Missed-event recovery designed.
- [ ] Multi-instance fan-out designed.
- [ ] Events ordered after commit.

## Definition of Done

A recorded realtime design — justified transport per use case, authenticated connections, ownership-checked subscriptions, reconnection with event recovery, and instance-spanning fan-out — with negative tests for unauthorized subscription.

## Related Skills

`backend-authentication`, `backend-authorization`, `ownership-authorization`, `queues` (backplane overlap), `backend-observability` (connection metrics), `nestjs-foundation` (gateways), `rest-api-design` (fallback endpoints).

## Related Knowledge

`../../../knowledge/` (staleness tolerances, concurrency expectations).

## Related References

`../../../references/backend/realtime/` (channel model, when populated).

## Context Loading Guidance

- **Requires:** use cases with staleness/direction, auth model, instance count.
- **Does not require:** client rendering details, broker internals.
- **May load:** `ownership-authorization` (subscribe checks), `queues`.
- **Stop when:** transport decisions and the channel/auth/recovery design are recorded.

## Token Efficiency Guidance

The use-case × transport table plus the channel-authorization rules carry the design; don't restate socket library APIs.
