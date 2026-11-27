# E001_PROPOSAL — Narrowed research hypothesis and experiment design

**Task:** RES-01 (issue #3) · **Gate:** G01 · **Author:** Claude (research lead) · **Revision:** 2 (2026-11-27), responding to the director review on [PR #4](https://github.com/purushpsm147/EpistemicDB/pull/4) at head `2113214`.

**Status:** proposal only. This document is **not** the G02 preregistration. No thresholds are final, nothing is implemented, and no technology stack is chosen. Evidence references point to `PRIOR_ART.md` (written PA §x). Key claims are cited by their ID in PA §2.0 (E1–E8 economic, S1–S7 safety).

## 0. Director decisions recorded (PR #4 review, 2026-11-27)

| # | Question (rev. 1 §14) | Director answer | Where applied |
|---|---|---|---|
| D1 | Restrict the contribution? | **Yes.** The primary contribution is exact replay vs materiality validation. | §2 |
| D2 | Simulation first? | **Yes**, deterministic simulation first. | §12 |
| D3 | Authorization rule | Conservative authorization is **mandatory**. No unsafe baseline is needed except an explicitly labelled negative control. | §7, §5 |
| D4 | Second literature pass | A **targeted** second literature pass is required **before protocol freeze** (G02). | §14 |
| D5 | External replication | **Optional** unless a central claim depends on it. | §14 |

### Review corrections and where they are addressed

| Review item | Resolution |
|---|---|
| (1) Strongest-baseline set omitted TTL and query-result cache | §5. A single "eligible tuned baseline" set is used everywhere, including adaptive TTL and prompt-cached exact replay |
| (2) H2 stated as if a theorem | §2 H2 is rewritten as a hypothesis with both counterexample families to be tested |
| (3) Canonical replay with column-dropping or tolerance is not sound | §5. Lossless canonicalization (a baseline) is separated from invariance-based canonicalization (a materiality candidate that needs a sufficiency argument) |
| (4) Coverage: unlogged calls not detected; recompute does not cure missing access | §3.3 and §4. Detectable and latent coverage loss are separated; abstain/deny is a first-class outcome |
| (5) Authorization semantics; strictness overclaimed | §7. Grant semantics defined. Union-of-required-grants ≡ intersection-of-permitted-principals is stated; the "stricter" claim is withdrawn. Predicate/absence scope and revocation race added |
| (6) Snapshot/use semantics; denominators; zero ≠ proof | §4 and §9 |
| (7) K5 direction suspect | §13. K5 is replaced with an expected-cost criterion that conditions on recompute probability |
| "No market prevalence from an arbitrary grid" | §8 and §13. Prevalence-based criteria are removed; regimes are reported only as parameter regions |

---

## 1. Why the original E001 question is narrowed

1. **Dependency-directed invalidation is not novel** (PA §6.1).
2. **The economic default favours re-reading and recomputation** once the full pipeline and cached-input prices are counted (E1–E5, E7–E8). This evidence comes from specific workloads and open models (PA §6.2 scope caveat).
3. **Coverage failures are silent and severe** (S2, S3, S5).
4. **Reachability ≠ materiality** (S4). A contribution may remain here, but it must be precise about soundness.

## 2. Hypotheses (primary contribution restricted per D1)

**Unit of study.** A *knowledge item* K, made of:
- a conclusion c;
- the observations O = {(qᵢ, source, version/time, result rᵢ, completeness flag)} that were read to reach it;
- a derivation f (deterministic in E001a);
- an authorization context (§7).

**Reuse event.** At t_req, a principal requests c. A policy outputs one of three things: **serve stored c**, **serve a freshly recomputed c**, or **abstain/deny**.

### H1: Materiality vs exact replay (primary)
- **Claim.** There exist parameter regions where *materiality validation* has lower expected total cost than *exact lazy replay* while meeting the same safety requirement (§9).
  - Materiality validation: evaluate an executable predicate P; serve if P holds, otherwise recompute.
  - Exact lazy replay: re-execute all qᵢ; serve if every result is byte-identical after **lossless** canonicalization, otherwise recompute.
- **Expected region:** replay cost ≫ predicate cost, *and* a high immaterial-change rate.
- **The comparison is not only against B3.** H1 is also tested against **every eligible tuned baseline** (§5). The primary contrast is materiality vs replay (D1); the others decide whether the contrast matters in practice.

### H2: Validation cost depends on access paths, not monotonicity alone (hypothesis, not theorem)
- **Rev. 1 claimed** that sound predicates are cheap for monotone or positive conclusions and near replay-cost for absence or aggregate conclusions. **That is withdrawn as a general statement.**
- **Counterexample family A (monotone but expensive).** A conclusion like "a path exists from X to Y" or "some record satisfies an expensive join or external-API condition". Even with the original witness recorded, the sufficient check (the witness still satisfies the predicate) may need costly calls when the witness's own validation is expensive or the source has no point lookup.
- **Counterexample family B (absence or aggregate but cheap).** "No overdue invoice" or "A is the minimum price". These can be validated cheaply when the source has a suitable index or a maintained aggregate: an indexed existence probe, a B-tree min lookup, a version counter on the predicate's range, or result-change notification (Oracle QRCN, PA §4).
- **Hypothesis to test.** Validation cost is predicted by (i) the access paths available at the source (point, range, indexed aggregate, notification, none), (ii) the witness structure of the conclusion, and (iii) the predicate's selectivity. Monotonicity is predicted to be insufficient on its own. E001a must include both counterexample families as named workload cells.

### H3: Coverage loss (safety)
- **Detectable loss** is coverage loss the policy can observe: a truncated or paginated result without a completion flag, a known tool call with no receipt, an unversioned source where the validator class needs versions, an unknown permission state. Every policy must **fail closed** on it (§3.3).
- **Latent loss** is coverage loss the policy *cannot* observe: an unlogged call, information that entered via the prompt rather than a tool, a side channel, a derivation that used a fact not in O. **No receipt-based policy can detect latent loss.** Fail-closed does not help.
- **What H3 measures.** The unsafe rate of every policy under injected latent loss, and the savings erosion caused by fail-closed under detectable loss. These are separate quantities.
- **What protects against latent loss.** Only an architectural assumption, *all reads go through a mediated, instrumented gateway*, can bound it. E001a states this assumption explicitly and also runs with it violated.

### H0: Null
- No validation-based candidate beats the best eligible tuned baseline (§5) under the safety requirement in any declared parameter region (§8). This would be a valid negative result. It must be published, not tuned away.

## 3. Soundness definitions

### 3.1 Validator soundness classes

| Class | Definition | Allowed claim |
|---|---|---|
| **S1 Deterministic sufficient** | A machine-checkable argument shows that, for all source states s satisfying the stated assumptions (complete observation, deterministic f, mediated reads), V(s)=true ⇒ f(observe(s)) = c. | "Serving is correct under the stated assumptions." It is not a claim about latent coverage loss. |
| **S2 Empirically validated** | V's false-accept rate is estimated on held-out worlds against the oracle, with a confidence interval. | "False-accept ≤ x (one-sided 95% upper bound) on distribution D." It does not transfer off-distribution. |
| **S3 Unverified** | An LLM judgement or a heuristic. | No safety claim. A pre-filter only. |

### 3.2 Non-implications E001 must test
1. **A predicate true at derivation is not sufficient later.** Example: phantom insert of a cheaper supplier against "A is cheapest" (PA §5.2).
2. **Observation preservation is not truth.** Replay equality shows the source did not change for those queries. It shows nothing about whether the source, or f, was correct.
3. **Version equality is sufficient for staleness under complete observation but is not necessary.** It over-fires on immaterial changes (Tether's version trigger, PA §2.1).
4. **Reachable ≠ material** (S4).
5. **Validation is not atomic with use** (§4).
6. **Lossy canonicalization is a materiality claim, not replay.** Dropping columns, tolerating float differences or ignoring order where f is order-sensitive is sound only with a *sufficient-invariance argument*: f(r) = f(canon(r)) for every r reachable under the assumptions. Without such an argument it is S2 at best (§5, C-policies).

### 3.3 Fail-closed conditions (detectable coverage loss)
**Rule.** The policy must not serve stored c if any of the conditions below holds. What it must do instead depends on whether a fresh recompute can be trusted:

| Condition | Action |
|---|---|
| Any observation lacks a completion flag, or is flagged truncated or partial | Recompute **only if** the recompute can itself obtain complete observations. Otherwise **abstain** |
| A source is non-deterministic or unversioned and the validator needs versions | Recompute, or abstain if the recompute is also non-deterministic for c |
| The reader's authorization over the required record set (§7) is unknown or insufficient | **Deny.** Recompute does not cure missing access |
| The validator is S3 with no S1/S2 follow-up | Fall through to B3 or recompute |
| The push-invalidation lag bound is unknown (push variants only) | Treat as no notification |

**Key point.** Recompute does not cure an incomplete source or missing access. A fresh recompute over an incomplete or inaccessible source is just as wrong. In those cases the correct output is abstain or deny, and that is scored separately (§9).

## 4. Time and snapshot semantics
- **Timestamps per request:**
  - **t_req**: request arrives.
  - **t_val**: the validation or replay reads execute; this can be an interval [t_val0, t_val1] if reads are not atomic.
  - **t_serve**: the answer is returned.
  - **t_use**: the consumer acts. E001a models t_use = t_serve + Δ_use with a configurable Δ_use.
- **Source model.** A versioned store with a global logical clock. It offers optional **snapshot reads** (all reads in [t_val0, t_val1] at one version) or **non-snapshot reads** (each read at its own version), configurable per source to model external APIs.
- **Primary correctness reference:** the oracle's c computed on the full state at **t_serve**.
- **Secondary reference:** the oracle's c at **t_use**, which measures time-of-check to time-of-use (TOCTOU) exposure. Reported separately and never mixed into the primary metric.
- **Concurrency injection.** Writes land inside [t_val0, t_val1], inside (t_val1, t_serve], and inside (t_serve, t_use], each at configurable rates. Revocations are injected the same way (§7).

## 5. Baselines: the single eligible tuned set

**Eligibility.** A baseline is *eligible* for H1/H0 comparisons if, after tuning on development seeds, it meets the safety requirement (§9). Baselines that cannot meet it are kept and reported as **labelled negative controls** (D3), never as the comparison point.

| ID | Policy | Notes |
|---|---|---|
| B0 | **Recompute always** (queries plus inference) | Inference priced with a cached-input multiplier for stable prefixes. The prompt-cached recompute baseline |
| B0f | **Source-filtered recompute** | Re-reads only task-allowed sources (2610.03902's strongest pipeline; E4) |
| B1 | **Fixed TTL** τ | τ tuned per workload. Not sound, so eligible only if it meets the safety requirement in that region |
| B1a | **Adaptive TTL** | τ set per item from the observed change rate of its sources (e.g. estimated hazard), plus HTTP-style revalidation (RFC 9111) on expiry. Same eligibility rule as B1 |
| B2 | **Query-result cache** keyed by query text plus parameters, invalidated on any write to a referenced table/object | The OCN / MySQL query-cache analogue. Granularity is a tuned parameter (table vs row-range) |
| B3 | **Exact lazy replay** with lossless canonicalization only | Re-run every qᵢ and compare. On mismatch, recompute f |
| B3c | **Prompt-cached exact replay** | B3, but any recompute after a mismatch reuses the cached prompt prefix (the same stable context as the original derivation), priced at the cached-input rate |
| B4L | **Lossless canonical replay** | B3 with canonicalization that is provably information-preserving for f: set/bag ordering when no ORDER BY applies, encoding normalization, stable serialization. Sound under the §3.1 assumptions |
| B5 | **Version-stamp validation** | Compare source versions of observed records or keys (PlanFence / Invalidation Contracts analogue). Requires versions |
| B6 | **Dependency-cascade memory** (Tether/TMS-style) | Withdrawal triggers recompute |
| NC1 | **Negative control: unconditional reuse** | Labelled unsafe. Calibrates the harness's ability to detect unsafe reuse |
| NC2 | **Negative control: no authorization check** | Labelled unsafe. Calibrates authorization tests |

**Rule.** H0 and every kill/narrow criterion compare against **min over all eligible baselines** in {B0, B0f, B1, B1a, B2, B3, B3c, B4L, B5, B6}. Tuning effort is equal and logged (§10).

## 6. Candidate policies

| ID | Policy | Class |
|---|---|---|
| C1 | **Materiality predicate with a sufficiency argument** (includes absence clauses where needed; uses an indexed or aggregate access path when available) | S1 |
| C1i | **Invariance-based canonical replay**: replay plus lossy canonicalization (projection onto the columns f reads, float tolerance) **with** a sufficient-invariance argument | S1 if the argument is checked, otherwise S2 |
| C2 | **Cheaper predicate without full sufficiency** (e.g. omits absence clauses) | S2, reported with a one-sided upper bound on false-accept |
| C3 | **Tiered**: an S2/S3 pre-filter, then B4L/B3, then recompute | The class of the path that actually served |
| C4 *(later gate only)* | LLM claim-check (2609.25130 style) | S3 unless calibrated |

## 7. Authorization model (conservative, mandatory per D3)

**Grant semantics** (E001a):
- Principals 𝒫. Each source record r has a read ACL, ACL_t(r) ⊆ 𝒫, which may change over time t.
- Equivalently, each principal p has a grant set G_t(p) = {r : p ∈ ACL_t(r)}.

**Required record set R(K)** is the union of:
- (a) every record observed in O;
- (b) for conclusions with a quantifier, absence or aggregate, the **entire predicate domain** D(c): every record that *could* satisfy, or change, the predicate. This is not just the records returned. The conclusion "no overdue invoice for customer X" depends on every invoice of X, including ones the reader cannot see.

**Serve rule.** Serve K to p at t_serve only if R(K) ⊆ G_{t_serve}(p). Equivalently, p ∈ ⋂_{r∈R(K)} ACL_{t_serve}(r).

**Equivalence note (correcting rev. 1).** Under this model, "the union of required grants is covered" and "p is in the intersection of the permitted principals" are the **same condition**. Rev. 1 claimed the rule was stricter than MAP-Graph's scope intersection; that claim is withdrawn. The real differences from MAP-Graph (PA §2.2) are (i) *which* record set is used (observed plus predicate domain, versus visible-at-write) and (ii) *when* it is evaluated (at serve time, versus at write time). Whether these differences matter is an empirical question for E001a; no strictness is claimed without proof.

**Absence over partially visible domains.** If p cannot read all of D(c), stored c is **denied**. The policy may instead recompute a *different, labelled* conclusion c_p over p's visible subset, which is reported as a distinct outcome and never passed off as c.

**Revocation race.** Revocations are injected in (t_val, t_serve] and (t_serve, t_use]. The **linearization point** for authorization is the serve-time check against ACL_{t_serve}. Revocations after t_serve are reported as post-serve exposure, separately from unauthorized serves.

**Unknown ACL** for any r ∈ R(K) → **deny** (§3.3).

## 8. Workload sweep and regime reporting
- **Data.** Synthetic, generator-based worlds only: a versioned relational source plus simulated external APIs with configurable access paths (point, range, indexed aggregate, notification, none).
- **Conclusion templates.** Monotone existence, threshold, min/max, count, absence, top-k, multi-hop derived-of-derived. Both H2 counterexample families must be present as named cells.

**Sweep axes:**

| Axis | Range |
|---|---|
| Replay cost / inference cost ratio | 10⁻³ … 10² |
| Validation cost / replay cost ratio | Determined by access path; swept via the access-path axis |
| Access paths per source | point, range, indexed aggregate, notification, none |
| Repeat rate per item | 1 … 100 reuses |
| Material change rate μ / immaterial change rate ι | μ 0 … 0.5, ι 0 … 0.9 per interval |
| Result size and pagination | 1 … 10⁴ rows; page limits on or off |
| Cache economics | cached-input multiplier c ∈ {1.0, 0.5, 0.25, 0.1, 0.025} (0.025–0.1 matches the reference prices cited in 2611.22238 §2); cache-write multiplier {1.0, 1.25, 2.0}; TTL expiry on or off |
| Detectable coverage loss | 0 … 30% of observations |
| Latent coverage loss | 0 … 30% of derivations, mediated-gateway assumption on or off |
| Event / notification lag | 0 … several intervals |
| Permission churn | revocation rate 0 … 0.2; cross-principal share 0 … 0.5; partially visible predicate domains on or off |
| Source determinism | deterministic; non-deterministic order; non-deterministic content |
| Concurrency | write and revocation injection rates per window in §4: 0 … 0.1 |

**No prevalence claims.** The grid is a design choice, not a sample of real deployments. Results are reported as a **regime map over parameter regions**. Nothing is said about how common a region is in practice. Where a region is motivated by an external source (e.g. cache price ranges), that source is cited, and still no frequency claim is made.

## 9. Outcomes, denominators and safety requirement

**Outcome partition per request** (mutually exclusive):
1. **Served-stored, correct** (vs oracle at t_serve)
2. **Served-stored, incorrect** (unsafe reuse)
3. **Served-fresh, correct**
4. **Served-fresh, incorrect.** The recompute itself was wrong: a concurrent write during recompute, a non-deterministic source, or an incomplete source that was not detected
5. **Abstain/deny, warranted.** The oracle confirms the request could not be answered correctly or authorized
6. **Abstain/deny, unwarranted.** A correct answer was available
7. **Unauthorized serve.** Any serve with R(K) ⊄ G_{t_serve}(p). This flag can co-occur with 1–4, and is counted separately

**Rates, with explicit denominators:**

| Rate | Formula |
|---|---|
| Unsafe-reuse rate per request | (2) / all requests |
| Unsafe-reuse rate per served reuse | (2) / ((1)+(2)) |
| Fresh-recompute error rate | (4) / ((3)+(4)) |
| Unwarranted-abstention rate | (6) / all requests |
| Unauthorized-serve rate | (7) / all requests, and (7) / all serves |
| TOCTOU exposure (secondary) | served answers incorrect vs the oracle at t_use, over all serves |

**Safety requirement for eligibility.**
- S1 candidates and sound baselines: **zero observed** unsafe reuse and zero unauthorized serves. This is always reported with the **one-sided 95% upper bound** (exact Clopper–Pearson; ≈3/n when zero events are observed in n trials, the "rule of three"). **Zero observed failures is not proof of zero risk.** The bound is the claim.
- S2 candidates: the upper bound must be ≤ ε, a value fixed at G02.

**Cost metrics.**
- Total cost per request, end to end. This covers derivation, receipt storage, validation, replay, recompute and inference. Uncached-input, cached-input and output tokens are priced separately; query cost units and latency are added.
- Report **cost at matched safety**: in each region, the cheapest eligible policy.

## 10. Oracle, tuning, seeds and uncertainty
- **Oracle.** The generator owns the full state at every logical tick and evaluates c, D(c) and the ACLs exactly. There are two independent evaluators (imperative and declarative), cross-checked. There is no LLM judge. Known weakness: both evaluators share the generator.
- **Tuning.** An equal, logged budget per policy on development seeds. TTL, adaptive TTL, B2 granularity, lossless canonicalization rules and C-predicates are all tuned under the same budget.
- **Held-out seeds** are sealed before any candidate runs, and run once.
- **Uncertainty.** Cluster bootstrap over worlds (≥2,000 resamples) for cost differences. Exact binomial bounds for rare safety events. A small, pre-declared primary family; everything else is exploratory.
- **Retention.** All raw outputs, failures and negative controls are kept.

## 11. What E001 will not claim
- That observation preservation implies truth.
- That LLM materiality judgements are sound.
- That synthetic regions reflect real-world prevalence.
- That zero observed failures means zero risk.
- Any product form before G05.

## 12. Staging (per D2)
- **E001a.** A deterministic simulation: f is code and inference cost is a parameter. No external services and no paid infrastructure. This is the proposed G03 scope if G02 approves it.
- **E001b** *(separate approval)*. A small LLM-in-the-loop check with a locally served open model, to test behavioural side effects (e.g. extra steps, E7–E8).
- **E001c** *(separate approval, owner spend approval)*. Hosted-API pricing validation.

## 13. Kill / narrow criteria (structure only; thresholds fixed at G02)

"Best eligible baseline" always means the minimum over the full eligible set in §5.

| Trigger | Action |
|---|---|
| K1: In **no** declared parameter region does any C-policy have lower expected cost than the best eligible baseline while meeting §9 | **Kill** the materiality claim; publish the negative result |
| K2: C-policies beat the best eligible baseline only as S2 (bounded but non-zero false-accept), never as S1 | **Narrow** to "empirical pre-filter in front of replay"; no soundness claim |
| K3: S1 wins occur only for conclusion classes with cheap access paths (indexed, aggregate or notification), whatever their monotonicity | **Narrow** to sources exposing those access paths, i.e. toward a DBMS-extension framing rather than a generic agent gateway |
| K4: Fail-closed under realistic detectable loss removes > *q\** of the C-policy savings, *or* any policy is unsafe under latent loss unless the mediated-gateway assumption holds | **Narrow** to mediated or instrumented read paths, and state the assumption as a deployment requirement |
| K5 (revised): The expected-cost advantage of validation over replay is below *δ\** in every region. Per request, the comparison is: **C cost** = V + P(recompute \| C)·(R_q + I) against **B3c cost** = R_q + P(mismatch \| B3)·I_cached, where V = validation cost, R_q = replay query cost, I = inference cost and I_cached = prompt-cached inference cost. The advantage vanishes when V ≈ R_q, or when P(recompute \| C) ≈ 1 | **Narrow** toward replay (receipt log plus replay library). Note: high inference cost *favours* reuse whenever the recompute probability is low. Rev. 1's K5 had this backwards and is withdrawn |
| K6: B3, B3c or B4L is within *δ\** of the best policy everywhere | Recommend **replay as a library**, not a database |

Thresholds *q\** and *δ\** and the S2 ε are set at G02.

## 14. Pre-G02 conditions and remaining open items

**Targeted second literature pass (D4)**, required before protocol freeze. Proposed scope:
1. Adaptive / freshness-aware TTL for LLM and agent caches.
2. Prompt-cached replay or recompute cost studies after 2026-11-17.
3. Recent query–update independence and irrelevant-update work for aggregates and absence queries, and index-based validation.
4. Authorization of derived data: view-based access control and inference control in recent literature.
5. Any new exact-replay vs materiality studies.

**Other items:**
- **External replication (D5):** optional. Currently no central E001 claim depends on an external result. If G02 makes one central (e.g. the 2610.03902 cost ordering, used to set baselines), replicate it or treat it only as motivation.
- **Remaining unverifiable claims:** see PRIOR_ART Appendix B.
