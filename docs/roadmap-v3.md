# Research roadmap — v3

**Nothing on this page is a feature of 21-simulator today.** It is the research
direction for v3, written so that the difference between *planned engineering*,
*open research* and *computationally hard* stays visible. Where something is
uncertain, it says so; where a result would be needed to justify a claim, that
result does not exist yet.

Each item is tagged:

| Tag | Meaning |
| --- | --- |
| **[E]** | engineering — the approach is known, the work is building it |
| **[R]** | research — the question is open; the answer may be negative |
| **[H]** | computationally hard — may not become tractable at all |

v2 is a working analysis engine with published, caveated results. v3 is about
pushing past the three walls v2 hit: **exact split computation**, **states that
are only partially known**, and **analysis of one state at a time**.

---

## 1. A practical exact shared-shoe split / resplit solver **[H]**

The wall, measured: of 50 selected six-deck split cells, **13 fit** an exact
shared-shoe enumeration within 16 GB; **37 did not**. Where exact comparison was
possible, the per-hand model was within 4.5 × 10⁻⁵ EV. For the rest, the error
is bounded in sign but not measured.

Directions worth trying:

- **state compression** — smaller representations of a partially played shoe;
- **canonicalisation** — one representative per equivalent state;
- **symmetry reduction** — ranks a rule cannot distinguish collapsed;
- **dependency-aware decomposition** — separating what the second hand actually
  needs from the first;
- **dynamic programming and memoisation improvements** over the shared-shoe
  recursion;
- **better shared-shoe representations**, including bounded-error variants where
  the bound is provable rather than hoped for.

**Goal:** increase the share of realistic six-deck split and resplit states that
can be solved exactly, and measure the per-hand model's error over a much larger
part of the space.

**Not promised:** that every state becomes tractable. The state space may simply
be too large for exhaustive treatment on ordinary hardware.

## 2. Uncertain-state mathematics **[R]**

Today the analysis starts from a state taken as known: these cards were seen,
this is the shoe. Real observation is worse than that — a card is missed, a
burn is unseen, a neighbour's hand is half-visible.

The object of interest becomes a distribution over states,

$$P(S \mid \text{observations})$$

rather than a single $S$, with action values taken in expectation over it,

$$\mathrm{EV}(a \mid \text{observations}) = \sum_{S} P(S \mid \text{observations})\, \mathrm{EV}(a \mid S).$$

Open questions: which uncertainties change a decision often enough to be worth
modelling, how to elicit them without inventing false precision, and how to
present a belief-weighted answer without it reading as more certain than it is.

## 3. A partially observable sequential formulation **[R]**

Where it earns its place, state the problem explicitly as a partially observable
sequential decision process, separating **physical state**, **observation**,
**belief state**, **action**, **transition** and **payoff**.

This is a framing, not a solver, and it is **not** to be forced: for a fully
observed shoe the ordinary exact recursion is simpler, faster and exact, and a
POMDP wrapper around it would be decoration. The value would be in the places
where information is genuinely missing — which is item 2's territory.

## 4. Symbolic and auditable derivations **[E]**

Extend the Full Mathematical Trace from an explanation into something closer to
a checkable derivation, carrying provenance along the whole chain:

```
observed state → conditional distribution → transition → terminal outcomes
              → payoff terms → action EV → decision comparison
```

**Goal:** every displayed number reachable from its inputs by steps a machine
can re-verify, so that "exact" is demonstrated rather than asserted. Includes
stronger machine-checkable identities and a derivation format that survives
being exported.

## 5. Counterfactual decision analysis **[R]**

Questions the engine can almost answer but does not:

- *What is the smallest change in composition that changes the optimal action?*
- *Which unseen cards would move this state across a decision boundary?*
- *Which features of this shoe are actually responsible for the recommendation?*

This is the distance to a decision boundary, and an attribution problem over the
composition vector. It is more useful than a single recommendation, because it
says how fragile that recommendation is.

## 6. Global decision surfaces **[H]**

Move from one state at a time to regions of the state space: EV surfaces, policy
maps, decision boundaries, count-versus-composition regions, sensitivity maps,
near-tie surfaces, regret surfaces.

The obstacle is cost. Each point is an exact evaluation, and a surface is many
points, so this depends partly on item 1 and partly on deciding which slices are
worth the compute.

## 7. Generalised sensitivity and effect of removal **[E]**

Effect of removal is a one-card finite difference. Generalise it into systematic
sensitivity analysis: finite differences and, where meaningful, derivatives of
decision value with respect to composition; multi-card perturbations; and which
directions in composition space matter most for a given decision.

## 8. More serious machine-learning research **[R]**

v2's honest result: **20M-episode runs did not converge**, more state
information did not automatically help, a coarse representation with ≈ 59% fewer
states recovered what the larger default representation lost, and hand-written
reference policies still beat every learned policy on the benchmark.

Worth investigating: richer state representations; the coarse/fine resolution
trade-off; function approximation; sample efficiency; convergence diagnostics;
calibrated uncertainty; ensembles; interpretability; policy distillation;
optimising Oracle regret directly; disagreement surfaces; model capacity against
state complexity; and using exact Oracle labels as supervision rather than
learning only from realised outcomes.

**A method will not be called useful because it has more parameters.** The
benchmark stays mathematical — EV and regret against the Oracle — not
short-term realised winnings, which mostly measure luck.

## 9. A general finite stochastic decision engine **[R]**

Much of what is here is not about Blackjack: exact finite-population
conditioning, terminal distributions by recursion, action comparison with
provenance, exact-versus-estimated discipline, and Oracle-based policy
evaluation.

The long-term question is which parts separate cleanly into a reusable engine
for finite stochastic decision problems, with Blackjack remaining the
demonstrator. **No general solver is promised**; the first step is finding the
boundary between the general machinery and the game-specific parts.

---

## How v3 will be reported

The same way v2 was: results with their assumptions, limitations named rather
than omitted, figures labelled exact or estimated, and negative results
published. A research direction that does not work out will be written up as
such.
