<div align="center">

# 21-simulator

### See the math behind the game.

A probability and stochastic-decision research application built around Blackjack.

![Release](https://img.shields.io/badge/release-v2.0.0-1f6f5c)
![Download](https://img.shields.io/badge/public%20download-coming%20soon-lightgrey)
![Source](https://img.shields.io/badge/source-not%20publicly%20distributed-555555)

</div>

---

> **21-simulator does not predict the next card.**
> Nothing in it can see the future of the shoe. It calculates only from what is
> already known — the rules, the number of decks, and the cards that have been
> observed — and shows exactly what those facts imply, how every figure was
> produced, and where the model stops.

---

## What 21-simulator does

You describe a Blackjack state you have observed. 21-simulator works out what that
state means, mathematically, and shows its working.

| | |
| --- | --- |
| **Remaining shoe** | The exact composition of the cards still unseen, rank by rank. |
| **Next-card probabilities** | The conditional probability of each rank, given every card already accounted for. |
| **Dealer outcomes** | The full distribution of where the dealer finishes — each total, and bust. |
| **Every legal action, compared** | Stand, hit, double, split and surrender where the rules allow them, side by side. |
| **Expected Value** | What each action is worth per unit staked, with its win / push / loss probabilities. |
| **Payoff distributions** | Not just the average: every amount an action can pay, how likely it is, and the risk around it. |
| **Decision margins** | How far the best action is ahead of the next one — and whether that gap is decisive or a near-tie. |
| **Provenance** | Every number is labelled **exact**, **estimated** (with its uncertainty) or **lower bound**. |
| **Mathematical trace** | A step-by-step derivation behind the answer, generated from the calculation that produced it. |
| **Live Reconstruction** | Rebuild a round as it is dealt, one observed card at a time. |
| **Research views** | Policies and learned models measured against a mathematical Oracle. |

---

## Screenshots

Product screenshots and short demo recordings will be added here. The planned
images, their filenames and what each one should show are listed in
[`assets/README.md`](assets/README.md).

| View | What it shows |
| --- | --- |
| Action comparison | Every legal action with its EV, outcome probabilities and margin |
| Mathematical trace | The derivation behind a recommendation, section by section |
| Live Reconstruction | A round rebuilt card by card as it is dealt |
| Research view | Policies and models scored against the Oracle |

---

## From probability to decisions

Every answer 21-simulator gives is built in the same order, and each step can be
inspected on its own.

```
  finite shoe  →  conditional probability  →  action outcomes  →  Expected Value  →  decision comparison
```

1. **Finite shoe.** A shoe holds a fixed set of cards. Every card that has been
   seen is removed from what can still be dealt.
2. **Conditional probability.** Given what remains, the chance of each rank
   coming next is exactly its remaining count divided by the cards left — and it
   changes with every card observed.
3. **Action outcomes.** For each legal action, the possible continuations are
   followed through to the end of the hand, including how the dealer draws.
4. **Expected Value.** Each outcome is weighted by its probability and by what
   it pays, giving the average result of the action per unit staked.
5. **Decision comparison.** The actions are ranked, the gap between them is
   measured, and the page says how confident that ranking is.

The objective is Expected Value. Risk figures are shown as descriptions; they
never quietly change which action is reported as best.

---

## Live Reconstruction

Most tools ask you to fill in a finished state. Live Reconstruction follows the
round instead.

- It knows the dealing order, and asks for **one thing at a time** — the next
  card for the next position, or the decision for the hand that is acting.
- Cards are entered by tapping ranks. The remaining shoe, the probabilities and
  the action comparison **update as the hand develops**.
- Only legal actions are offered. Splits create real separate hands that are
  played in order.
- **Undo and redo** are exact: the round is replayed from the observations, not
  patched backwards.
- Other occupied seats and previously seen cards leave the shoe just as your own
  cards do.

A freeform builder remains available for describing any state directly.

---

## Research layer

Alongside the decision view, 21-simulator carries the research that tests it.

**Oracle benchmarking.** A fixed benchmark of exactly labelled game states is
used to score any policy — hand-written strategies and learned models alike —
against the mathematically best action in each state.

**EV regret.** A disagreement is measured by what it costs:
`EV(best) − EV(chosen)`. Regret is broken down by how close the decision was,
because agreeing with the Oracle on a near-tie and on a decisive decision are
not the same achievement.

**Same count, different composition.** Card-counting systems compress the shoe
into one number. 21-simulator builds shoes that a count cannot tell apart and
measures whether they really are equivalent.

**Model experiments.** Tabular learning agents are trained with different
amounts of shoe information and evaluated against the Oracle. They are research
subjects, not a product feature, and nothing here claims that a model “beats”
the game.

A research view gathers these results. It reads recorded research outputs and
computes nothing new when opened.

---

## Selected v2.0.0 findings

These results hold **under the documented rules and model** — read the
caveats with the numbers. Full context is in [`docs/research.md`](docs/research.md).

| Finding | Result | Caveat |
| --- | --- | --- |
| **Six-deck whole game** | House edge ≈ **0.5661%** (player EV −0.005661 per unit) | First round from a full shoe, optimal composition-dependent play, no resplitting, split hands under the per-hand model. Not a session edge. |
| **One-deck whole game** | **+0.0474%** player EV | Same model, off the top of a full single deck. Not an edge anyone can play to. |
| **Same count, different composition** | **30 of 90** decisions changed their best action between shoes Hi-Lo read as identical | 2,250 shoe compositions, all valued exactly, over a declared grid of decisions. |
| **Soft 18 vs dealer 4** | Same-count shoes moved the best action between **DOUBLE** and **STAND** | Constructed compositions; shows what a count cannot see, not how often a real shoe does this. |
| **More information ≠ better policy** | A coarse composition representation used **59% fewer states** and recovered the decision quality lost by the larger default one | Learned agents trained to 20M episodes on 3 seeds; **none converged**. |
| **Split model accuracy** | Per-hand split model within **4.5 × 10⁻⁵ EV** of exact | Only where exact six-deck evaluation was feasible: **13 of 50** measured cells. |

---

## Download

<!--
When a verified release exists, replace this block with:

  ### ⬇ Download the latest release
  [**Download 21-simulator v2.x.x**](https://github.com/shanla-gh/21-simulator/releases/latest)
  Supported platforms · checksum · release notes
-->

**Public binary download coming soon.**

Packaged builds of 21-simulator will be published through
[GitHub Releases](https://github.com/shanla-gh/21-simulator/releases) on this
repository. There is no downloadable application here yet, and no other
official download location.

---

## Version history

| Version | Focus |
| --- | --- |
| **v2.0.0** — current | Advanced mathematics, research views, machine-learning evaluation and compute hardening |
| **v1.1.0** | Live Reconstruction and the table builder |
| **v1.0.0** | First public release: the exact finite-shoe engine, action comparison and the mathematical trace |

Details in [`CHANGELOG.md`](CHANGELOG.md).

---

## Technical integrity

21-simulator is built to be checkable rather than taken on trust.

- **Exact vs estimated labelling.** Every figure says how it was produced. An
  estimate carries its uncertainty; a bound is called a bound.
- **Independent certification.** Core results are compared against a separate
  exact-rational reference implementation that shares no code with the main
  engine. In the v2.0.0 certification all 27 comparisons agreed to within
  2.2 × 10⁻¹⁶.
- **Validation.** Probabilities are checked to sum to one, exact results are
  cross-checked against sampling, and dealer distributions are compared with
  published tables. The v2.0.0 validation suite passed 15 of 15 checks.
- **Regression testing.** More than 2,100 automated tests, including browser
  tests of the interface, run before a release.
- **Stated limits.** Where a calculation is too expensive to do exactly, or a
  result depends on an assumption, that is documented rather than hidden. See
  [`docs/limitations.md`](docs/limitations.md).

How the mathematics works, at a high level: [`docs/methodology.md`](docs/methodology.md).

---

## Disclaimer

21-simulator is **educational and research software**. It calculates from observed
information under stated rules and assumptions.

- It **does not predict future cards**.
- It **does not guarantee** any gambling outcome, result or profit. A positive or
  negative Expected Value describes an average over many hypothetical outcomes,
  not what will happen next.
- Rules differ between tables and venues, and the published results depend on
  the rules they were computed under.
- You are responsible for complying with the laws, regulations and venue rules
  that apply to you, including any rules on the use of devices or software.

---

## Source availability

**The 21-simulator source code is not publicly distributed.**

This repository is the public product page and release channel for 21-simulator. It
contains documentation only. 21-simulator is not open-source software, and neither
this repository nor any published build grants access to the source code. See
[`NOTICE.md`](NOTICE.md).
