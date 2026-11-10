---
tags:
  - system-design
  - lessons-learned
  - ksi
  - architecture
---

---

## What it is called

This topic is called **application lifecycle management**, or **graceful startup and shutdown**. It is a production-engineering practice that comes up in system design, not a classic design pattern.

The one related design pattern is the **composition root**: a single place, usually `main`, where the app creates its parts and connects them.

The idea: an app depends on things in a chain, so one place should know the order to start them, the order to stop them, and when the app should die.

## The rules

Start dependencies first and the work last, then stop in the opposite order.

![[Pasted image 20261110150801.png]]

1. **Start in dependency order.** Config, then connections, then outside clients, then the thing that takes work.
2. **Check dependencies for real at startup.** If one is down, refuse to start ("fail fast"). Do not report success and fail later.
3. **Take work last.** A worker should not pick up jobs until everything it needs is ready.
4. **On a stop signal, stop taking new work and finish running work.** On Linux and in containers the signal is usually `SIGTERM`. Ctrl+C sends `SIGINT`.
5. **Stop in reverse order.** Close the worker first, because it is still using Redis and the database.
6. **Put a time limit on shutdown.** One stuck job must not block the app from stopping forever.
7. **Ignore repeat stop signals.** Shutdown should run once.
8. **Only the top level exits the process.** Helpers throw errors. `main` catches them and decides.

Why helpers should not call `process.exit`: the caller cannot retry or clean up, a test that reaches it kills the test runner, and you cannot see every way the app can die from one file.


## Why queue workers need it most

Every deploy stops the app, so this happens all the time. A web request that gets cut off fails once and the user can retry. A job that gets cut off is run again, which can mean a duplicate push notification.

Graceful shutdown only helps when the app is asked to stop. A crash, a force-kill or a power loss skips it.

That is why system design treats queues as **at-least-once delivery**: any job might run twice. You need both halves:

- **Graceful shutdown** makes duplicates rare.
- **Idempotency** (safe to run twice) makes duplicates harmless. For example, record which devices already received a notification.


## Where you will see it

Most frameworks and platforms build this in or expect it. These examples are from general knowledge, so check the current docs before relying on the details.

|   |   |
|---|---|
|Where|What to look at|
|NestJS|`onModuleInit`, `onApplicationShutdown`|
|Spring|Destroys objects in reverse dependency order|
|.NET|`IHostedService`|
|Go|`defer` runs in reverse order; `os.Exit` stays in `main`|
|ESLint|`no-process-exit` rule, from plugins|
|Kubernetes|Sends `SIGTERM`, waits 30 seconds by default, then force-kills|
|Twelve-Factor App|The "Disposability" factor|

**Terms to search later:** graceful shutdown, fail fast, composition root, liveness and readiness probes, at-least-once delivery, idempotency.