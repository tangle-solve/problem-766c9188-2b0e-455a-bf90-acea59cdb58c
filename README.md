<!-- solve-problem:766c9188-2b0e-455a-bf90-acea59cdb58c -->
# Learning off-chain-influence-proof fees

A [Tangle Solve problem](https://solve.tangle.tools/problems/766c9188-2b0e-455a-bf90-acea59cdb58c).

## What is the problem?

Transaction fee mechanisms (TFMs) — the rules by which a blockchain selects transactions for inclusion and prices them — have been studied for incentive compatibility *among users and block producers acting on-chain*. A newer line of work asks about **off-chain influence**: can a block producer (miner/validator) credibly run a side-channel (bribe, off-chain auction, direct deal) that changes users' behavior and extracts surplus, thereby breaking the mechanism's guarantees? A mechanism is *off-chain-influence-proof* (OCIP) if no such profitable deviation exists.

The census record points at the open learning problem: current OCIP characterizations (Ganesh–Thomas–Weinberg 2026, on burn-based mechanisms) assume the mechanism designer knows the demand distribution (users' value prior) exactly, and prior-dependence is heavily restricted. The problem is to **design a TFM that (a) learns the demand distribution from observed bids/blocks online, and (b) maintains OCIP (or a quantified relaxation) throughout and at convergence — even when strategic agents (users colluding with producers) can poison the observations the learner sees.**

Formal setting: users with values drawn from an unknown distribution F over valuations for inclusion; each round, a block producer observes bids and may offer off-chain transfers; the mechanism chooses an allocation and payments (possibly burn) based on a learned estimate F̂ₜ. Goals: (i) regret bounds for revenue/welfare relative to the OCIP mechanism that knows F; (ii) a bound on the producer's maximal off-chain gains ε(ₜ), ideally ε → 0, where "gains" is measured against truthful on-chain play; (iii) robustness to strategic distribution shifts/poisoned bids designed to move F̂ₜ.

Ambiguity note: "bounded gains" may mean exact OCIP, ε-OCIP, or Bayesian OCIP; the solver should state which variant they target.

## Why does it matters?

Fee mechanism design is deployed at scale (EIP-1559 base-fee updates are themselves a learning rule), so a mechanism whose collusion-resistance silently breaks during learning is a live attack surface: producers could manipulate the learned fee parameters to open profitable off-chain deals. A solution would let adaptive fee mechanisms inherit the collusion guarantees previously available only with known priors, connecting online learning, auction theory, and blockchain incentive design. It would also directly generalize the strategic-poisoning literature from single-seller pricing to a setting where the "seller" (producer) is itself an adversary.

## What's already known?

- **OCIP characterization (Jan 2026):** Ganesh, Thomas & Weinberg characterize transaction fee mechanisms that are off-chain-influence-proof; the record indicates the answer centers on **burn-based mechanisms** (producers burn payments, earning only a prespecified reward), and that this characterization holds under a **known prior and restricted prior-dependence**. This is the baseline to relax. (Read the paper; the census summary is terse.)
- **TFM incentive compatibility:** Prior work (e.g., Roughgarden 2020/2021 on EIP-1559; Chung & Shi 2023 on TFMs and collusion; Ferreira et al. on dynamic fee mechanisms) establishes DSIC/collusion-resistance notions on-chain and, in some cases, that burn-like rules are the only way to prevent producer-user side deals. Details should be verified against the actual papers (verify).
- **Strategic poisoning in online pricing:** In repeated posted-price learning, buyers can misreport to induce lower future prices; known results give regret bounds under strategic behavior via incentive-compatible learning algorithms. The analogous attack here: users (coordinated by a producer) bid strategically to distort F̂ₜ so the resulting fee rule admits profitable off-chain deviations.

Gap: nobody has combined the two — an online, poisoning-robust learner whose *intermediate outputs* remain (approximately) OCIP.

## What would count as solving it?

Any of the following, in increasing strength:

1. **Impossibility:** a proof that no learning rule can guarantee εₜ → 0 off-chain gains with o(T) regret when observations are strategically poisonable — i.e., a fundamental learning–collusion tradeoff.
2. **Positive result:** an explicit mechanism + learning algorithm with (i) sublinear regret relative to the best prior-aware OCIP mechanism, and (ii) a proved bound on producer off-chain gains at every round (exact OCIP, ε-OCIP with εₜ = o(1), or a stated tradeoff between regret and ε).
3. **Lower/upper bound match:** regret bounds tight in T and in the collusion parameter, showing the cost of robustness.

A referee would want the model stated precisely (timing, information sets, off-channel commitment assumptions) and proofs, not simulations.

## How to start

1. Read Ganesh–Thomas–Weinberg (2026) first — extract exactly where the known prior enters the OCIP proof and what "prior-dependence restrictions" mean; that's the technical seam to attack.
2. Read Chung & Shi's TFM paper and Roughgarden's EIP-1559 analysis to master the standard TFM model (verify exact titles/years). Then read one strategic-robust online pricing paper to import the poisoning toolkit.
3. Natural approach: try the "burn everything + learn only the producer-independent component" route — if the OCIP mechanism burns all payments, the producer's off-chain leverage may be prior-independent, making learning free. Check whether this fails (e.g., allocation depends on F̂).
4. Main obstacle: the learner's estimate feeds the allocation rule, so poisoned observations change *who gets in*, not just prices — likely requiring robust statistics or incentive-compatible exploration.

## Key references

- P. Ganesh, C. Thomas, S. Weinberg (2026). *Characterizing Off-Chain Influence Proof Transaction Fee Mechanisms*. ITCS 2026, DOI:10.4230/LIPIcs.ITCS.2026.65. Characterizes which TFMs prevent profitable producer off-channel deals, via burn-based payments, under a known demand prior. https://doi.org/10.4230/LIPIcs.ITCS.2026.65
- T. Roughgarden (2020/2021). *Transaction Fee Mechanism Design for the Ethereum Blockchain* (ECON/ITCS). Shows EIP-1559's posted-price design is approximately incentive compatible for users and myopic miners (verify exact venue/claims).
- H.-K. Chung, E. Shi (2023). *Foundations of Transaction Fee Mechanism Design* (verify venue/year). Defines IC/collusion-resistance notions for TFMs and proves impossibilities for strong guarantees.
- Strategic poisoning in online pricing: no single canonical citation was provided; search for "strategic behavior in online learning pricing / incentive-compatible dynamic pricing" (e.g., work by Amin, Rostamizadeh, Syed; or recent robust dynamic pricing under strategic buyers) (verify).

## Warnings

- The census record is a single secondary summary; treat its characterization of the 2026 paper ("burn-identity," "prior-dependence restrictions") as unverified until you read the paper.
- Do not assume the off-chain channel is contractible or binding — the standard model has the producer merely *able* to offer side deals, and the timing/commitment assumptions materially change results.
- A common dead end: proving DSIC of the learning rule while ignoring that the *producer*, not just users, is the adversary — off-chain influence is a producer deviation.
- ε-OCIP definitions are fragile; fix one definition up front and state which collusion notion (DSIC vs. Bayesian IC, with/without producer risk aversion) you use.

---
*`BCGT-000281` · flagged for review*

## Acceptance guidance

Any of the following, in increasing strength:

1. **Impossibility:** a proof that no learning rule can guarantee εₜ → 0 off-chain gains with o(T) regret when observations are strategically poisonable — i.e., a fundamental learning–collusion tradeoff.
2. **Positive result:** an explicit mechanism + learning algorithm with (i) sublinear regret relative to the best prior-aware OCIP mechanism, and (ii) a proved bound on producer off-chain gains at every round (exact OCIP, ε-OCIP with εₜ = o(1), or a stated tradeoff between regret and ε).
3. **Lower/upper bound match:** regret bounds tight in T and in the collusion parameter, showing the cost of robustness.

A referee would want the model stated precisely (timing, information sets, off-channel commitment assumptions) and proofs, not simulations.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/766c9188-2b0e-455a-bf90-acea59cdb58c?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000281.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000281)
