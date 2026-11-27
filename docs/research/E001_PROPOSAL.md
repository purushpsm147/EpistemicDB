# E001_PROPOSAL — Narrowed research hypothesis and experiment design

**Task:** RES-01 (issue #3) · **Gate:** G01 · **Author:** Claude (research lead) · **Date:** 2026-11-27

**Status:** proposal only. This document is **not** the G02 preregistration. No thresholds are final, nothing is implemented, and no technology stack is chosen. Evidence references point to `PRIOR_ART.md` (written PA §x).

---

## 1. Why the original E001 question must be narrowed

The README asks whether "execution-derived observation receipts and materiality-aware validation reduce total investigation cost at matched correctness, freshness and authorization compared with strong TTL, prompt-cached recomputation and exact query replay baselines". The prior-art review changes that question in four ways:

1. **Dependency-directed invalidation is not novel.** TMS, view maintenance, build systems and at least five 2026 agent-memory systems already do it (PA §6.1). E001 must not test "does invalidation help?".
2. **The economic default favours re-reading and recomputation** once full-pipeline cost and prompt caching are priced (PA §6.2 items 1–2). A win for reuse is plausible only in specific regimes, and E001 must find them or show they do not exist.
3. **Dependency or observation coverage failures are silent and severe** (PA §6.2 item 3). Any reuse scheme must fail closed on unknown coverage, and E001 must measure what that costs.
4. **Reachability ≠ materiality** (PA §6.2 item 4). This is where a contribution may remain. It must be made precise about soundness, because LLM materiality judgements are empirical only.

## 2. Narrowed hypotheses

**Unit of study.** A *knowledge item* K, made of:
- a conclusion c;
- the observations O = {(qᵢ, source, version/time, result rᵢ, completeness flag)} that were read to reach it;
- a derivation f (deterministic code, or LLM inference);
- an authorization context (principal, scopes of inputs).

**Reuse event.** At a later time t′, a principal requests c. A *policy* decides one of three things: reuse K, validate-then-reuse, or recompute.

### H1: Materiality vs exact replay (primary)
- **Claim.** For knowledge items whose validity condition can be expressed as an executable predicate P over the source, *materiality validation* costs less than *exact lazy replay*, at **zero observed unsafe reuse** (both are designed to be sound under complete observation).
- **What each policy does.**
  - Materiality validation: evaluate P on current state; reuse if P holds, otherwise recompute.
  - Exact lazy replay: re-execute every qᵢ; reuse if every rᵢ is byte-identical, otherwise recompute f.
- **Where we expect a win.** Only in the region where (a) replay cost ≫ predicate cost, *and* (b) the rate of immaterial changes (source changed, conclusion did not) is high.
- **Expected outcome outside that region.** Exact replay, canonical replay or plain recompute-with-prompt-cache is no worse.

### H2: Soundness boundary (theory plus test)
- **Claim.** A cheap sufficient predicate exists for *monotone or positive* conclusions (existence, membership, "at least one approval").
- **The hard class.** Conclusions with universal or absence components ("cheapest", "none overdue", top-k, count = n). For these, any **sound** predicate must check the absence of new satisfying records. Its cost approaches re-executing the aggregating query, so the materiality advantage should vanish or shrink. This links to irrelevant-update and query-independence results for restricted query classes (Blakeley et al.; Elkan; Levy & Sagiv; PA §4).
- **What E001 should do.** Measure where the boundary falls in practice. Do not assume it.

### H3: Coverage failure modes (safety)
- **Claim.** Under *incomplete* observation every validator can be unsafe, whatever its class. Examples of incomplete observation: an unlogged tool call, a truncated page, an LLM-inferred dependency, a predicate omitting justified scope.
- **What fail-closed must do.** Treat any item with unknown coverage, unknown permission or a non-deterministic source as recompute. This removes unsafe reuse but erodes savings.
- **What we measure.** The size of that erosion.

### H0: Null and kill framing
- **The null.** No validation-based policy beats the best tuned baseline, chosen from {exact replay, canonical replay, version-stamp validation, recompute with prompt caching, query-result cache}, at matched safety in any regime with non-trivial prevalence.
- **If the null holds, it is a publishable negative result.** We record it and do not tune it away.

## 3. Soundness limits (definitions E001 must enforce)

### 3.1 Three soundness classes for a validator V attached to K

| Class | Definition | Allowed claim |
|---|---|---|
| **S1 Deterministic sufficient** | There is a machine-checkable argument that, for every source state s, V(s)=true ⇒ f(observe(s)) = c. This needs f to be deterministic and observation to be complete. | "Reuse is correct under the stated assumptions." |
| **S2 Empirically validated** | V's false-accept rate is estimated on held-out worlds with an execution oracle, with a confidence interval. | "Reuse error ≤ x with confidence y on distribution D." It does not transfer off-distribution. |
| **S3 Unverified inference** | V is an LLM judgement or a heuristic with no estimate. | No safety claim. Use only as a pre-filter in front of S1/S2 or full recompute. |

### 3.2 Explicit non-implications E001 must test, not assume
1. **A predicate true at derivation time is not necessarily sufficient later.** Example: c = "Supplier A is the cheapest eligible supplier". The predicate P = "A still eligible ∧ A.price unchanged" stays true when a *new* supplier B with a lower price is inserted. That is a phantom (Eswaran et al. 1976; PA §5.2). A sound P must also assert "¬∃ eligible x: x.price < A.price", i.e. re-run the aggregate.
2. **Preserving the observation is not truth.** An identical replay result shows only that the *source* did not change for those queries. It does not show that the source was correct, or that f (if an LLM) derived c correctly. Replay validates *staleness*, not *truth* (Director Desk #1; DerivAudit, PA §3).
3. **Version equality is sufficient for staleness but not necessary.** It over-fires on immaterial changes; Tether's version trigger over-withdraws 9.6% (PA §2.1). Materiality aims to recover that waste without losing sufficiency.
4. **Reachable ≠ material.** Coverage-derived reach has 0.415 precision for falsification (PA §3, 2609.25130).
5. **Validation is not atomic with use.** Without a snapshot shared with the effect, a change can land between validate and use (TOCTOU; PA §5.4). E001 must inject this and report it separately.

### 3.3 Fail-closed conditions (mandatory for every candidate policy)
The policy must recompute or deny, never reuse, if any of these hold:
- any observation lacks a completeness flag, or is flagged truncated or paginated-incomplete;
- any source is marked non-deterministic or lacks versioning, and the validator class needs it;
- the reader's authorization over *every* input is unknown, or narrower than the deriver's (see §6);
- the validator's soundness class is S3 and no S1/S2 check follows;
- the event or notification lag bound is unknown, for push-based invalidation variants.

## 4. Baselines (all tuned with equal effort)

| ID | Policy | Notes |
|---|---|---|
| B0 | **Recompute always.** Re-run queries and inference at every request. | Price inference with cached-input rates for stable prefixes. This is the "prompt-cached recomputation" baseline (PA §2.10). |
| B0f | **Source-filtered recompute.** Like B0, but re-reads only the sources the task allows. | The strongest re-reading pipeline in 2610.03902 (PA §2.7). |
| B1 | **Strong TTL.** Reuse if age < τ. | τ tuned per workload on development seeds to minimize cost subject to the safety constraint. Report its unsafe rate honestly; TTL is not sound. |
| B2 | **Query-result cache** keyed by exact query text, invalidated by object/table change. | The MySQL-query-cache / Oracle Object Change Notification (OCN) analogue (PA §4). |
| B3 | **Exact lazy replay.** At reuse, re-run all recorded qᵢ and compare result hashes. Reuse on equality, otherwise recompute f. | Sound under deterministic sources, complete observation and deterministic f. **A serious candidate** (README). |
| B4 | **Canonical replay.** Like B3, but results are canonicalized first: ordering, irrelevant columns, float tolerance. | Early cutoff at the observation level (build systems à la carte). |
| B5 | **Version-stamp validation.** Compare the source versions of the observed records or keys. | PlanFence / Invalidation Contracts analogue (PA §3). Needs versions to be available. |
| B6 | **Dependency-cascade memory.** Tether/TMS-style withdrawal over recorded edges. | Withdrawal triggers recompute. |

## 5. Candidate policies under test

| ID | Policy | Soundness class |
|---|---|---|
| C1 | **Materiality predicate, S1.** A hand-written or derived sufficient predicate per conclusion template, including absence clauses where H2 requires them. | S1 |
| C2 | **Materiality predicate, S2.** A cheaper predicate *without* absence clauses, with an empirically estimated false-accept rate. | S2 (reported with a confidence interval) |
| C3 | **Tiered.** Run C2 or an S3 filter as a cheap pre-check; on doubt fall through to B4 or B3; then recompute. | Inherits the strongest class on the path that actually reused |
| C4 *(optional, later gate)* | **LLM claim-check** ("does *this* claim still hold given this diff?") in the style of 2609.25130. | S3 unless calibrated to S2 |

## 6. Authorization model for E001 (minimal)
- Each source record carries a scope set. A knowledge item's *required scope* is the union of the scopes of every record in its **observation and justified scope**. This differs from MAP-Graph's intersection-of-visible approach (PA §2.2) and is stricter.
- **Reuse is allowed only if** the reader's grants ⊇ the required scope, *evaluated at reuse time* (revocation must take effect).
- **Fail closed when coverage is unknown.**
- **Safety tests:**
  - revocation between derivation and reuse;
  - cross-principal reuse;
  - a derived aggregate that leaks the existence of a hidden record ("no overdue invoices" over a set that includes invoices the reader cannot see).
- **Out of scope for E001:** general information-flow control and differential privacy.

## 7. Workload sweep

**Data.** Synthetic, generator-based worlds only: a versioned relational source plus simulated external APIs. Public datasets are an optional stretch. No credentialed data. eICU demo-style data is *not* proposed.

**Conclusion templates.** Monotone (exists or member), threshold, aggregate (min, max, count), absence (none-with-property), top-k, and multi-hop derived-of-derived.

**Sweep axes** (each item is an axis and its proposed range):

| Axis | Proposed range |
|---|---|
| Replay cost / inference cost ratio ρ | 10⁻³ … 10² (query cost units vs priced LLM tokens) |
| Repeat rate per item | 1 … 100 reuses over a horizon |
| Material change rate μ | 0 … 0.5 per interval |
| Immaterial change rate ι | 0 … 0.9 per interval |
| Result size | 1 … 10⁴ rows, with and without pagination limits |
| Cache economics | cached-input multiplier {1.0, 0.5, 0.1}; cache-write multiplier {1.0, 1.25, 2.0}; TTL expiry on or off (parameterized, not tied to one vendor) |
| Observation coverage | 100%, 99%, 95%, 70% (unlogged calls, truncated pages, missing absence clauses) |
| Event or notification lag | 0 … several intervals, for push-based variants |
| Permission churn | revocation rate 0 … 0.2; cross-principal reuse share 0 … 0.5 |
| Source determinism | deterministic; non-deterministic ordering; non-deterministic content (e.g. a "latest" endpoint) |
| Concurrency | changes injected between validate and use (TOCTOU) at rates 0 … 0.1 |

## 8. Metrics
- **Unsafe reuse rate.** The fraction of reuse events where the served c ≠ the oracle's c at use time. Stratified by cause: phantom, update, deletion, revocation, truncation, TOCTOU, non-determinism.
- **Unauthorized reuse rate.** Served to a principal lacking the required scope.
- **Missed-reuse rate.** Recomputed although c was still valid (an efficiency loss).
- **Total cost per request, end to end.** This covers derivation, receipt and record storage, validation, replay, recompute and inference. Uncached-input, cached-input and output tokens are priced separately (PA §2.10), plus query cost units and wall-clock latency (simulated plus measured where feasible).
- **Cost at matched safety.** For each regime, the cheapest policy with unsafe = 0 (S1-class policies), or with unsafe ≤ ε (reported separately for S2).
- **Regime map.** The winner by (ρ, ι/μ, coverage, cache multiplier) region, with uncertainty bands.

## 9. Ground-truth oracle
- **The generator owns the full world state at every tick.** The oracle recomputes c with the reference deterministic f over the *complete* current state, and evaluates authorization from the ground-truth grants. There is no LLM judge anywhere in the oracle (following Fallout Eq. 1, MemShift and 2609.25130; PA §5.8).
- **Two oracle implementations, cross-checked.** For example, an imperative evaluator and a declarative or query evaluator (as in 2611.19859 §4.1). A known weakness: both share the generator.

## 10. Fair baseline tuning, seeds and uncertainty
- **Equal tuning budget.** Every policy with parameters (τ for TTL, canonicalization rules, predicate templates) gets the same budget on *development seeds*. The tuning procedure is reported.
- **Held-out seeds.** Worlds are sealed before any candidate policy runs on them. The results run is executed once.
- **Uncertainty.** Cluster bootstrap over worlds (≥2,000 resamples) for differences. Report intervals, not just means.
- **Multiple comparisons.** Pre-declare a small primary family (H1 at three regime points). Everything else is exploratory.
- **LLM-in-the-loop stages** (if authorized later) use ≥3 seeds or replays per configuration. They report non-determinism, because hosted models drift even at temperature 0 (Tether §5).
- **Raw outputs, the cache of every model or query call, and all failed runs are kept** (Director Desk #1).

## 11. Staging and resources (no paid infrastructure assumed)
- **E001a (proposed for G03 if approved).** A purely deterministic simulation in which f is code and inference cost is a *parameter*. It isolates the economics and soundness questions at near-zero cost on a laptop or CI-scale machine, with no external services.
- **E001b (separate approval required).** A small LLM-in-the-loop replication with a locally served open model, to check that the parameterized inference costs and behavioural effects (e.g. extra steps, PA §2.10) do not overturn E001a.
- **E001c (stretch, separate approval).** Hosted-API pricing validation. This needs owner approval for any spend.

## 12. Preregistered kill / narrow criteria (structure only; thresholds TBD at G02)

| Trigger | Action |
|---|---|
| K1: No C-policy beats the best of {B0 with cache, B0f, B3, B4, B5} at matched safety in any regime whose prevalence ≥ *p\** in the sweep | **Kill** the materiality-validation claim; publish the negative result |
| K2: C-policies win only where their unsafe rate > 0 (S2/S3 only) | **Narrow** to "empirical pre-filter in front of replay"; no soundness claim |
| K3: Sound (S1) predicates win only for monotone or positive conclusions (H2 confirmed) | **Narrow** scope to that conclusion class; document the absence-class negative result |
| K4: The fail-closed coverage requirement removes > *q\** of savings at realistic coverage (≤95%) | **Narrow** to sources with native versioning or notification, i.e. a DBMS-extension direction rather than an agent-layer gateway |
| K5: Inference cost dominates validation cost by > *r\** in all plausible regimes | **Narrow** the research toward inference reuse (prompt and KV caching) rather than knowledge validation |
| K6: Exact or canonical replay (B3/B4) is within *δ\** of the best policy everywhere | Recommend **replay as a library** (a receipt log plus replay), not a database |

Thresholds *p\**, *q\**, *r\** and *δ\** are deliberately left unset until the G02 protocol review.

## 13. What E001 will not claim
- That observation preservation implies truth.
- That an LLM materiality judgement is sound.
- That results on synthetic worlds transfer to production without replication.
- Any product form (database, extension, gateway or library) before G05.

## 14. Open questions for the director
1. **Regime mapping is partly prior art.** 2610.03902 maps memory vs re-reading on one clinical dataset. Should E001's contribution be explicitly restricted to *exact query replay vs materiality validation* (H1/H2), dropping broad memory-vs-re-reading claims?
2. **Is the deterministic-simulation-first plan (E001a) acceptable as the G03 scope,** with LLM-in-the-loop deferred to a separate approval?
3. **Authorization rule.** Should E001 use the *union-of-observed-and-justified-scope* rule (§6), or also compare against MAP-Graph-style intersection-of-visible scopes as a baseline?
4. **Second literature pass.** The 2026 corpus is growing weekly. Should a second literature sweep be a G02 entry condition?
5. **Author-cluster concentration.** Several supportive results come from one author cluster (PA §0). Should G02 require reproducing at least one external result (e.g. Tether's released cache, MemShift's scorer) before relying on it?
