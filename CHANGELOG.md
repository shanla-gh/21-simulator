# Changelog

Public release notes for 21-simulator. Versions follow semantic versioning.

Where a release changes a number that an earlier release reported, it says so
explicitly, because a changed figure is something a reader may have written
down.

---

## v2.0.0 — Beyond the recommendation

*Advanced mathematics, research views, machine-learning evaluation and compute
hardening.*

### Added

- **Full action comparison.** Every legal action side by side with its Expected
  Value, win / push / loss probabilities, regret against the best action, and
  the stake it puts at risk.
- **Payoff distributions.** For each action, every amount it can pay and how
  likely each is, with variance, downside probability and tail figures.
- **Decision margins in bands.** The gap between the best and second-best action
  is classed as decisive, clear, narrow or near-tie; a gap smaller than its own
  sampling uncertainty is reported as unresolved.
- **Hidden information.** What is known and unknown about the dealer's hole card,
  and what the rules allow to be inferred from it.
- **Sensitivity.** Effect of removal — how removing one card of each rank would
  move the decision — and the effect of changing individual table rules.
- **Same count, different composition.** Shoes that a card count reads as
  identical, valued side by side.
- **Other policies.** What alternative strategies would choose at the same
  decision, and what their choices would cost in Expected Value.
- **Split models.** Split value under several models, including an exact
  shared-shoe evaluation where it is affordable, reported side by side.
- **Whole-game Expected Value.** Every opening deal enumerated and weighted by its
  exact probability.
- **Tiered mathematics.** Standard, upgraded and advanced detail, computed only
  when asked for. Asking for more detail never changes the headline answer.
- **Extended mathematical trace**, including how close the decision was and where
  every displayed number came from.
- **Session ledger** showing where decisions fell by margin band.
- **Research view** gathering recorded research results, including the policy
  benchmark and the findings below.

### Research results

- Six-deck whole-game house edge ≈ **0.5661%** under the documented model.
- **30 of 90** decisions change their best action between same-count shoes
  (2,250 compositions, valued exactly).
- Per-hand split model within **4.5 × 10⁻⁵ EV** of exact in the **13 of 50**
  six-deck cells where exact evaluation fitted in memory.
- Learning agents trained to 20M episodes on three seeds: none converged; a
  coarse composition representation (59% fewer states) recovered the decision
  quality the default representation lost.

See [`docs/research.md`](docs/research.md).

### Changed — reported numbers

- **Split values are enumerated where affordable.** Previously a split could fall
  back to a sampled lower bound too early, understating its value — for a pair of
  eights against a six, by about a quarter of a unit.
- **The one-deck whole-game figure is +0.0474% player EV.** It supersedes the
  earlier, lower one-deck figure, which counted some fully enumerated split deals
  as sampled and included two deals that genuinely had been sampled under a time
  limit. Only +0.0474% should be quoted.

### Fixed

- Long simulations no longer grow memory without bound.
- Same-count searches no longer report impossible shoe patterns as missing
  results.
- The research view now uses the full page width on desktop screens.

---

## v1.1.0 — Live Reconstruction

### Added

- **Live Reconstruction**, the new default way to build a table: the round is
  rebuilt in dealing order, one observed card or decision at a time.
- Only legal actions are offered once a hand is ready; splits create real,
  separate hands that are played in order.
- **Exact undo and redo**, by replaying the recorded observations.
- Multi-hand states, so a split table is valued as a split.

### Changed

- The freeform table builder is kept as an alternative workflow.
- The table redraws immediately, without waiting for the mathematics to finish.

### Fixed — reported numbers

- A known dealer hole card is now part of the dealer's hand, not only removed from
  the shoe.
- Insurance is no longer offered once the hole card is known.

---

## v1.0.0 — First release

### Added

- **Exact finite-shoe probability**: sampling without replacement from the cards
  actually remaining.
- **Dealer outcome distributions** for any visible dealer card and shoe.
- **Expected Value for every legal action**, with win / push / loss probabilities
  and the margin between the best actions.
- **Provenance labels** on every figure: exact, estimated (with its uncertainty),
  or lower bound.
- **Show full mathematics**: the derivation behind each result.
- **Play, training and analysis modes**, including a mode that withholds the
  answer until a decision is made.
- **Visual table builder** for describing a state by tapping cards.
- **Session ledger** separating what happened in a session from what the
  decisions were expected to be worth.
- **Independent certification** against a separate exact-rational reference
  implementation.
