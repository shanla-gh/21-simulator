# Limitations

What 21-simulator does not do, cannot yet do exactly, or does under an assumption. These
are stated so that results can be read correctly, not as a list of planned fixes.

---

## Scope

- **No prediction.** 21-simulator calculates from known cards and rules. It cannot know
  the order of the remaining cards and never claims to.
- **Rules must match the table.** Results depend on the configured rules. Several
  table rules — including deck count, whether the dealer checks for a natural,
  doubling after a split, and resplit limits — are configurable assumptions, and
  a result computed under one set of rules does not transfer to another.
- **Continuous shuffling machines are not modelled.** A continuous shuffler is not
  simply a shoe with different penetration, and 21-simulator does not pretend otherwise.
- **Fixed wager.** Research on learned policies concerns playing decisions only;
  bet sizing is not modelled.

---

## Split calculations

### Exact six-deck shared-shoe split: memory ceiling

Evaluating a split exactly — both hands drawn from the same shoe, in order,
against one dealer — is very memory-intensive on a six-deck shoe. In the v2.0.0
measurement of 50 pair-against-upcard cells, only **13** could be evaluated
exactly within the memory available on a 16 GB machine. The other **37** — mostly
low and middle pairs, and pairs against a dealer 2 or ace — did not fit.

In the 13 cells that did fit, the per-hand model was within 4.5 × 10⁻⁵ EV of the
exact result. For the other 37, the per-hand model's error is **not measured**; the
reasoning in [`research.md`](research.md) indicates only its likely direction.

### Interactive sampled split fallback

In interactive use, every calculation has a time and computation budget. If a
split cannot be valued within it, 21-simulator falls back to a sampled estimate. That
fallback plays the split hands with a fixed strategy, so it is a **lower bound**
on the split's value, and it is labelled as such. A lower bound can understate a
split enough to change which action ranks first; the label and margin display are
there so this is visible. Research computations use larger budgets to avoid the
fallback.

### Resplitting

- Split values reported by the engine model **two hands**. Resplitting to more
  hands is not part of the standard split calculation or of the whole-game
  Expected Value.
- The v2.0.0 resplit figures were **sampled**, not enumerated, under a stated
  playing strategy in which each hand uses only its own cards and the pair is
  resplit when the engine recommends splitting it. Jointly optimal play across
  hands, or play that uses other hands' cards, could be worth slightly more.
- Aces are not resplit under the documented rules, so their resplit value is zero
  by rule.

---

## Whole-game Expected Value

- It describes the **first round from a full shoe**, not a session.
- It assumes the best composition-dependent action at every decision.
- Split deals carry the per-hand split model; resplitting is excluded.
- Insurance is excluded as a separate wager.
- It is an enumeration of one round, not comparable with an edge sampled over many
  dealt rounds with a particular strategy.

---

## Learned policies

### Unconverged training

None of the twenty-million-episode training runs converged. At the end of
training every run was still discovering new states, and a meaningful share of
its frequently visited states were still changing their preferred action. Every
learned-policy result describes an agent at a stated training budget, not a
finished one.

### One-deck benchmark caveat

The fixed benchmark used to score policies against the Oracle is built from
**one-deck** states, because exact labelling costs far more per state at six
decks. The learned agents were trained on **six decks**. One-deck states produce
more extreme true counts than a six-deck shoe usually does, so count- and
composition-aware agents are partly evaluated outside the range they trained on.
Realised return in the agents' own training environment is the in-distribution
measure, and the two should be read together.

### Other caveats

- Three training seeds per configuration support conservative comparisons only.
- Only one alternative state representation (the coarse composition grid) was
  carried to the full training budget; the representation question is not settled.

---

## Same-count research

- The compared shoes are **constructed** to hold a counting system's numbers
  fixed; some involve large removals of a single rank. The results show what a
  count can hide, not how often real shoes reach those compositions.
- The grid of decisions is declared and limited to 90 decisions; it is not every
  state in the game.

---

## Computation and reproducibility

- Timings and memory ceilings are machine-dependent. The v2.0.0 figures were
  measured on a 16 GB, 14-core laptop.
- Where an interactive calculation is bounded by time, which values come out exact
  can vary with machine speed. Published research figures were computed with
  budgets that do not depend on time.
