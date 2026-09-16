# Research findings — v2.0.0

This page explains the main research results published with 21-simulator v2.0.0: what
was measured, what the numbers mean, and — just as important — what they do
**not** show.

Two conventions hold throughout. A difference that does not clear its own 95%
uncertainty is reported as *no difference*, not as a small one. And a learning
run is never called converged unless its own diagnostics say so; none did.

---

## 1. The whole game at six decks

**Result.** Enumerating every one of the 1,183 distinct opening deals, weighting
each by its exact probability and valuing it at the best action, gives a
six-deck house edge of **≈ 0.5661%** — a player Expected Value of
**−0.005661** per unit staked.

**What was and was not solved exactly.** No action of any deal was sampled. But
"exact" here describes the arithmetic, not the split model: the 52 deals where
splitting is best — about **2.27% of opening probability mass** — are valued
under the **per-hand** model. This figure should not be read as every split
state having been solved by full exact shared-shoe enumeration. Section 4 is
about exactly that distinction.

No action of any deal was estimated. 52 deals, carrying 2.27% of the
probability, are best played by splitting; those are valued with the per-hand
split model (see section 4).

The same calculation off the top of a single deck gives **+0.0474%** to the
player.

**Rules behind the number.** Six decks; the dealer stands on soft 17; the dealer
does not check for a natural, and a dealer natural takes doubled and split
stakes; doubling on any first two cards and after splitting; no surrender;
naturals pay 3:2. Several of these rules are configurable and some are
unconfirmed for any particular real table. Each one moves the figure.

**What it does not show.**

- **It is not a session edge.** It is the first round from a *full* shoe. A shoe
  dealt to its cut card spends most of its rounds in other compositions.
- **It is not basic strategy.** Every state is played at its
  composition-dependent optimum, which is worth slightly more than a fixed
  strategy card.
- **It does not include resplitting.** Splits are valued to two hands. Measured
  separately (section 5), resplitting to four hands adds **+0.053%** under a
  stated playing strategy, which would put the modelled edge near **0.513%**.
  That correction is not folded into the enumerated figure, because the two come
  from different models.
- **It is not advice.** It says nothing about what any player will win or lose.

> A note on the one-deck figure: v2.0.0 supersedes an earlier, lower one-deck
> value. That calculation had counted fully enumerated split deals as "sampled"
> and included two deals that had genuinely been estimated under a time limit.
> Re-computed with every action enumerated, the figure is **+0.0474%**, and only
> that value should be quoted.

---

## 2. Same count, different composition

Card counting compresses the shoe into a single number. The question here is how
much a decision can depend on what that number hides.

**Method.** For a declared grid of **90 decisions** — nine player hands against
all ten dealer upcards — shoes were built by removing cards in patterns that
leave a Hi-Lo counter holding *identical* numbers: the same running count, the
same true count and the same number of cards remaining. Every one of the
**2,250** resulting shoe compositions was valued exactly. The ranking rule for
examples was fixed before the sweep ran.

**Result.** In **30 of the 90** decisions, the best action changed between shoes
that Hi-Lo treated as the same.

**Example — soft 18 against a dealer 4.** Across shoes a counter cannot tell
apart, the best play moves between **DOUBLE** and **STAND**, and the value of the
hand spans about 0.20 units. The shoes where doubling is best are consistently
the ones short of aces: Hi-Lo counts an ace exactly as it counts a ten, so it
cannot distinguish a shortage of one from a shortage of the other.

A stricter variant removes only ranks the count ignores entirely, so the count
never moves at all. It still changes the best action in 3 of the 90 decisions.

**What it does not show.**

- **It does not say how often this happens at a real table.** The compositions
  were constructed to hold the count fixed, and some are extreme — twelve of a
  rank removed. The result shows *that* a count can hide decision-relevant
  information, not how frequently a dealt shoe reaches such a state.
- **It is not a critique of any counting system for betting.** It concerns playing
  decisions only.
- In the other 60 decisions the value differed between shoes but the best action
  did not — the count missed value there, not a decision.

---

## 3. Learned policies: more information is not automatically better

**Method.** Tabular learning agents were trained in a persistent six-deck shoe
with four different views of the game:

| Agent | Sees |
| --- | --- |
| basic | its own hand and the dealer's card |
| count | + a banded true count |
| composition | + banded shares of tens and aces (default resolution) |
| composition-coarse | the same composition information on a coarser grid |

Each was trained to **20 million episodes** on **three independent seeds**, and
evaluated two ways: realised return over held-out play, and decision quality
(EV regret) on a fixed benchmark of exactly labelled states. Differences were
judged across seeds, conservatively.

**Results.**

- **No run converged.** Every one of the twelve runs was still discovering new
  states and still changing some of its preferred actions at 20M episodes.
- **Realised return no longer separated the agents.** At 20M episodes every
  pairwise difference in return was within its uncertainty. At 5M the default
  composition agent had been clearly behind.
- **More information did not buy better decisions.** On the benchmark, the default
  composition agent gave up more Expected Value per decision than the basic and
  count agents.
- **Representation mattered.** The coarse composition agent used **59% fewer
  states** and recovered the decision quality the default composition agent lost,
  ending level with the basic agent.
- Carefully hand-written strategies still made better decisions than every
  learned agent on the same benchmark.

**What it does not show.**

- **It is not "AI beats Blackjack".** No learned agent outperformed the
  mathematical Oracle or the best hand-written strategies, and none has converged.
- **It does not show that shoe information is useless.** The wager was fixed; in
  real play much of a count's value lies in bet sizing, which these experiments
  do not model. A larger budget or a different learner could change the picture.
- **The benchmark has a known mismatch.** It is built from one-deck states while
  the agents trained on six decks, so count- and composition-aware agents meet
  more extreme counts there than they trained on. See
  [`limitations.md`](limitations.md).

---

## 4. How accurate is the split model?

When a pair is split, the two hands draw from the same shoe and face the same
dealer. The faster per-hand model treats each hand separately.

**Method.** Fifty pair-against-upcard cells were valued on a real six-deck shoe
under both the per-hand model and an exact enumeration of the shared-shoe
process.

**Result.** The exact evaluation fitted in memory in **13 of the 50** cells. In
every one of those 13, the per-hand model was within **4.5 × 10⁻⁵ EV** of the
exact result, and never above it. That direction is expected: the second hand
can only gain from information about the first hand's cards, and the two models
agree exactly whenever that information changes no decision.

**What it does not show.** The remaining **37 cells** — mostly low and middle
pairs, and pairs against a 2 or an ace — could not be evaluated exactly within
the available memory. The error there is not measured. See
[`limitations.md`](limitations.md).

---

## 5. What resplitting is worth

Resplitting was measured separately, by sampling the physical process on a single
shared shoe with a built-in check against the enumerated split in every cell. Under
a stated per-hand playing strategy, resplitting to four hands was worth up to
about **+0.10 units** for a pair of eights against a seven, and **+0.053%**
across the six-deck game — moving the modelled six-deck edge from ≈ 0.5661% to
approximately **0.513%**.

This is an estimate with its own uncertainty and assumptions, not an exact
result, and it comes from a different model than the enumeration it corrects.
The two are quoted with their parts rather than merged into a single figure
called exact.
