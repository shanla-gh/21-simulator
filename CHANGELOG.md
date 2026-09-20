# Changelog

Public release notes for 21-simulator. Versions follow semantic versioning.

Where a release changes a number that an earlier release reported, it says so
explicitly, because a changed figure is something a reader may have written
down.

---

## v2.1.2 — Six decks or eight, and it says which

*A product release. **No mathematical result changed and no engine file
changed**: eight decks already computed correctly. What this adds is the part a
person touches.*

### Added

- **The two practical shoes are one press** — six decks and eight decks, beside
  the deck field in the simulator, the table builder and Live Reconstruction.
  Any count the rules allow can still be typed, so custom shoes are unchanged.
- **The shoe states its own size**: *"Shoe — 8 decks, 416 cards"*, rather than
  leaving you to divide a card total by 52.
- **A chosen count that is not yet in play says so**: *"Playing 6 decks — start
  the simulator to build 8"*.

### Changed

- The table builder's stale notice names the shoe: *"This answer is for a
  6-deck shoe. The shoe is now set to 8 decks — press Calculate again."* A
  six-deck expected value under an eight-deck setting is a wrong number, not
  merely an old one.

### Verified

A fresh shoe holds 312 cards at six decks and 416 at eight, with every rank
scaled. Composition, next-card probabilities, dealer distributions, action
values, penetration and true-count normalisation all divide by the selected
shoe. Both sizes are covered by the regression suite and both were checked
against the independent exact-rational reference engine — 27 comparisons each,
agreeing to within 4.4 × 10⁻¹⁶.

### Not in this release

Continuous shuffling machines, standard-shoe versus CSM selection, uncertain
deck counts or burn cards, belief states over unobserved shoes, and automatic
table recognition. This release makes finite-shoe analysis at six and eight
decks correct and selectable; it does not model a real casino.

---

## v2.1.1 — Readable, and where you left it

*Product fixes. **No mathematical result changed**: every figure v2.1.0
reported, v2.1.1 reports identically.*

### Fixed

- **A control no longer hides its own label under the pointer.** The view
  tabs, the mode switch, the table-builder's workflow picker and an occupied
  seat all lost their text the moment the mouse arrived — light lettering on a
  pale hover wash, measured at 1.05 : 1 on the selected tab, which is not low
  contrast but invisible. The control you were pointing at was the one you
  could not read. Every control now states what hover means for it, and the
  contrast of each one is measured by a test.
- **A refresh no longer throws away the hand.** Pressing F5 in the middle of a
  round used to start a new shoe. The application had the table all along; the
  page had forgotten which table was its own. A tab now stays on its own table
  across a reload, and falls back to a new one only when the application has
  been restarted in the meantime.
- **Two windows are two tables.** A second tab used to quietly take over the
  first tab's session; the two now run independently.

### Changed

- The interface calls itself `21-simulator` — the same spelling as the
  download, the executable and this page. v2.1.0 showed `21 Simulator` in the
  page heading.

*The published v2.1.0 archive and its checksum are unchanged and remain
available on the [releases page](https://github.com/shanla-gh/21-simulator/releases).*

---

## v2.1.0 — The download

*Product hardening, packaging and the first public build. No validated
mathematical result from v2.0.0 changed.*

### Added

- **A downloadable Windows application.** No Python, no installer, no account:
  extract and run. It starts a local server, opens your browser at it, and stops
  when the window is closed. Published with a SHA-256 checksum.
- **Two ways to run it.** The normal launch, and `--serve` for anyone who wants
  to open the address themselves. Also `--port`, `--no-browser`, `--version` and
  `--help`.
- **`--diagnostics`**: a short, shareable report — version, platform, whether the
  bundled resources were found, which research artifacts are present. It
  contains no environment variables, credentials or personal paths.
- **Worked examples in the table builder**, so a first run produces a real
  analysis in one click: hard 16 vs 10, soft 18 vs 4, a pair of eights vs 6, and
  a thin single-deck shoe.
- **The version is shown in the interface.**

### Fixed

- **A decision could be applied to a hand you were never asked about.** If a
  command took a moment, the page looked unchanged, and a second press was sent
  as a second decision — on a two-seat table, pressing STAND twice stood the
  next seat's hand. Actions are now tied to the state they were chosen against,
  so one deliberate action causes at most one state change, whatever the timing.
  The actions are unavailable while one is being applied, and a command that
  lost its turn redraws the table instead of reporting an error.
- **Two copies could fight over one port on Windows.** A second instance could
  bind a port already in use and silently take part of the first one's traffic.
  A busy port now moves the new copy to another port on the same interface, and
  says so.
- **The research view was empty unless started from the right directory.**
  Bundled artifacts are now found wherever the application is run from.
- **An unexpected fault could reach the browser as a stack trace.** It now
  answers with a plain message and logs the detail to the console.
- Output is no longer buffered when the application's output is captured, so a
  log shows the address and any startup problem immediately.

### Security

- Loopback-only by default; any other interface requires `--allow-remote`, and
  the reason is stated where the flag is.
- A strict content-security-policy, framing refusal, `nosniff`, `no-referrer`
  and `no-store` on every response.
- Request size limits, JSON validation, and static files served only from the
  bundled web folder.
- No credentials, no telemetry by default, and nothing written outside the
  application folder in normal use.

### Notes

- Builds are **not code-signed**; Windows SmartScreen may warn on first run. The
  published checksum is the check that matters.
- Packaging bundles a Python runtime and is **not** source protection; this is
  stated rather than implied.

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
