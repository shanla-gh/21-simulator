# Methodology

A high-level description of how 21-simulator turns an observed Blackjack state into
the numbers it shows. This page explains the ideas; it does not describe the
implementation.

---

## 1. Finite-shoe conditioning

A shoe is a finite set of cards. Once a card has been dealt, it cannot be dealt
again.

21-simulator therefore never treats cards as independent draws from an infinite deck.
It starts from the full shoe defined by the rules — for example 312 cards for six
decks — and removes every card known to be out of play: the player's cards, the
dealer's visible cards, other seats' cards and any cards seen earlier in the
shoe.

The probability that the next card is a given rank is then

```
P(next card = r) = cards of rank r remaining / cards remaining
```

and after each further card, the shoe changes and every probability is
recomputed. This is conditioning, not prediction: the numbers describe what is
*possible and how likely* given what is known, and the unknown dealer hole card
is treated as still unseen, not as missing.

---

## 2. State reconstruction

The mathematics is only as good as the description of the table. 21-simulator
represents a state as the full set of facts that matter:

- the rules in force, including deck count and table options;
- the player's hand or hands, including split hands;
- the dealer's visible cards, and the hole card if it is known;
- cards held by other seats and cards seen earlier in the shoe.

**Live Reconstruction** builds this state in dealing order. The application knows
whose card comes next and which hand is acting, asks for one observation at a
time, and records observations rather than overwriting a form. Undo replays the
recorded observations, so the state after an undo is exactly the state that
those observations imply.

States that could not exist — more cards of a rank than the shoe holds, for
example — are refused with a reason rather than evaluated.

---

## 3. Expected Value and action comparison

For each legal action, 21-simulator follows every possible continuation:

- **Stand** — the dealer draws to completion from the remaining shoe, and the
  result is settled against the player's total.
- **Hit** — each possible next card is drawn with its conditional probability,
  and the best continuation is taken from the resulting state.
- **Double** — exactly one more card, at double the stake.
- **Split** — the pair becomes separate hands (see the split models below).
- **Surrender**, where the rules offer it.

Each final outcome has a probability and a payout. Their weighted sum is the
action's **Expected Value** per unit staked; the same outcomes give its win /
push / loss probabilities and its full **payoff distribution**.

Actions are ranked by Expected Value. The **decision margin** is the gap between
the best and second-best action, placed in published bands (decisive, clear,
narrow, near-tie). **EV regret** for any other choice is `EV(best) − EV(choice)`.

### Split models

Splitting is the one action where two hands share a shoe and a dealer. 21-simulator can
value it in several ways and names which one it used:

| Model | Idea |
| --- | --- |
| Per-hand | Each hand is valued against the post-split shoe separately. Fast; used by default. |
| Shared first cards | Each hand's shoe already excludes the other hand's first card. |
| Shared shoe, exact | The real sequence — first hand played out, second dealt from what remains, then the dealer — enumerated exactly. Expensive. |
| Shared shoe, sampled | The same real sequence, simulated, with a stated uncertainty. |

---

## 4. Exact vs estimated computation

Every number carries a provenance label:

| Label | Meaning |
| --- | --- |
| **Exact** | Computed by enumerating the possibilities, with no sampling. |
| **Estimated** | Computed by simulation, shown with a standard error and interval. |
| **Lower bound** | A sampled value known to be at most the true value — for example a split played out with a simple fixed strategy. |

Exact enumeration is bounded by a computation budget. When an interactive
calculation would exceed its budget, 21-simulator falls back to a labelled estimate
rather than making the user wait indefinitely — and says so. Research
computations use larger budgets and, where reproducibility matters, no time
limit, so that whether a result is exact does not depend on how fast the
machine is.

A decision margin smaller than the uncertainty of the estimate behind it is
reported as **unresolved**, which is different from a near-tie.

---

## 5. Oracle and model benchmarking

The **Oracle** is the exact calculation above, used as ground truth. It is used to
*evaluate* policies, never inside a learning agent's training.

To compare policies fairly, a fixed benchmark of game states is sampled from
realistically dealt rounds and each state is labelled once with the Oracle's
values for every action. Every policy — hand-written or learned — is then asked
for its choice in exactly the same states, and scored on:

- **agreement** with the Oracle's best action;
- **EV regret** — how much Expected Value each disagreement gives up;
- both of these **broken down by decision margin**, because disagreeing on a
  near-tie costs almost nothing while disagreeing on a decisive decision is a
  real error.

Learned models are additionally evaluated on realised return in the environment
they trained in, with uncertainty measured across whole shoes rather than
individual rounds. Where several training seeds exist, conclusions are drawn
across seeds.

---

## 6. Verification

- Probability distributions are checked to sum to one.
- Exact values are cross-checked against independent sampling.
- Dealer outcome distributions are compared with published reference tables.
- Core results are certified against a separate exact-rational reference
  implementation that shares no code with the main engine.
- Identities between displayed figures — for example that a payoff distribution's
  mean equals the Expected Value shown beside it — are recomputed and displayed as
  pass or fail.
