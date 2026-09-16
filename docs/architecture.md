# Architecture

How the application is put together, at the level that matters to somebody
deciding whether to trust its numbers. Implementation internals are not
described.

---

## Shape

```
        browser (the view)                    local process (the engine)
  ┌─────────────────────────┐           ┌──────────────────────────────────┐
  │ table, controls, panels │  HTTP on  │  rules → shoe state → probability│
  │ renders what it is sent │ ◄──────►  │  → dealer law → action values    │
  │ computes no game value  │ 127.0.0.1 │  → comparison, provenance, trace │
  └─────────────────────────┘           └──────────────────────────────────┘
```

A local HTTP server and a browser page. The split is deliberate and is enforced:
**the front end computes no game quantity**. Every total, legal action,
probability and expected value arrives from the engine, so the interface cannot
drift away from the mathematics, and a check in the build refuses front-end code
that tries.

## Why a browser

The interface needs tables, disclosure, keyboard control and a printable
derivation. A browser gives all of that without shipping a toolkit, and it makes
the boundary between *view* and *calculation* something a reader can inspect
rather than trust.

## Layers

| Layer | Responsibility |
| --- | --- |
| Rules | a table's rules as data — deck count, dealer rule, doubling, splitting, payouts — with unsupplied values marked as assumptions |
| Shoe state | exact remaining counts, updated per observed card |
| Probability | next-card law, dealer terminal distributions, settlement |
| Evaluation | action values, payoff distributions, margins, provenance |
| Reconstruction | observations → a validated table state; replay for undo |
| Session | a dealt table: seats, hands, phases, ledger |
| Research | Oracle labelling, policy scoring, published study artifacts |
| Interface | rendering, disclosure, keyboard control, accessibility |

## Rules as data, not code

Nothing about the game is hard-coded. A rules file describes the table, and
values that were never confirmed for a specific real table are carried as
**assumptions** and labelled wherever they affect a number. Changing the deck
count or the dealer's soft-17 rule changes results because it changes the model,
not because a different code path was taken.

## State and actions

The session holds a version that advances only when a command is accepted. A
command names the version it was chosen against; one naming a version the table
has already left is refused, and nothing is applied. That is what makes one
deliberate action cause at most one state transition, regardless of timing,
duplicate delivery or how long a calculation took. Commands on a session are
serialised, so two arriving at once cannot interleave.

Live Reconstruction is **event-sourced**: the observations are the state, undo
is a shorter replay, and the server holds nothing that could drift out of step
with what was seen at the table.

## Cost, and what is computed when

Exact enumeration is bounded. Interactive work also carries a time bound and
reports an estimate — labelled, with its uncertainty — rather than making
someone wait indefinitely. The expensive mathematics is tiered and fetched only
when opened, so the table never waits on an enumeration it was not asked for.
Offline research runs drop the clock and use a reproducible node bound instead,
so their results do not depend on machine speed.

## Verification

- an independent exact-rational reference implementation, sharing no code with
  the engine, used for certification;
- a validation suite checking distribution closure, exact-vs-sampled agreement,
  and published dealer tables;
- displayed identities recomputed and shown pass or fail beside the numbers;
- a regression suite including browser tests that drive the real interface;
- packaged-artifact tests that run the downloadable build as a stranger would.

## Privacy and network

The server binds loopback only; another interface requires an explicit flag, and
there is no authentication. Nothing is uploaded. Research contribution is off
unless switched on and pointed somewhere. No account, no licence check, no
background updater.

## Distribution

The Windows build bundles a CPython runtime, the engine, the web interface, the
rule files and the published research artifacts as one folder. It is not
code-signed; each release publishes a SHA-256 instead. Packaging is not source
protection and is not claimed to be.
