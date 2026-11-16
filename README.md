# FECG-ST: Finite Cognitive State Transition (Research Draft v0.1, 2026-11-16)

> Status: draft, not locked. An independent research branch of FECG,
> not (yet) merged into the energy-convergence theory.
> Research question: given a finite cognitive system in an internal state
> at time t, by what mechanism does it form a new thought state at time t+1?
>
> This document strictly separates three levels, which must not substitute
> for one another:
>
> - **Neural mechanisms**: what might be happening in the brain?
>   (candidate correspondences, not conclusions)
> - **Functional hypotheses**: what functions must a cognitive system have?
>   (to be tested)
> - **Mathematical propositions**: what follows rigorously under explicit
>   assumptions? (proved / to be proved)
>
> Brain-region words appear only in the neural-mechanism layer and play no
> role in any mathematical argument. (Deletion test: deleting all
> brain-region words leaves §1–§5 and §7 fully intact and operational.)
>
> [中文版](README.zh-CN.md)

## 0. The question and the entry point

**Core question (A, priority):** How does the current thought produce the
next thought? I.e., the state-transition mechanism S_t → S_{t+1}.
**Sub-question (B, must be explained inside A):** Why does one candidate win
among several possible thoughts? I.e., the selection mechanism.
**Deferred:** C (prediction: how the brain knows the next sentence or next
thought in advance), D (strategy switching: association vs logical
inference). C and D depend on A's answer; separate branches (FECG-PD,
strategy switching).

**Three easily confused questions, separated in advance:** A (where does the
candidate content of the next thought come from), B (why does one candidate
win), C (how does a thought become the next sentence). Language production
≠ thought production: a complete idea can lack words, and fluent speech can
lack rigorous reasoning. This draft studies A (with B as a sub-problem);
language production is mentioned only as an output layer.

## 1. What is a thought state?

**Definition 1 (cognitive state).** Let the cognitive model state be

S_t = (X_t, G_t, M_t, H_t)

| Variable | Definition | Role |
|---|---|---|
| X_t | current active representation | what is being processed now |
| G_t | current goals and constraints | sets the direction of thought |
| M_t | accessible memory state | provides history and associations |
| H_t | summary of recent cognitive activity | retains recent states and transitions |

S_t is not the whole physical state of the brain; it is the state of the
chosen cognitive model. Example: X_t = "the cerebellum participates in
prediction", G_t = "explain how the next thought is produced",
M_t = {cerebellum, frontal lobe, prediction, attention}.

**Hypothesis H0 (finite effective representation — to be tested, not a fact).**
For a given task, the relevant dynamics can be approximated by a
finite-dimensional, finite-precision state representation. This does not say
the brain has finitely many neuronal states; it is the working hypothesis of
this branch, to be tested, not a universal fact.

**Note on circular definitions.** "A thought state is whatever one is
currently thinking" carries no mathematical information, so it is not used
as a definition. Definition 1's content is the values and value spaces of
the four components, not the empty phrase "what one is thinking".

## 2. Minimal three-stage model (functional-hypothesis layer)

Do not write S_{t+1} = F(S_t) directly — it hides competition and selection.
Decompose into three processes (not necessarily strictly serial in the brain;
they may run in parallel, interact, and iterate; as a minimal mathematical
decomposition they are worth studying):

**Stage A: Candidate generation.** C_t ~ Q_θ(C | S_t),
C_t = {c_1, …, c_n}, 1 ≤ n ≤ K. A candidate can be a concept, a judgment, a
memory fragment, a question, an action plan, or a thinking strategy. Q_θ
expresses how the current state shapes the candidate space. Whether
candidates are retrieved or composed is **left undecided**, as competing models:

- Q_retrieval(c | S_t): retrieved from memory;
- Q_composition(c | S_t, M_t): composed from the current state and memory.

**Stage B: Competitive selection.** c\*_t ~ Π_φ(c | C_t, S_t).
Score decomposition (a functional model, not a discovered brain formula):

R_i = w_a A_i + w_g G_i + w_p P_i + w_v V_i

| Factor | Meaning |
|---|---|
| A_i | association with the current state |
| G_i | match to current goals |
| P_i | predictive value for subsequent states |
| V_i | the system's current value assessment of the candidate |

Simplest selection mechanism (softmax):

P(c_i | C_t, S_t) = e^{β R_i} / Σ_j e^{β R_j}, β controlling determinacy.

Numerical check (scores 3, 2, 1, β = 2.0): e^6, e^4, e^2 give
p ≈ 86.7%, 11.7%, 1.6%. Small β → diffuse choice; large β → the
highest-scoring candidate dominates. (These numbers illustrate the model;
they are not brain data.)

**Stage C: Stabilization and update.** S_{t+1} = U(S_t, c\*_t, ξ_t),
ξ_t random disturbance or unmodeled input. Key property:
Q(C_{t+1} | S_{t+1}) ≠ Q(C_t | S_t) — the new thought changes the
conditions for the next round of candidate generation, forming a recursive
process.

## 3. Provable propositions (mathematical-proposition layer, verified)

Let the state space S be finite; each state generates at most K candidates;
Q and Π are probability distributions; U is deterministic given ξ.

**Proposition P1 (existence of the transition kernel).** Define

T(s, s') = Σ_C Q(C | s) Σ_{c∈C} Π(c | C, s) 1[U(s, c) = s'].

Then T(s, s') ≥ 0 and Σ_{s'} T(s, s') = 1, so the system is a finite-state
Markov chain.

*Proof.* Nonnegativity follows from nonnegativity of Q and Π. Summing over
s': Σ_{s'} 1[U(s,c) = s'] = 1 (U determines a unique next state), hence
Σ_{s'} T = Σ_C Q(C|s) Σ_{c∈C} Π(c|C,s) = 1. ∎

*Clarification P1′.* The U in §2 carries noise ξ_t; the U in P1 does not.
P1 holds with ξ fixed; in general take the ξ-marginal:

T(s, s') = Σ_C Q(C|s) Σ_{c∈C} Π(c|C,s) E_ξ[1[U(s,c,ξ) = s']].

Same conclusion, same proof (linearity of expectation). This is a
clarification, not a correction.

*Scope:* P1 only proves that this mechanism model forms a legitimate
probabilistic state-transition system; it does not prove the real brain
works this way.

**Proposition P2 (selection probability vs candidate score).**
p_i = e^{βR_i} / Σ_j e^{βR_j}; then

∂p_i/∂R_i = β p_i (1 − p_i), ∂p_i/∂R_j = −β p_i p_j (j ≠ i).

So for β > 0: raising a candidate's score raises its selection
probability; raising a competitor's score lowers it. ∎ (Standard softmax
differentiation.)

*Scope:* does not prove the brain uses softmax, nor that real candidate
scores have the four components of §2.

**Proposition P3 (limits of selection determinacy).** With n candidates:

- β → 0 gives p_i → 1/n (near-uniform choice);
- β → ∞ gives p_k → 1 for a unique highest-scoring candidate c_k,
  others → 0 (near-deterministic choice). ∎

*Scope:* whether a real cognitive system has such a single parameter β
needs experimental study.

**Proposition P4 (a finite-state system has a stationary distribution,
but need not converge).** T is a finite-state stochastic transition
matrix, so at least one stationary distribution π exists, πT = π. But
existence of a stationary distribution ≠ convergence to it.
**Counterexample:** two-state alternation 0→1→0→1 has stationary
distribution (0.5, 0.5), yet from a deterministic initial state the
distribution never converges. A unique stationary distribution and
convergence additionally require irreducibility, aperiodicity, etc. ∎

*Scope:* state transition must not be equated with cognitive convergence.
This is one of the dividing lines between this branch and FECG's
energy-convergence theory.

## 4. Three smuggled conclusions to watch for

**Problem 1: Does candidate generation already presuppose the next thought?**
If Q simply lists all possible next thoughts, it explains nothing about how
candidates arise. The generator itself needs further modeling
(Q(C | S, M), split into associative activation, memory retrieval, and
compositional construction).

**Problem 2: Is the scoring function just a relabeling of the outcome?**
If we see the system choose B and then say B had the highest composite
score, that is post-hoc explanation. The scoring factors must be specified
in advance and must independently measure or predict choices. (The R_i
decomposition in §2 is a functional model to be tested, not a conclusion.)

**Problem 3: Is the state really sufficient? (Most important.)**
If P(S_{t+1} | S_t, S_{t-1}) ≠ P(S_{t+1} | S_t), then the current state S_t
does not suffice to describe the system; history variables or
longer-timescale internal states must be added. This bears directly on
finite cognition and memory continuity. Test: add history information
stepwise and see whether predictive performance keeps improving
significantly; if it plateaus at a stable finite-dimensional approximation,
then H4 (§5) holds.

## 5. Hypothesis set v0.1 (functional-hypothesis layer, to be tested)

| Hypothesis | Content | Falsification direction |
|---|---|---|
| H1: state dependence | The next thought's probability distribution depends on the current thought state, P(S_{t+1} \| S_t) | After controlling history, environment, and task, the current state carries no additional predictive information about the next state |
| H2: candidate competition | Forming the next thought involves competition among several latent candidates, not a single deterministic unfolding | After operationalizing candidate representations, no repeatably observable competition or selection process |
| H3: state feedback | A newly formed thought changes subsequent candidate generation and selection, S_{t+1} → Q(C_{t+1} \| S_{t+1}) | After controlling external input, the new state has no detectable effect on the subsequent candidate distribution |
| H4: finite-state sufficiency | A finite-dimensional state representation suffices to predict the next thought state within a given task and error tolerance | Predictive performance keeps improving significantly as history information grows, with no stable finite-dimensional approximation |

*Caution on H2.* The brain may have several representations active in
parallel, but that does not mean they have all become independent candidate
thoughts. One must distinguish latent competition in neural activity from
subjectively reportable thought candidates.

## 6. Neural-mechanism layer (candidate correspondences, not conclusions)

| Functional module | Candidate neural systems | Current evidence boundary |
|---|---|---|
| Candidate generation | hippocampus, default mode network, temporal association networks | involved in memory retrieval, internal thought, associative activity |
| Candidate representation | temporal, parietal, and distributed cortical networks | involved in semantic, conceptual, and multimodal representation |
| Goal constraint | prefrontal cortex, ACC | involved in goal maintenance, conflict monitoring, control |
| Competitive gating | basal ganglia–thalamus–cortex loops | involved in cognitive and behavioral selection and gating |
| Prediction and timing adjustment | cerebellum and related cortical loops | involved in sequence prediction, error learning, timing |
| Language production | frontotemporal language networks, motor-related cortex | involved in lexical, syntactic, phonological, and articulatory planning |

*Correcting two common misconceptions:* the default mode network is not just
a "mind-wandering network", the prefrontal cortex is not the commander of
all thought, and the cerebellum is not a standalone prediction module. They
interact through large-scale cortical–subcortical loops. At present only
functional-correspondence hypotheses can be offered; Q, Π, and U cannot be
mapped onto three brain regions. Candidate generation may be distributed,
competitive selection may occur at several levels, and state updating may
proceed on several timescales.

*Where the cerebellum stands (this branch's judgment):* the most valuable
question about the cerebellum is not whether it directly produces the next
thought, but whether it helps the whole cognitive system predict the likely
outcome of current activity and adjust network activity in advance.
Formally: each functional system i forms its own prediction of the next
state, Ŝ_{t+1}^{(i)} = F_i(S_t), and the system forms the next state
through an integration mechanism
S_{t+1} = Φ(Ŝ_{t+1}^{(1)}, …, Ŝ_{t+1}^{(k)}, S_t). Φ is an abstraction to be
studied, not a claim that the brain has a single unified prediction
aggregator; and not all prediction should be attributed to the cerebellum.

## 7. The bottleneck: how is a candidate thought actually produced? (Distinguishability of A/B/C)

Three competing explanations (not necessarily mutually exclusive; they may
coexist at different levels):

- **A. Memory retrieval:** c_{t+1} ~ P(M | S_t). The next thought is an
  associated memory retrieved; thought continuity comes mainly from
  associative propagation of past experience.
- **B. Dynamic composition:** c_{t+1} = F(S_t, M_t). The current state
  combines with several existing representations to form a new candidate
  that never appeared whole before; thought is constructive, not mere
  retrieval.
- **C. Internal prediction:** Ŝ_{t+1} = P(S_t, θ_t). The system forms
  possible future states in advance from the current state and internal
  dynamics; some predictions enter subsequent thought.

**Goal of this section:** before arguing about which mechanism the brain
uses, ask whether a minimal model can be built in which the three produce
different, observable and falsifiable next-state distributions.

Let the item universe be U (finite) and the memory set M ⊆ U. A combination
algebra gives the constructible set Comb(S_t, M) ⊆ U; a predictive dynamics
gives a distribution P_pred(· | S_t) with support supp(P_pred).

- A's support ⊆ M (the next thought is always an existing memory item).
- B's support ⊆ M ∪ Comb(S_t, M) (may contain new composite items).
- C's support ⊆ supp(P_pred(· | S_t)) (may contain dynamically extrapolated items).

**Proposition D1 (A vs B/C, support).** If a next thought c ∉ M is
observed, model A is falsified. (A's support ⊆ M is part of its definition.)

**Proposition D2 (B vs C, support separation).** Suppose there is a state
S_t where Comb(S_t, M) and supp(P_pred(· | S_t)) differ. Then items in the
symmetric difference are observable distinguishing events:
c ∈ Comb \ supp(P_pred) can be produced by B and is assigned (near-)zero
probability by C; c ∈ supp(P_pred) \ (M ∪ Comb) can be produced by C and
not by B. Repeatedly observing the empirical next-thought distribution
from the same S_t distinguishes B from C by frequencies (or likelihood ratio).

*Minimal instance.* M = {a, b}; the only allowed combination is the pair
(a, b); the learned dynamics is a → c, where c ∉ M ∪ {(a, b)}. Then A
predicts the next thought ∈ {a, b}; B predicts ∈ {a, b, (a, b)}; C predicts
= c (with high probability). Observing (a, b) supports B; observing c
supports C (and falsifies A and B); observing only a/b is consistent with A,
but also with the low-weight branches of B and C — so the test must use
frequency distributions, not single observations.

**Proposition D3 (collapse conditions — when distinguishability vanishes).**

- If M is closed under combination (∀S_t, Comb(S_t, M) ⊆ M), then B's
  support = A's support and B collapses into A (up to weighting).
- If P_pred is supported on M and proportional to the association kernel,
  then C degenerates into A.
- If the combination rule is itself defined as "take the predicted item and
  combine it with memory", then B and C coincide by construction.

Hence the distinguishability of A/B/C **depends on the combination algebra
and the predictive dynamics being fixed independently**: if either is
redefined post-hoc to fit the observed data, any model can fit any
sequence — the same class of smuggling as Problem 2 in §4 (post-hoc scoring).

**Conclusion.** D1–D3 turn "which mechanism does the brain use?" into an
operational question: first fix the combination algebra and the predictive
dynamics (as explicit commitments of the model class), then test with
supports and frequency distributions. The three need not be mutually
exclusive; the D2 instance shows they can each dominate under different
conditions.

## 8. First minimal experiment (simulation, not EEG)

Compare three models on the same task and data:

| Model | Mechanism | Core question |
|---|---|---|
| M0 | direct state transition S_{t+1} = F(S_t) | the current state directly determines the next state |
| M1 | candidate generation + competitive selection | several candidates compete to produce the next state |
| M2 | candidate generation + competition + state feedback | the new state changes the next round's generation |

Compare: next-state prediction error, state-transition entropy,
thought-sequence continuity, speed of adaptation to goal changes, stability
under random disturbance, degree of history dependence.

**Statistical trap (must be guarded against in advance):** M2 has more
parameters, so better prediction may come from model complexity alone.
Parameter matching, held-out test sets, and complexity penalties are
required. Only if M2 still has a stable advantage at equal model capacity is
it worth pursuing the necessity of the feedback mechanism; if M0 already
suffices, there is no reason to insist on the three-stage model.

## 9. Relation to existing FECG theory (organizational structure, not a proven theoretical relation)

- FECG: dynamics and energy foundations (states, transitions, energy, stability).
- FECG-ST: thought-state transition (S_t → S_{t+1}, this branch).
- FECG-PD: predictive dynamics (Ŝ_{t+1}, how the system predicts the next thought in advance).
- FECG-CC: concept formation and stabilization (S/~, when stable predictive structures become concepts).
- FECG-ML: memory and history updating (M_t → M_{t+1}).

FECG-ST and FECG-PD must be distinguished: the former studies how the next
thought is **actually** formed; the latter studies how the system
**predicts** the next thought in advance. The two must not be conflated.

## 10. Next steps

1. **Priority: distinguishability of A/B/C (§7).** Implement the D2
   instance in a minimal toy world: fix the combination algebra and
   predictive dynamics, write down the three next-state distributions, and
   give the decision line for a frequency test (paper audit against the
   12-item checklist before any simulation).
2. M0/M1/M2 simulation experiment (§8), with complexity penalties and
   held-out test sets.
3. Testing H4's finite-state sufficiency (§4, Problem 3): add history
   information stepwise and see whether predictive performance converges to
   a plateau.
4. The neural-mechanism layer (§6) is on hold — revisit the correspondences
   only after §7 and §8 produce results.
