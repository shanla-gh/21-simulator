<div align="center">

# 21-simulator

### See the math behind the game.

A probability and stochastic-decision research application built around Blackjack.
It reconstructs an observed table state and shows the mathematics that state implies —
every distribution, every action value, and how each number was produced.

![Release](https://img.shields.io/badge/release-v2.1.0-1f6f5c)
![Platform](https://img.shields.io/badge/platform-Windows%20x64-444444)
![Runs](https://img.shields.io/badge/runs-locally%2C%20no%20account-5b7f8a)
![Source](https://img.shields.io/badge/source-not%20publicly%20distributed-555555)

[Download](#download) · [Methodology](docs/methodology.md) · [Research](docs/research.md) ·
[Limitations](docs/limitations.md) · [Roadmap](docs/roadmap-v3.md)

</div>

---

## It does not predict the next card

Nothing in 21-simulator sees the order of the shoe. There is no prediction, no
signal, and no claim about what comes next beyond what the cards already on the
table imply.

What it does is condition. Given the rules, the deck count, and every card that
has been observed, the composition of the remaining shoe is known exactly, and
so is the probability of each rank appearing next:

$$P(X_{t+1} = r \mid c_t) = \frac{c_{t,r}}{N_t}, \qquad N_t = \sum_{r} c_{t,r}$$

where $c_t$ is the vector of remaining counts by rank after $t$ observed cards.
Every figure the application reports is a consequence of that vector, the rules,
and nothing else.

---

## What it computes

| Layer | Quantity |
| --- | --- |
| **Shoe state** | $c_t$ — exact remaining counts by rank, updated per observed card |
| **Next card** | $P(X_{t+1}=r \mid c_t)$, sampling without replacement |
| **Dealer** | the terminal distribution $P(D = d \mid u, c_t)$ over 17–21 and bust, by recursion under the table's drawing rule |
| **Action values** | $\mathrm{EV}(a \mid s) = \sum_{o} P(o \mid a, s)\, u(o)$ for every legal $a$ |
| **Outcomes** | win / push / loss probabilities per action |
| **Payoff distribution** | the full law of the payout $u$ per action, not just its mean |
| **Risk** | variance, standard deviation, downside probability, lower tail |
| **Decision margin** | $\mathrm{EV}(a^{*}) - \mathrm{EV}(a^{(2)})$, placed in published bands |
| **EV regret** | $\mathrm{EV}(a^{*}) - \mathrm{EV}(a)$ for any other policy's choice |
| **Hidden information** | hole-card beliefs before and after what the rules reveal |
| **Effect of removal** | $\Delta \mathrm{EV}$ from removing one card of each rank |
| **Provenance** | every number labelled **exact**, **estimated** (with its standard error) or **lower bound** |

Plus the surfaces built on those: **Live Reconstruction**, the **Full
Mathematical Trace**, the **Session Ledger**, and the **research views**.

---

## From probability to decisions

```
observed cards → c_t → P(next card) → dealer terminal law → action outcomes → EV(a|s) → ranked comparison
```

1. **Finite shoe.** A shoe is a finite multiset. Each observed card leaves it.
   No step treats draws as independent or as coming from an infinite deck.
2. **Conditional next card.** $P(X_{t+1}=r \mid c_t) = c_{t,r}/N_t$, recomputed
   after every observation.
3. **Dealer terminal distribution.** The dealer's hand is enumerated
   recursively against the remaining shoe, each path carrying its probability,
   ending where the drawing rule stops or the hand breaks.
4. **Action values.** Standing settles against $P(D)$. Hitting takes each rank
   with its conditional probability and continues optimally. Doubling takes one
   card at a multiplied stake. Splitting is modelled explicitly (below).
5. **Comparison.** Actions are ranked by EV; the margin between the best two is
   measured and banded; the uncertainty of any sampled value is carried with it.

The objective is expected value. Risk figures are descriptions — nothing
promotes a lower-EV action.

---

## Exact vs estimated

A number is only as good as its provenance, so every figure carries one.

| Label | Meaning |
| --- | --- |
| **exact** | enumerated; no sampling anywhere in it |
| **estimated** | sampled, reported with standard error and interval |
| **lower bound** | a sampled value known not to exceed the true one |

Exact enumeration is bounded by a computation budget. Interactive calculations
also carry a time bound, and when one is exceeded the answer is *estimated and
said to be* rather than silently approximated or made to wait. Offline research
runs use a reproducible node bound and no clock, so their exactness does not
depend on machine speed. A margin smaller than the uncertainty behind it is
reported as **unresolved**, which is not the same as a near-tie.

---

## Splitting and shared-shoe dependence

Splitting is the one action whose two hands are not independent: they draw from
one shoe, in order, and are settled against one dealer hand. 21-simulator names
which model produced a split value.

| Model | What it represents |
| --- | --- |
| per-hand | each hand valued against the post-split shoe separately |
| shared first cards | each hand's shoe excludes the other's first card |
| shared shoe, exact | the real sequence enumerated; expensive |
| shared shoe, sampled | the same process simulated, with a standard error |

Measured on a real six-deck shoe, where the exact enumeration was affordable,
the per-hand model was within **4.5 × 10⁻⁵ EV** of exact and never above it —
in the 13 of 50 measured cells that fit. See [limitations](docs/limitations.md).

---

## Live Reconstruction

Rebuild a round the way it was dealt, rather than describing a finished state.

- The application knows the dealing order and whose card comes next; it asks for
  **one observation at a time** — a rank, or a decision.
- The shoe, the probabilities and the action comparison update as the hand
  develops.
- Only legal actions are offered. A split creates real separate hands played in
  order.
- **Undo and redo are exact**: the state is replayed from the recorded
  observations, not patched backwards.
- Other seats' cards and previously seen cards leave the shoe exactly as your
  own do.

One deliberate action causes at most one state transition. Every command names
the state it was chosen against, and one that arrives against a state the table
has already left is refused rather than applied to whatever is current — so an
impatient second click cannot act for a hand you were never asked about.

---

## Research layer

**Oracle benchmarking.** A fixed set of exactly labelled states is the ground
truth. Any policy — hand-written or learned — is scored on the same states.

**EV regret, by margin band.** Disagreement is priced, not counted: agreeing on
a near-tie and on a decisive decision are different achievements.

**Same count, different composition.** Shoes constructed so a counting system
reads them identically, valued against each other.

**Learned-policy experiments.** Tabular agents with different state
representations, trained and then measured against the Oracle. Research, not a
product claim.

---

## Selected v2 findings

Every figure below holds **under the documented rules and model**.
Full detail and caveats: [docs/research.md](docs/research.md).

### The whole game, six decks

| | |
| --- | --- |
| House edge | **≈ 0.5661%** (player EV ≈ **−0.005661** per unit staked) |
| Coverage | all 1,183 distinct opening deals, weighted by exact probability |
| Sampling | none — no action of any deal was estimated |
| Split deals | 52 deals, ≈ **2.27%** of opening probability mass, valued under the **per-hand** model |
| Resplitting | not included; modelled separately (below) |

Model: first round from a full shoe, composition-dependent optimal play, six
decks, dealer stands on soft 17, no hole-card check with the dealer's natural
taking doubled and split stakes, doubling on any two cards and after splitting,
no surrender, 3:2 naturals. **Not** a session edge, and **not** basic strategy.

**This does not mean every split state was solved by exact shared-shoe
enumeration.** Those 52 deals carry the per-hand model.

### Resplitting

Under the documented resplitting model — a stated per-hand playing strategy,
sampled on one shared shoe with a check against the enumerated split in every
cell — resplitting to four hands moves the modelled six-deck edge to
approximately **0.513%**.

### One deck, off the top

**+0.0474%** player EV under the same model at one deck. (This supersedes an
earlier, lower published figure that mislabelled enumerated split deals as
sampled; only +0.0474% should be quoted.)

### Same count, different composition

| | |
| --- | --- |
| Decisions swept | 90 |
| Shoe compositions | 2,250, **all valued exactly**, no search gaps |
| Decisions whose best action changed | **30 of 90** |

Shoes were constructed so that Hi-Lo reads them identically — same running
count, same true count, same cards remaining. The best action still changed in a
third of the decisions.

**Same count does not imply same composition, and does not imply the same
decision value.** A strong case is **soft 18 against a dealer 4**, where
composition alone moves the preferred action between **DOUBLE** and **STAND**.
In the sharpest examples the distinguishing feature is the ace supply: Hi-Lo tags
an ace exactly as it tags a ten, so a shortage of one reads like a shortage of
the other.

### Learned policies — what the experiments do *not* show

This layer is research. It is **not** "AI beats Blackjack".

- Runs of **20M episodes** across three seeds and four state representations
  **did not converge**.
- **More state information did not automatically produce a better policy.**
- The default composition representation **gave up more EV** than the simpler
  references on the current benchmark.
- A **coarse composition representation used ≈ 59% fewer states** and recovered
  the loss the larger default representation showed.
- **Hand-written reference policies still outperform every learned policy** on
  the reported benchmark.
- The benchmark has documented scope caveats — notably that it is built from
  one-deck states while the agents trained on six decks.

---

## Download

**[⬇ Download the latest release](https://github.com/shanla-gh/21-simulator/releases/latest)**

| | |
| --- | --- |
| Current release | **v2.1.0** |
| Platform | Windows x64 |
| Artifact | `21-simulator-v2.1.0-windows-x64.zip` (8.7 MB) |
| SHA-256 | `1c1d7dcbf2ec063af6da4fd7f61dfecc680a0c897db623164982e8fe723e633b` |
| Requirements | none — no Python, no installer, no account |

Extract the folder anywhere and run `21-simulator.exe`. It starts a local
server, opens your browser at it, and stops when you close the window.
Full instructions, checksum verification and the security model:
**[docs/installation.md](docs/installation.md)**.

The build is **not code-signed**, so Windows SmartScreen will likely warn the
first time. Verify the published SHA-256 before running it — that check is worth
more than a signature you cannot inspect.

---

## Running it

| Mode | Command |
| --- | --- |
| Normal | run `21-simulator.exe` — server starts, browser opens |
| Server only | `21-simulator.exe --serve` then open the printed address |
| Another port | `21-simulator.exe --port 9000` |
| Diagnostics | `21-simulator.exe --diagnostics` |

It binds **127.0.0.1** only. Binding any other interface requires an explicit
`--allow-remote`, because there is no authentication. Nothing is uploaded; the
research-contribution feature is off unless switched on deliberately.

---

## Validation

- **Independent certification.** Core values are compared against a separate
  exact-rational reference implementation sharing no code with the engine. In
  the v2.1.0 run all 27 comparisons agreed to within **2.2 × 10⁻¹⁶**.
- **Validation suite.** Distribution closure, exact-vs-sampled cross-checks and
  published dealer tables: **15 of 15** checks passed.
- **Regression suite.** Over 2,190 automated tests, including 149 browser tests
  driving the real interface, plus a stress tier of long simulations.
- **Packaged-artifact tests.** The downloadable build is started as a stranger
  would start it — copied elsewhere, path containing spaces, no development
  environment — and then driven over HTTP.

---

## Version history

| Version | Focus |
| --- | --- |
| **v2.1.0** | Product hardening: interaction correctness, security, packaging, the Windows download |
| v2.0.0 | Advanced mathematics, research views, ML evaluation, compute hardening |
| v1.1.0 | Live Reconstruction and the table builder |
| v1.0.0 | Exact finite-shoe engine, action comparison, mathematical trace |

Details in [CHANGELOG.md](CHANGELOG.md).

---

## What is planned next

[docs/roadmap-v3.md](docs/roadmap-v3.md) sets out the v3 research direction:
pushing exact shared-shoe split/resplit solving past its current memory ceiling,
uncertain-state inference $P(S \mid \text{observations})$, a partially observable
formulation where it earns its place, auditable symbolic derivations,
counterfactual decision analysis, global decision surfaces, systematic
sensitivity analysis, more serious ML research, and the question of what
generalises beyond Blackjack.

Everything there is **planned or open**, not delivered.

---

## Disclaimer

21-simulator is **educational and research software**.

- It **does not predict future cards**.
- It **does not guarantee** any gambling outcome, result or profit. An expected
  value is an average over hypothetical repetitions, not a forecast of a hand.
- Results depend on the rules they were computed under; real tables differ.
- You are responsible for complying with the laws, regulations and venue rules
  that apply to you, including any rules about the use of devices or software.

---

## Is the source public?

**No. The 21-simulator source code is not publicly distributed.**

This repository is the product page, documentation and release channel. It
contains no source. 21-simulator is not open-source software; see
[LICENSE.md](LICENSE.md) and [NOTICE.md](NOTICE.md).
