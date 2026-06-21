# Socket.IO Valkey Adapter

A native [Valkey](https://valkey.io) adapter for [Socket.IO](https://socket.io) — the equivalent of `socket.io-redis-adapter`, but powered by Valkey instead of Redis, enabling real-time multi-server WebSocket broadcasting.

---

## Attendee/Team Details

**Team Name:** Stalwart Team

| Name | LinkedIn |
| :--- | :--- |
| Sai Krishna Jamanjyothi | https://www.linkedin.com/in/sai-krishna-jamanjyothi-2bb6a72ba/ |
| Aditya Sathwik Dasari | https://www.linkedin.com/in/aditya-sathwik-dasari-553616219/ |
| Chandrasekhar K | https://www.linkedin.com/in/chandrasekhark1729/ |
| Srinu Desetti | https://www.linkedin.com/in/srinu-desetti/ |

**GitHub Username (repo owner):** chandu-s-1729
**GitHub Project Repository:** https://github.com/webdevelopersrinu/socket.io-valkey-adapter/actions

---

## Problem Statement Selected

**Track A — Integration #27: Socket.IO — add a native Valkey adapter.**

```txt
Problem statement 1
```

Socket.IO currently supports a Redis adapter (`socket.io-redis-adapter`) for scaling across multiple servers. The goal is to add native Valkey support — a `socket.io-valkey-adapter`.

---

## Project Description

**Socket.IO Valkey Adapter** is a drop-in adapter that lets Socket.IO scale horizontally across multiple server instances using **Valkey** as the message bus.

* **What is the project about?** It replaces the existing `socket.io-redis-adapter` with a Valkey-backed equivalent — `socket.io-valkey-adapter` — built on the official Valkey GLIDE client.
* **Who is it for?** Developers running Socket.IO in production who need to scale to multiple Node.js processes/machines and want an open-source (BSD), Redis-free coordination layer.
* **What problem does it solve?** A single Socket.IO server only knows its own connected clients. When scaled across instances, a message emitted on one server never reaches clients connected to another. This adapter coordinates all instances so broadcasts reach every client cluster-wide.
* **How does it help the user?** It gives the proven, battle-tested Socket.IO scaling model on a fully open-source Valkey backend — no licensing risk, with enhanced cluster support via GLIDE.

---

## Approach

We did **not** reinvent the broadcasting logic. Since Valkey is protocol-compatible with Redis 7.2, the proven Pub/Sub model carries over directly. Instead of `socket.io-redis-adapter`, we are building `socket.io-valkey-adapter`.

**How it works:**

1. Every Socket.IO server instance connects to the same Valkey server.
2. When a server broadcasts, it **publishes** the message (with target rooms / excluded sockets) to a Valkey channel.
3. Every server **subscribes** to that channel and receives the message.
4. Each receiving server delivers the message to its **own local clients** that match the target rooms.

For cluster-wide operations (`fetchSockets()`, `socketsJoin()`, remote disconnects) we use a **request/response pattern over Pub/Sub** — one server publishes a request with a unique ID, all servers reply, and the requester aggregates responses until everyone answers or a timeout hits.

**What makes our approach useful:** It implements the standard Socket.IO `Adapter` interface, so it is a true drop-in — existing apps switch backends with no code changes — while moving to an open-source-forever Valkey foundation using the first-party GLIDE client.

---

## Tech Stack and Tools Used

**Frontend:** — (library project; demo client uses Socket.IO client + plain HTML/JS)
**Backend:** Node.js, Socket.IO, Socket.IO Adapter interface
**Database / Datastore:** Valkey (Pub/Sub message bus)
**Client Library:** Valkey GLIDE (`@valkey/valkey-glide`)
**AI Tools/API:** —
**Cloud/Deployment:** GitHub (open source); local multi-instance Node.js setup for testing
**Other Tools:** Git, npm, Docker (Valkey container for local testing)

---

## Key Features

1. Cross-server room broadcasting via Valkey Pub/Sub
2. Targeted emits — specific rooms, multiple rooms, with socket exclusions
3. Cluster-wide room membership tracking
4. `fetchSockets()` across all server instances
5. `socketsJoin()` / `socketsLeave()` for remote sockets and remote disconnect
6. Built on the official Valkey GLIDE client
7. Implements the standard Socket.IO `Adapter` interface — true drop-in replacement

---

## What is Working?

We are reimplementing the existing `socket.io-redis-adapter` as `socket.io-valkey-adapter` — keeping the same proven architecture and adapter interface, swapping the Redis client for the Valkey GLIDE client. The core Pub/Sub broadcast path (publish on one server → subscribe and deliver on all servers) is the foundation being built against the standard Socket.IO `Adapter` contract.

---

## What is Still in Progress?

* Wiring all cluster-wide request/response operations (`fetchSockets`, `socketsJoin`/`socketsLeave`, remote disconnect) through GLIDE Pub/Sub
* CI tests proving correct message flow across 2+ server instances
* Packaging and publishing to npm as `socket.io-valkey-adapter`
* Documentation and upstream PR proposal to the Socket.IO org

---

## Screenshots or Demo

**Deployed Link:** —
**Demo Video Link:** —
**GitHub Repository:** https://github.com/chandu-s-1729/socket-valkey-adaptor_stalwart-team
**Screenshots:** —

---

## Challenges Faced

* The Valkey GLIDE client exposes Pub/Sub and clustering differently from `ioredis`/`node-redis`, so adapting the existing adapter design to GLIDE's API is the main engineering effort.
* Ensuring the request/response coordination over Pub/Sub correctly aggregates replies from all live server instances with proper timeouts.
* Matching the exact Socket.IO `Adapter` interface so the package is a genuine drop-in replacement.

---

## Learnings

* How Socket.IO scales horizontally and why an adapter is required.
* The internals of the Redis adapter's Pub/Sub broadcast and request/response patterns.
* Working with the Valkey GLIDE client for Pub/Sub and cluster connections.
* Valkey's drop-in compatibility with Redis 7.2 and the open-source advantages of the Valkey ecosystem.

---

## Future Improvements

* Add a Streams-based variant for stronger delivery guarantees (mirroring `socket.io-redis-streams-adapter`).
* Full Valkey Cluster and sharded Pub/Sub support.
* Benchmarks comparing throughput/latency against the Redis adapter.
* Submit as a first-party adapter to the upstream Socket.IO organization.

---

## Final Note

Built for the **Valkey Hackathon 2026** at Amazon HYD13, Hyderabad. Our goal is a clean, tested, open-source `socket.io-valkey-adapter` that gives the Socket.IO community a first-party way to scale on Valkey — open source forever, no Redis dependency.
