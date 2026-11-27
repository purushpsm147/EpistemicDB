# PRIOR_ART — RES-01 prior-art verification

**Task:** RES-01 (issue #3) · **Gate:** G01 (research verification only) · **Author:** Claude (research lead) · **Date of access for all sources:** 2026-11-27 unless stated.

**Status:** Draft for director review. Nothing here is an engineering or stack decision.

---

## 0. Method, and how far to trust this document

**How the sources were obtained.**
- arXiv metadata (title, authors, submission/version dates, Comments field, categories) came from the arXiv export API (`export.arxiv.org/api/query?id_list=…`).
- Full text came from the arXiv HTML rendering (`arxiv.org/html/<id>`). Each paper was converted to plain text and read in the parts cited below.
- Classical database and AI references were checked against Crossref (`api.crossref.org`), DBLP/VLDB anthology pages, publisher or author pages, or official documentation via web search.
- At the start of the session, `arxiv.org` was blocked by the network policy and the first attempts to read full text failed. With the owner's approval, a personal environment allowing arXiv, Crossref, DBLP, VLDB, OpenReview and neurips.cc was created. Everything cited as "read" below was read after that change.

**Levels of reading** (revision 2, unified after director review). Every claim is tagged with one of these:
- **[FT]**: I read the cited sections or tables in the full text of the **pinned version**. **No paper was read cover to cover.** The exact sections read for each paper are listed in one place, the evidence ledger in Appendix A. Wherever this document or a GitHub comment summarises read levels, Appendix A is authoritative.
- **[ABS]**: verified only from the abstract or arXiv metadata of the pinned version.
- **[META]**: bibliographic metadata only, from Crossref, DBLP or the publisher. I did not read the content.

**Version pinning.** Every arXiv citation refers to the version in the evidence ledger (e.g. `arxiv.org/abs/2610.25123v2`). The HTML full text I read was that same version: the version stamp printed in each downloaded HTML file was checked on 2026-11-27. PDF fallbacks are at `arxiv.org/pdf/<id>v<n>`. Revision 1 described some papers as "full text" in the PR summary while Appendix A listed only partial sections; that inconsistency is resolved in favour of Appendix A.

**Peer review status.** Unless marked otherwise, every 2026 agent-memory paper here is an **arXiv preprint with no peer-reviewed venue stated** in its arXiv Comments field. The exceptions are flagged individually.

**Correlated-source warning.** Six of the papers share a core author cluster: Yiqi Wang, Jiaqi Zhang, Taotao Cai, Mingkai Zheng, and in most of them Zhangkai Wu and Yiqun Duan. The six are Tether, MAP-Graph, PACMI, MemTX, Rollback Repair and StateGraph (2610.25123, 2608.10509, 2610.05732, 2607.23929, 2608.10502, 2611.02912). Their agreement should be **read as one research programme, not six independent confirmations**.

**Model identifiers.** Several papers report results on hosted model identifiers I cannot independently date or verify (e.g. "gpt-6-luna", "gpt-6.1-sol", "GPT-5.5", "Claude Sonnet 5"). I report them as the papers state them.

---

## 1. Verification of the eleven named leads

Legend:
- **VERIFIED**: the paper exists and its metadata matches the lead.
- **MISMATCH**: the paper exists, but the lead mis-describes it.
- **UNSUBSTANTIATED**: no matching primary source was found.

| # | Lead (as given) | Verdict | Primary source | Title (exact) | Authors | Dates (arXiv) | Venue / review status | Read level |
|---|---|---|---|---|---|---|---|---|
| 1 | MAP-Graph | VERIFIED | [arXiv:2608.10509v1](https://arxiv.org/abs/2608.10509v1) | MAP-Graph: Provenance-Aware Shared Memory for Multi-Agent Workflows | Yiqi Wang, Zihao Yan, Jiaqi Zhang, Zhangkai Wu, Mingkai Zheng, Zequn Sun, Yanming Zhu, Taotao Cai | v1 2026-08-11 | Preprint; none stated | FT (§3.2–3.4, §4.4 "Scope and limitations") |
| 2 | Tether ("Memories That Outlive Their Reasons") | VERIFIED | [arXiv:2610.25123v2](https://arxiv.org/abs/2610.25123v2) | Memories That Outlive Their Reasons: Dependency-Tethered Memory for Tool-Using LLM Agents | Yiqi Wang, Jinqian Ju, Jiaqi Zhang, Zhangkai Wu, Yiqun Duan, Zequn Sun, Taotao Cai, Mingkai Zheng | v1 2026-10-18, v2 2026-10-22 | Preprint; none stated | FT (Abstract, §2–§6.3, Table 1, §6.6 + Table 3, §7, §8) |
| 3 | MemTX | VERIFIED (note name collision) | [arXiv:2607.23929v2](https://arxiv.org/abs/2607.23929v2) | MemTX: Transactional Belief Commit for Stateful Agent Memory | Xiaoyang Li, Yiqi Wang, Haohui Lu, Zhi Chen, Mo Li, Pingan Song, Mingkai Zheng, Taotao Cai | v1 2026-07-27, v2 2026-07-28 | Comments: "Preprint"; footnote "Under review" | FT (§3.1–3.5, §5 "Scope blind spots", "Cost and isolation") |
| 3b | (distinct) MemTxn | Not the lead; a different paper with a similar name | [arXiv:2607.27834v1](https://arxiv.org/abs/2607.27834v1) | MemTxn: A Transaction Boundary for Source-Supported Updates and Complete-State Recovery in Agent Memory | Hanshuai Cui, Zhiqing Tang, Zhi Yao, Fanshuai Meng, Qianli Ma, Weijia Jia | 2026-07-30 | Preprint | ABS |
| 4 | PACMI | VERIFIED (date discrepancy) | [arXiv:2610.05732v1](https://arxiv.org/abs/2610.05732v1) | PACMI: Provenance-Aware Cascading Memory Invalidation for Long-Term LLM Agents | Yiqi Wang, Jiaqi Liu, Jiaqi Zhang, Zhangkai Wu, Yiqun Duan, Mingkai Zheng, Taotao Cai | v1 2026-10-05 | Preprint. A web search surfaced an SSRN record (abstract 7480138) with a 2026-09-17 date; I did not fetch it, so the discrepancy is unresolved. | FT (Abstract, §6, §7) |
| 5 | MEMOREPAIR / memory repair | VERIFIED | [arXiv:2605.07242v1](https://arxiv.org/abs/2605.07242v1) | MEMOREPAIR: Barrier-First Cascade Repair in Agentic Memory | Yang Zhao, Chengxiao Dai, Mengying Kou, Yue Xiu | 2026-05-08 | Preprint | FT (Abstract, §2.4, Table 4 caption/text, §5) |
| 6 | PLACEMEM | VERIFIED | [arXiv:2607.04089v1](https://arxiv.org/abs/2607.04089v1) | PLACEMEM: Toward a Compute-Aware Memory Plane for Lifelong Agents | Sukanta Ganguly | 2026-07-05 | Preprint; 6-page position paper (Comments: "6 pages, 3 Tables, 1 Figure") | FT (Abstract, §5) |
| 7 | "When Evidence Changes" / **replay-gate** | VERIFIED paper; **MISMATCH on "replay-gate"** | [arXiv:2610.03902v1](https://arxiv.org/abs/2610.03902v1) | When Evidence Changes: Evaluating Memory Repair and Re-reading in Language-Model Agents | Wenhui Chu | 2026-10-02 | Preprint; 43 pp. Internally pre-specified (hash-recorded), but "no external registry timestamp exists" (§5) | FT (Abstract, §2, §4, §5, §6.2, §6.3, §7, §8; Tables 4, 19, 24 captions/rows) |
| 8 | MemShift | VERIFIED | [arXiv:2611.02338v1](https://arxiv.org/abs/2611.02338v1) | MemShift: Separating Detection from Repair in LLM-Derived Personal Memory | Zichuan Xu, Haochen Zhang, Yuyang Dong, Makoto Onizuka, Chuan Xiao | 2026-11-02 | Preprint; code at github.com/zichuanxu/memshift | FT (Abstract, Limitations) |
| 9 | Alleged NeurIPS 2026 exact-invalidation paper | **UNSUBSTANTIATED** | none | — | — | — | — | see §1.1 |
| 10 | ContextPipe | VERIFIED | [arXiv:2609.00749v1](https://arxiv.org/abs/2609.00749v1) | ContextPipe: Database-Inspired Context Assembly for Long-Horizon Agents | Peng Xu, Zuyu Zhang, Yuze Sun, Feng Tian, Long Wang, Chen Zhang | 2026-09-01 | **Workshop paper**: the text states "VLDB 2026 Workshop: ADS 2026" | ABS |
| 11 | Context Has a Price | VERIFIED | [arXiv:2611.22238v1](https://arxiv.org/abs/2611.22238v1) | Context Has a Price: Adding and Removing Context for LLM Coding Agents under Prefix Caching | Zerui Yang, Miaomiao Dong, Quan Xue, Chaorui Zhang, Xuanyu Zheng, Xiyuan Wang, Haolin Ruan, Xueyan Niu, Guangcheng Song, Weixi Zhang, Wei Han, Bo Bai | 2026-11-17 | Preprint | FT (Abstract, Limitations) |

No arXiv entry in this set carries a journal reference or DOI other than arXiv's own.

### 1.1 The "NeurIPS 2026 exact invalidation" lead: unsubstantiated

**What I checked:**
1. The neurips.cc 2026 virtual-site paper listing (`neurips.cc/virtual/2026/papers.html`, retrieved 2026-11-27; 9,183 non-empty title lines). I searched it for invalidat*, stale, provenance, truth maintenance and agent memory. No title concerns exact invalidation of agent-derived knowledge.
2. Near-misses in that listing, **not substitutes** (I have not read any of them):
   - "MemContract: Contract-Sensitive Evaluation for Mutable Agent Memory"
   - "When Stored Evidence Stops Being Usable: Scale-Conditioned Evaluation of Agent Memory"
   - "MEMAUDIT: An Exact Package-Oracle Evaluation Protocol for Budgeted Long-Term LLM Memory Writing"
   - "StateLedger: Path-Addressed External Memory for Persistent Multi-Agent Systems"
3. Of the 2026 preprints I downloaded, only one claims a NeurIPS 2026 affiliation: arXiv:2609.01836 (EAL-Bench, "Accepted at the NeurIPS 2026 PALM Workshop"). That paper is about authorization laundering, not exact invalidation.
4. The paper most plausibly behind the lead is arXiv:2609.00243 (Wu & Canedo, "Invalidation Contracts for Cross-Episode Agent Memory"). Its metadata and text state no venue. Its abstract describes version-stamp validity as "deterministic by construction". It does not claim "exact invalidation" in a general sense.

**Conclusion:** the lead is recorded as unsubstantiated. I have not replaced it with another paper.

### 1.2 The "replay-gate" mismatch

The terms "replay gate" and "replay-gate" do not appear in arXiv:2610.03902.
- The paper defines a **read-set commit gate**: it "validates every evidence version and every node's own parent versions from the snapshot read set inside one transaction with the action record" (App. A).
- The paper scopes it narrowly: "The commit gate checks only that nothing an action read changed between snapshot and commit, not support, permission…" (§1). Also: "it rejects commits whose read set changed and enforces no source-support, provenance, permission or information-flow policy" (§2).
- Its main comparison is **re-reading** current evidence against memory repair. "Replay" in that paper refers to decoding replay (batch B), not a validity gate.

The lead's label should therefore be corrected to "read-set commit gate + re-reading baseline".

---

## 2.0 Key-claim evidence table (added in revision 2)

The director asked for exact locations, short supporting excerpts and explicit denominators for the key economic and safety claims. Each row below gives that for one claim.
- **Excerpts** are verbatim from the pinned version, trimmed with "…" where needed.
- **"Location"** refers to section or table numbers in that version.
- **Fallback.** If HTML fails, every claim can be checked in the PDF at `https://arxiv.org/pdf/<id>v<n>`.

### Economic claims

| # | Claim | Source (pinned) | Location | Verbatim excerpt | Denominator / conditions |
|---|---|---|---|---|---|
| E1 | Full local-repair pipeline costs several times re-reading (development) | [2610.03902v1](https://arxiv.org/abs/2610.03902v1) | §6.2; Table 5 (App. D) | "in the E1 development cells the complete local-repair pipeline costs 3.96–5.03× the tokens of re-reading" | E1 revocation condition, development screening patients, two 7B models (Qwen2.5, Mistral), two record sizes. "all 32 E1 screening trajectories". Tokens = prompt + completion, **no cached-input discount**. Memory supplied in full, no retrieval. |
| E2 | Same ordering on held-out patients | 2610.03902v1 | §6.2; Table 4 (App. C) | "local-repair full-trajectory tokens were 3.5–4.3× re-reading's over the four held-out conditions … re-reading was cheapest in every held-out cell" | 20 sealed patients per cell, small size only, four conditions × two models. Record payloads 59–155 tokens (§5). |
| E3 | Reuse does not reach break-even within 20 uses on short records | 2610.03902v1 | §6.2; Table 19 (App. N) | "No cell's mean cumulative cost fell below re-reading's by k=20 (lowest ratio 1.21, 95 % interval 0.94–1.47)" | 8 development patients, 128 trajectories, 12 model–size–pipeline cells. 1 of 12 cells had a negative per-use term (break-even extrapolated to ~652 uses, not observed). |
| E4 | Source-filtered re-reading cheapest at every length level | 2610.03902v1 | §6.3; Table 20 (P5) | "Source-filtered re-reading was the cheapest pipeline at every level in both models" | 8, 3, 5 and 6 patients at S, M, L and XL. 264 trajectories. Records lengthened only with **task-ineligible** documents. The paper notes "P5 was largely implied by the design". 225 of 520 extraction calls on added documents hit the output cap. |
| E5 | **Cached-input pricing worsens memory relative to re-reading** | 2610.03902v1 | Table 24 (App. O) | Cumulative tokens at k=20 relative to re-reading, Qwen, XL: local repair **0.78** (no cache) → **0.92** (c=0.5) → **1.17** (c=0.25) → **1.67** (c=0.1). Source-filtered re-read 0.05 → 0.13. | Qwen only: the paper marks Mistral's cached ratios invalid (tokenizer error). Ratios are of mean totals. c = price of a repeated-prefix input token relative to uncached. |
| E6 | Trace-derived dependency repair is not a quality-equivalent saving | [2611.19859v1](https://arxiv.org/abs/2611.19859v1) | §5.1; Table 1 | "extracted graph accuracy differs from full context by -11.8 percentage points (paired 95% CI [-15.3, -8.4]), with estimated total cost 3.0% lower" (gpt-6-luna); "-26.7 … cost 10.2% lower" (gpt-6.1-sol) | 120 workflow clusters × 2 repeats × 4 updates per model. Endpoint: complete-plan correctness. Cost at gateway list rates, "Cached-input discounts are ignored" (§4.2). |
| E7 | Adding retrieved code raises cost under cheap cached input | [2611.22238v1](https://arxiv.org/abs/2611.22238v1) | §4 "Cost rises"; Table 4 | "Runs with the package take fewer turns but cost at least 33–42% more at c=0.1 and 25–33% more at c=1 (Table 4)" | Paired E5−E0 differences per benchmark and model, using open models served locally. Cost uses the paper's Eq. 1 with output-price multiplier p (p=4 in Table 1). |
| E8 | The sign of the removal saving flips with cache price | 2611.22238v1 | §5 "The sign flips"; Fig. 3 | "Rolling masking raises cost at c=0.1 by 116% for Qwen3.6-27B and by 87% for Qwen3-Coder. It lowers cost at c=1 by 19% and 25%." Crossover "near c=0.5". | Preregistered tests (Table 3), Holm-corrected. The abstract's broader range "38–116%" covers several methods; only the rolling-masking figures are excerpted here. |

### Safety claims

| # | Claim | Source (pinned) | Location | Verbatim excerpt | Denominator / conditions |
|---|---|---|---|---|---|
| S1 | Edge-less fact memory publishes most upstream-failing objects; Tether almost none | [2610.25123v2](https://arxiv.org/abs/2610.25123v2) | Table 1; §6.2 | Table 1 unsafe at d≥1: Flat **.619**, Tether **.003**. §6.2: "4 of the 1,364 candidates at d≥1 are published while failing, against 844 for Flat" | Fallout-Mixed, 60 synthetic worlds, 720 requests, 2,903 candidates per memory, **reuse** instruction, one hosted model at temperature 0. Unsafe = share of candidates published while failing. |
| S2 | Tether degrades when relations arrive in a separate session | 2610.25123v2 | §6.6; Table 3 | Unsafe at d≥1 under reuse: same **.006**, before **.094**, after **.221**. "the last rests on few worlds (+.215 [.074, .432] over same)" | Fallout-Split: 20 worlds, 959 candidates, 451 at d≥1. Edge recall 1.000/.840/.658. |
| S3 | Small provenance loss gives large leakage | [2605.07242v1](https://arxiv.org/abs/2605.07242v1) | §3.4 text and Table 4; §5 | "dropping 1% of influence edges yields 17.7% Leak (≈18× amplification)" | MemoRepair component-ablation setting (ToolBench / MemoryArena harness). Leak = invalidated memory remaining exposed. |
| S4 | Coverage-derived reachability ≠ falsification | [2609.25130v1](https://arxiv.org/abs/2609.25130v1) | §V-B; Table III | "It catches 131 of the 151 flips, recall 0.868, and reaches 0.415 precision" | Held-out split: 604 claims, 151 execution-verified flips. testmon supplied a verdict for 80% of claims, and the rest are charged as holds. |
| S5 | Undeclared transcription defeats lineage checks | [2607.23929v2](https://arxiv.org/abs/2607.23929v2) | §5 "Scope blind spots" | "Every backbone fails every case for every method, including MemTX." | Source-scope family of the hardened suite (56 cases in total; family size not extracted), five backbones, nine methods. |
| S6 | Memory writers fabricate authority; executors act on it | [2609.01836v2](https://arxiv.org/abs/2609.01836v2) | Abstract (45.4%); §4.2 (99.0%) | "writers create false authority for up to 45.4% of unauthorized requests"; "With the erroneous memories, an unauthorized action occurs in 99.0% of trials" | 45.4% is a maximum across seven writer LLMs under typed incremental updates; I did not locate the per-writer table. 99.0% is conditional on memories that contain false authority, replayed behind the same request. |
| S7 | Inherited input versions are the missing ingredient for detecting stale plans | [2609.03340v2](https://arxiv.org/abs/2609.03340v2) | Abstract; §6.2 | "supplying inherited input versions raises detected handoff conflicts from 0/30 to 30/30" | 30 model-generated five-agent workflows, one model family, one inserted revision. |

## 2. What the lead papers actually claim

For each paper, this section records the mechanism, how dependencies are obtained, what is guaranteed, what is measured, and the stated limitations.

### 2.1 Tether (arXiv:2610.25123): unsafe-publication numbers in context [FT]

**Mechanism (§3).**
- The store holds atomic records. Check records (verdicts) gain one field, `derived_from`: the exact versions the verdict rests on.
- **Edges are written by an LLM writer** that sees the session transcript plus the k retrieved records, "as stated by facts in the session or in the retrieved records". They are **not** captured from execution traces.
- At read time, a reachability pass over the whole store withdraws, and serves as `null`, any passing verdict that has a failed, null or *higher-version* ancestor.
- Withdrawal "lowers, never raises" and is recomputed on every read.

**Guarantee.**
- Observation 1 (§2) is a locality argument: a window-local memory cannot distinguish the two worlds.
- Beyond that, the guarantee is **empirical only**. There is no soundness theorem for Tether itself.

**Benchmark.**
- "Fallout" is a synthetic generator of versioned worlds whose truth is computed exactly by Eq. (1), `pass_t(v) = own_t(v) ∧ ⋀ pass_t(parents)`.
- Main set (Fallout-Mixed): 60 worlds, 720 live requests, 2,903 candidates (§4).
- All roles in the main run use one hosted model, at temperature 0.
- Intervals are 95% cluster bootstrap over worlds (§5).
- Code and cache are released.

**Cost.** Cost is measured as **verification-tool calls ("checks") only**. Tokens and dollars are not part of the safety/cost trade-off (§2 "Checks cost one unit each"). Session time is "modelled from calls, not measured" (§8).

**The headline numbers, in context:**

| Number | What it is | Where |
|---|---|---|
| 62% | Flat (atomic-fact memory, no edges) publishes 62% of the objects whose failure lies *upstream* (d≥1), **under the "reuse" instruction**. By stratum: 55% at d=1, 61% at d=2, 71% at d≥3. At d=0 (the object itself failed) Flat is nearly safe, at 1.2%. | Abstract; §6.1 |
| 0.3% / 0.5% | Tether's unsafe rate on upstream-failing objects (0.3%, abstract) and overall (0.5%, §6.2). 4 of 1,364 d≥1 candidates are published while failing, against 844 for Flat. 11 of the 15 residual errors are at d=0, from notices "mentioned in passing". | Abstract; §6.2 |
| 37% fewer checks | 2.54 checks per request for Tether against 4.05 for always-verify. | §6.2 |
| 9% / 42% | A-Mem and Graphiti publish 9% of upstream-failing objects while re-checking 0.83–0.86 per candidate. Mem0 publishes 42%. | Abstract; §6.3 |
| Global flush | Flushing every verdict on any failure is as safe as Tether but costs 32% more checks. On Fallout-Split it published 0 of 451, at 0.85 checks against Tether's 0.60. | Abstract; §6.6 |
| **9.4–22.1%** | **Fallout-Split**: 20 worlds, 959 candidates, 451 at d≥1. Here relations arrive in a session *separate* from their verdict. Tether's d≥1 unsafe rate rises from 0.6% (same session) to 9.4% (relations one session before) and 22.1% (two sessions after), under "reuse". Edge recall falls 1.000 → .840 → .658 at precision 1.000. With a complete chain, 1 of 370 is published while failing; **with a missing edge, 40 of 81**. The 22.1% "rests on few worlds" (+.215 [.074, .432]). Under "forced" (told to trust memory) Tether reaches .139/.269. In the split setting **A-Mem and Graphiti are safer than Tether** (2–3%), because they re-check nearly everything. | Table 3; §6.6 |
| 9.6% over-withdrawal | 9.6% of passing records whose versions still pass are withdrawn by the version trigger. The cost recurs on every read. | §6.2 |

**Our reading:**
- Tether's safety is **conditional on dependency capture**. The paper says so itself: "With incomplete edges, an agent that cannot afford one failing publication should still verify everything" (§6.6).
- The "62%" figure is the failure of a deliberately edge-less ablation under an instruction to reuse. It is **not** a measured rate for deployed memory systems.
- The limitations (§8) state that dependencies are "stated explicitly in pasted records; dependencies that a writer would have to infer are not tested". The real-Go-advisory replay uses one world shape and one backbone.

### 2.2 MAP-Graph (arXiv:2608.10509) [FT]

**Mechanism.**
- A typed execution graph of agents, sources, memories, claims and actions.
- **Provenance is captured from observable workflow state** (§3.2): when an agent observes a source or tool output, the backend creates a record whose `derived_from` includes that identifier. Message-derived memories inherit the IDs of retrieved memories and visible sources.
- **Permission scope of a derived record = intersection of referenced scopes.** If no referenced scope exists, every task agent is eligible. If the intersection is empty, access falls back to the writer (§3.2).
- Revocation is an explicit `revoke_permission` event (§3.2).
- Retrieval removes permission-ineligible records before ranking, then ranks by semantic similarity × multiplicative "path trust" (Eq. 4, §3.3).
- A deterministic, action-risk-indexed gate (thresholds 0.30/0.85/0.60) is applied before acting (§3.4).

**Guarantee.** None formal. The gate is "an implemented policy layer rather than a proof that an allowed action is safe" (§3.4).

**Weaknesses.**
- Query-time permission checks "do not independently traverse every parent record" (§3.3).
- There is no supersession or conflict-resolution state machine (§3.2).
- The benchmark is "synthetic and templated, actions are simulated… One temperature-zero run" (§4.4).

**Relevance.** This is the closest prior art for *authorization preservation across derived knowledge* via scope intersection. Note that it records what was *visible* to the writer, not what was *used*.

### 2.3 MemTX (arXiv:2607.23929) [FT]

**Mechanism (§3).**
- Each record carries an evidence/source authority weight, a permission block (owner, reader/writer roles, share scope), `derived_from` edges, a type, a validity interval on a logical clock, and the writer's confidence.
- There is an eight-state lifecycle and five isolation levels.
- Commit runs four ordered checks: evidence threshold, validity interval, rule-based semantic conflict, dependency stability. It also blocks republication of a private-scope parent into a wider scope ("permission laundering").
- The action gate blocks irreversible calls while tentative records are in flight.
- Typed cascading repair runs over the derivation DAG.

**Guarantee.** Two invariants (action-safety gating; cascade-repair completeness) and corollary G3. They are checked by property-based testing (10k traces) and **bounded** exhaustive enumeration (5,530,160 states over a reduced alphabet). The authors describe this as "deliberately weaker than the unbounded soundness theorems of prior formal work" (§3.5).

**Explicit limits.**
- "Repair covers only recorded provenance, so a derivation never written down is never repaired" (§3.4).
- **Negative result (§5 "Scope blind spots"):** in the source-scope family (an agent transcribes private content into a shared summary, then acts), "Every backbone fails every case for every method, including MemTX… the agent transcribes without declaring the parent, so a declarative lineage check has nothing to inspect."
- The temporal-scope retry escapes staleness detection.

**Cost.** Tokens are up to +14% over baselines on three backbones. About 1.5 conflict adjudications per case (§5 "Cost and isolation").

### 2.4 PACMI (arXiv:2610.05732) [FT]

**Mechanism.** A provenance graph with typed dependency edges, a four-state validity lattice, propagation, and stale-premise checking.

**Critical assumption.** "The dependency edges are supplied by annotation (A1)… inducing such edges from raw memory text is an unsolved problem in its own right" (§6).

**Benchmark.** 100 cases and 300 queries with a fixed two-hop topology. Over-invalidation "cannot be exercised". An LLM judge from the same family as the generator is used, with a single run at temperature 0 (§6).

**Result.** Removing cascading raises final-answer errors from 3 to 11, which is **not significant** at 0.05 (Abstract). Cost is counted in LLM calls, not tokens or latency (§6).

### 2.5 MemoRepair (arXiv:2605.07242) [FT]

**Mechanism.** A withdrawal barrier: affected descendants are withdrawn before repair, successors are built from retained support, and only validated, predecessor-closed successors are republished. For a fixed λ, publication selection reduces to maximum-weight predecessor closure, solved exactly by one s–t min-cut (Theorem 1, §2.4). This is an exact optimisation result, *not* a validity guarantee.

**Results (with complete influence provenance).**
- Exposure to invalidated memory falls from 69.8–94.3% to 0%.
- It recovers 91.1–94.3% of validated successors at normalized repair-operator cost 0.57–0.76 relative to Repair-all (Abstract).

**Key limitation (§5).** "MemoRepair relies on complete influence provenance… **dropping 1% of influence edges yields 17.7% Leak**." Also: "A successor that passes the implemented checks is not guaranteed to be semantically correct in all future contexts."

### 2.6 PLACEMEM (arXiv:2607.04089) [FT]

- A 6-page **systems position paper** with a vLLM-oriented control-plane prototype. Memory is "versioned capsules" unifying semantics, provenance, validity and reusable runtime (KV) state.
- The pilot (§5) uses **one 48-turn trace and a streaming mock in place of the real token generator**. It reports time-to-first-token (TTFT), reuse and stale hits. For example, runtime reuse without semantic invalidation gives −63.2% TTFT but 17 stale hits.
- **Relevance:** it names the KV/prefix-reuse vs. invalidation tension. It provides no evidence on soundness or economics at scale.

### 2.7 When Evidence Changes (arXiv:2610.03902): the strongest evidence against reuse economics [FT]

**Setup.**
- Medication and problem-list tasks drawn from eICU-CRD demo v2.0.1 (a public demo; §5).
- Two 7B open models, plus one 32B supplement.
- Pipelines (§4): re-read, cache, rebuild, local repair, quarantine, no-propagation.
- "Costs include ingest, revision and every use."
- Memory is supplied in full, without retrieval.

**Findings.**
- Local repair's revise phase is **5–10× cheaper than rebuild**. But the *full* local-repair pipeline costs **3.96–5.03× the tokens of re-reading** in development and 3.5–4.3× in the held-out run. "Re-reading was cheapest in every held-out cell" (§6.2).
- Reuse does not rescue memory: in a 20-use sweep, the per-use term stayed positive in 11 of 12 cells. "No cell's mean cumulative cost fell below re-reading's by k=20." The paper concludes "graph-local repair saves recomputation, not tokens" (§6.2).
- With ~10k-token records padded by *task-ineligible* documents, memory undercut full re-reading after 2–14 uses. Part of that saving came from truncated extraction (225 of 520 extraction calls hit the output cap). **Source-filtered re-reading remained cheapest at every level** (§6.3).
- Conclusion (§7): "The baseline to beat is therefore the strongest re-reading pipeline (source filtering, cached input), not re-reading everything at full price."
- **Cached-input pricing (Table 24, Qwen only):** a prefix discount makes memory *worse* relative to re-reading. At XL, local repair's 20-use cumulative ratio rises from 0.78 with no cache to 1.67 at c=0.1 (key claim E5).

**Limitations (§8).**
- One dataset, small n (3–20 patients per cell), lexical scorers written after seeing outputs.
- "costs are tokens, not prices or latency".
- "Greedy decoding was not bit-reproducible on the serving stack."

### 2.8 MemShift (arXiv:2611.02338v1) [FT, partial: Abstract and Limitations only]

**Design.** A benchmark that separates *detection* (noticing that an input changed) from *repair* (producing the correct new value). It covers five shifts: revision, deletion, redefinition, writer switch and export. Gold values and dependencies are fixed before any text is generated, and a deterministic scorer is used.

**Finding.** "detection helps only as much as repair allows". **Programs that the LLM writes over typed records close most of the gap to exact recomputation** (Abstract).

**Limitations.** Synthetic templated worlds; one-sentence definitions over structured fields.

**Relevance.**
- It supports separating invalidation from re-derivation.
- It supports the idea that *executable* re-derivation beats free-text LLM re-derivation.

### 2.9 ContextPipe (arXiv:2609.00749v1) [ABS]

- A **VLDB 2026 ADS workshop** paper. It frames context assembly as query execution (Plan → Bind → Optimize → Execute → Feedback), with a catalog and a cache-aware optimizer.
- Preliminary evaluation on the SWE-bench Pro Qutebrowser subset: −31% tokens, −23% LLM calls and −9% response time against append-only context, "at a cost in lower KV cache-hit ratio" (Abstract, v1).
- A secondary site reported 30.1% and 17.2%. Those figures do **not** match arXiv v1, which is the only version.
- **Relevance:** a database framing of *context assembly*, not of knowledge validity.

### 2.10 Context Has a Price (arXiv:2611.22238v1) [FT, partial: see Appendix A]

**Setup.** More than 20,000 runs on SWE-bench Verified, SWE-bench and Loc-Bench, with uncached-input, cached-input and output tokens priced separately.

**Findings (Abstract).**
- Retrieved code cuts read and search commands by 18–34% but **increases cost by 33–42% at a tenth-price cache**, with no measurable gain in resolution.
- Removing old observations saves 19–36% with no cache discount, but **increases cost by 38–116% with a tenth-price cache**.
- "Shorter prompts do not guarantee cheaper runs because agents may respond by taking more steps."
- Concurrent experiments can share cached prefixes and distort cost comparisons.

**Limitations.**
- Open, locally served models, mostly from the Qwen family.
- Prices are applied to token streams rather than invoices.
- Provider-specific expiry and write charges are not modelled.

**Relevance.** Prompt/prefix caching changes the sign of context-management savings. Any E001 cost model **must** price cached input separately and measure behavioural side effects.

---

## 3. Closest competing work found beyond the leads

All entries are 2026 arXiv preprints unless marked otherwise.

| Work | ID | What it does | Why it matters to us | Read |
|---|---|---|---|---|
| **PlanFence**, "Fresh Memory, Stale Plans: Derivation Currency for Distributed LLM-Agent Memory" (Chen, Wang, Brinton). v1 was titled "…Dependency-scoped validation…" | 2609.03340 (v2 2026-09-27) | Distinguishes *observation currency* (local copy = owner head) from *derivation currency* (the plan's recorded inputs = owner head) (Def. 1). Builds an action-specific dependency frontier from exact parent IDs and validates against the owners before a protected action. Allows one replan, then blocks. | **The closest prior art to "commit-time validation of derived knowledge".** It has a conditional soundness proposition (Prop. 1) under a "recorded-input contract" (Assumption 1): links supplied by the application, complete declared scope, benign owners. It is **version-equality, not materiality**: any version change forces a replan. It provides "neither a common multi-owner snapshot nor currentness at the external effect" (§3). | FT (§3, §4, §7, App. E.3) |
| **Impact Is Not Invalidation** (Anand) | 2609.25130 | On 10,369 executable claims (tests) with 184 execution-verified flips from 23 Python libraries: a coverage-derived dependency selector (pytest-testmon) reaches 0.868 recall at only **0.415 precision**. A "does this diff preserve behaviour?" LLM question reaches 0.29–0.33 precision. A claim-specific question ("does *this* claim still hold?") reaches 0.705–0.974. | **Direct evidence on materiality.** Reachability is not falsification, and the *question asked* matters more than the evidence supplied. These are LLM judgements (empirical, not proofs). Ground truth is execution, which is a good template for our oracle. | ABS + FT (§VI Threats) |
| **When Facts Change, Which Decisions Break?** (Retinraj, Scale AI) | 2611.19859 | Field-level dependency graphs **extracted from actual agent read traces** (`read_records` calls), compared against full recompute, summary, lexical retrieval and an oracle graph. 120 workflows, 4 updates, 2 models. | **The closest test of "execution-derived observation" provenance, and it is largely negative.** Extracted graph vs full context: −11.8 pp (gpt-6-luna, cost −3.0%) and −26.7 pp (gpt-6.1-sol, cost −10.2%), "not a quality-equivalent saving" (§5.1, Table 1). Failure mechanism: "sufficient evidence for an initial decision" ≠ "sufficient support coverage for future repair"; "unvisited alternatives" are omitted (§7). Removing 30% of edges gives 0.0% accuracy (Table 2). Single author; no public code; synthetic workflows. | FT (§1–§5.2, §7) |
| **Invalidation Contracts** (Wu & Canedo) | 2609.00243 | Server-side version stamps plus cacheability hints on cached API-recovery suggestions. Row-level invalidation recovers 29–33% of baseline token cost on 4 of 7 models. Table-level invalidation destroys co-located entries. | Shows that granularity drives the cost of version-stamp validation. Validity is "deterministic by construction". Requires server cooperation; synthetic APIs (§6.4). | ABS + FT (§6.4) |
| **MERIT**, "When Does Memory Help?" (Mishra & Mishra) | 2609.05441 | Tool-using agent memory with token and dollar metering (23,440 episodes, $42.57). "full replay is never economical". | **Caution:** "full replay" here is *full-transcript replay*, **not** exact query replay against a source. Do not cite it against exact replay. | ABS |
| **Memory Tax** (He et al., UIUC) | 2610.34073 | Across benchmarks, "many dedicated memory systems underperform naive RAG", i.e. negative net value. | Evidence against assuming that derived memory beats raw retrieval. | ABS |
| Pollertlam & Kornsuwannawit (2026a; 2026b) | 2603.04814; 2608.11879 | Cost of fact memory vs long context under prompt caching (break-even after ~10 turns at 100k tokens, per 2610.03902 §2). Serving-cost benchmark of Mem0, Hindsight and Mastra: break-even ranges from tens of turns to never within 400 turns. | Break-even is system- and backbone-sensitive. | ABS |
| **EAL-Bench**, "Agent Memory Is a Surface for Endogenous Authorization Laundering" (Cerruti, Okamoto, Erol) | 2609.01836 (v2) | Memory writers create false authority for up to 45.4% of unauthorized requests. Executors act on false authority in 99.0% of trials. | **Accepted at NeurIPS 2026 PALM Workshop** (Comments). Authorization state in memory is fragile even without an attacker. Synthetic cases. | ABS + FT (Limitations) |
| **Cordon** (Chen et al.) | 2606.17573 | A semantic transaction runtime: tracks result lineage, stages external effects in an outbox, validates before commit. | Commit-time validation of agent effects. | ABS |
| Khan, Verified concurrency anomalies in multi-agent LLM systems | 2606.17182 | Formalizes stale-generation, phantom-tool, causal-cascade and tool-effect-reordering anomalies in TLA+. Verus proofs. SI adds ~8% tokens; pessimistic locking 1.6–2.3×. | Classical isolation analogues for agents, including a "phantom" anomaly. Prevention cost is bounded, not zero. | ABS |
| He & Yu, "Stored Is Not Supported" | 2609.02127 | Typed provenance with a conditional assertion-boundedness contract. 24 hand-authored cases, no LLM in the loop. | Formal, but tiny and not end-to-end. | ABS |
| MemLineage (Ouyang & Hou) | 2605.14421 | Signed Merkle log plus weighted derivation DAG. Gates sensitive actions whose justification descends from untrusted sources. | Lineage-based authorization against poisoning. | ABS |
| StateGraph (Wang et al.) | 2611.02912 | Dependency edges recovered from interaction histories by **counterfactual verification** (does the dependent survive loss of its premise?). 50 author-constructed cases. | An LLM-inferred materiality-like test, at small scale. Same author cluster. | ABS |
| Rollback Repair (Wang et al.) | 2608.10502 (v2) | Typed memory-to-action graph from runtime provenance with selective replay. 85.3% recovery vs 77.3%, but **recurrence 26.6% vs 12.1%** for broader LLM-judge repair. | Targeted repair trades off against broader recomputation. Same author cluster. | ABS |
| DerivAudit, "Memory Is a Derivation" (Liu & Zhao) | 2609.36130 | 17–21% of memories remain unsupported after evidence expansion. Unsupported memories are admitted 59–89% of the time. | Write-time derivation validity is itself unreliable. | ABS |
| "When Stale Constraints Go Unchecked" (Nakayashiki) | 2608.25553 (v3) | Even with provenance links, 16 LLMs rarely re-verified settled-looking constraints; stale-consistent decisions in ~75% of episodes. Data on Zenodo. | Provenance availability ≠ provenance use. | ABS |
| Agentic Plan Caching (Zhang, Wornow, Wan, Olukotun) | 2506.14852 (v2) | Reuses plan templates across similar tasks: −50.31% cost, −27.28% latency. **NeurIPS 2025** (Comments). | Semantic caching of agent work, without validity guarantees. | ABS |
| "Similarity Is Not Validity" (Zhang et al.) | 2609.35908 | Semantic LLM caches are poisonable. Embedding similarity loses the information needed for validity. | Supports the claim that semantic similarity is the wrong key for reuse. | ABS |
| MEME (Jung et al.) | 2605.12477 (v2) | A benchmark for multi-entity, evolving memory (dependent fact propagation). | An evaluation precedent. | META |

**Products and production systems** (these are memory baselines, not validity systems):
- **Mem0** (arXiv:2504.19413; ECAI 2025 per 2610.03902's reference list)
- **Zep/Graphiti** (arXiv:2501.13956; bi-temporal edges with temporal invalidation)
- **A-MEM** (arXiv:2502.12110; NeurIPS 2025)
- **MemGPT/Letta** (arXiv:2310.08560)

Tether (§5, §6.3) evaluates A-Mem, Graphiti and Mem0 as baselines. Their safety came mostly from re-checking nearly everything.

---

## 4. Established foundations (verified bibliographic metadata)

| Area | Reference | Verified metadata | Relevance to EpistemicDB |
|---|---|---|---|
| Truth maintenance | J. Doyle, "A Truth Maintenance System" | *Artificial Intelligence* 12(3):231–272, 1979. doi:10.1016/0004-3702(79)90008-0 (Crossref). MIT AIM-521. | Justification-based retraction of dependents when premises are withdrawn. Tether, PACMI and MemTX are explicitly descendants (Tether §7). [META] |
| Truth maintenance | J. de Kleer, "An Assumption-based TMS" | *Artificial Intelligence* 28(2):127–162, 1986. doi:10.1016/0004-3702(86)90080-9 (Crossref) | Labels beliefs with minimal assumption sets: multi-context validity. [META] |
| Provenance | Buneman, Khanna, Tan, "Why and Where: A Characterization of Data Provenance" | ICDT 2001, LNCS 1973, pp. 316–330 | Why- vs where-provenance. [META] |
| Provenance | Green, Karvounarakis, Tannen, "Provenance Semirings" | PODS 2007, pp. 31–40. doi:10.1145/1265530.1265535 | N[X] polynomials as the universal provenance for positive relational algebra and Datalog. **Positive** queries only; negation and aggregation need extensions. [META] |
| View maintenance | Gupta & Mumick, "Maintenance of Materialized Views: Problems, Techniques, and Applications" | IEEE Data Eng. Bull. 18(2):3–18, 1995 | Survey. [META] |
| View maintenance | Gupta, Mumick, Subrahmanian, "Maintaining Views Incrementally" (DRed) | SIGMOD 1993, pp. 157–166 | Delete-and-rederive for recursive views. [META] |
| **Irrelevant updates (materiality)** | Blakeley, Coburn, Larson, "Updating Derived Relations: Detecting Irrelevant and Autonomously Computable Updates" | VLDB 1986, pp. 457–466; ACM TODS 14(3):369–400, 1989 | **Necessary and sufficient conditions for an update to be irrelevant to a project-select-join view.** This is the classical formal core of "materiality". [META] |
| **Query–update independence** | C. Elkan, "Independence of Logic Database Queries and Updates" | PODS 1990, pp. 154–160. doi:10.1145/298514.298557 | Deciding whether an update can affect a query. [META] |
| **Query–update independence** | Levy & Sagiv, "Queries Independent of Updates" | VLDB 1993, pp. 171–181 | Independence for Datalog via equivalence tests. Decidable only for restricted classes. [META] |
| IVM | Budiu, Chajed, McSherry, Ryzhyk, Tannen, "DBSP: Automatic Incremental View Maintenance for Rich Query Languages" | PVLDB 16(7):1601–1614, 2023. doi:10.14778/3587136.3587137; arXiv:2203.16684 | Automatic incremental view maintenance (IVM) for recursion and aggregation via Z-set streams. [META] |
| IVM | Koch et al., "DBToaster: Higher-order Delta Processing…" | VLDB J. 23(2):253–278, 2014. doi:10.1007/s00778-013-0348-4 | Recursive delta compilation. [META] |
| Dataflow caching | Gjengset et al., "Noria: dynamic, partially-stateful data-flow for high-performance web applications" | OSDI 2018 (USENIX) | Partially materialized, incrementally maintained query caches with upqueries: a **database-native read cache with exact invalidation**. [META] |
| Incremental computation | Acar, Blelloch, Harper, "Adaptive Functional Programming" | POPL 2002, pp. 247–259. doi:10.1145/503272.503296 | Self-adjusting computation: dynamic dependency graphs and change propagation. [META] |
| Incremental computation | Hammer, Phang, Hicks, Foster, "Adapton" | PLDI 2014, pp. 156–166. doi:10.1145/2594291.2594324 | Demand-driven (lazy) incremental computation: validate-on-read. [META] |
| Build systems | Mokhov, Mitchell, Peyton Jones, "Build Systems à la Carte" | PACMPL 2(ICFP), Art. 79, 2018. doi:10.1145/3236774; JFP 30, 2020. doi:10.1017/S0956796820000088 | Verifying traces, constructive traces, **early cutoff** (stop propagating when a rebuilt value is unchanged) and dynamic dependencies. [META] |
| Concurrency control | Eswaran, Gray, Lorie, Traiger, "The Notions of Consistency and Predicate Locks in a Database System" | CACM 19(11):624–633, 1976 | **Phantoms**: reading a *predicate* must be protected, not only the records read. [META] |
| Concurrency control | Kung & Robinson, "On Optimistic Methods for Concurrency Control" | ACM TODS 6(2):213–226, 1981. doi:10.1145/319566.319567 | Read set + validation phase: **commit-time read-set validation**. [META] |
| Concurrency control | Cahill, Röhm, Fekete, "Serializable Isolation for Snapshot Databases" | SIGMOD 2008, pp. 729–738. doi:10.1145/1376616.1376690; TODS 34(4), 2009 | Serializable snapshot isolation (SSI): rw-antidependency detection. [META] |
| Continuous queries | Terry, Goldberg, Nichols, Oki, "Continuous Queries over Append-Only Databases" | SIGMOD 1992, pp. 321–330. doi:10.1145/130283.130333 | Standing queries over growing data. [META] |
| Semantic caching | Dar, Franklin, Jónsson, Srivastava, Tan, "Semantic Data Caching and Replacement" | VLDB 1996, pp. 330–341 | Caching by query predicate region rather than by page or tuple. [META] |
| Product: query-result change notification | Oracle Database, Continuous Query Notification (Development Guide, CQN chapter; DBMS_CQ_NOTIFICATION) | docs.oracle.com (18c–26ai) | **Object Change Notification** (any change to a referenced table) vs **Query Result Change Notification** (only when the result changes). QRCN falls back to object-level for unsupported queries, e.g. subqueries. Best-effort mode admits false positives. Notifications fire on commit. **A shipping DBMS feature that separates dependency from materiality for SQL.** [ABS, docs] |
| Product: percolator | Elasticsearch percolate query | elastic.co reference docs (current) | Stored queries matched against new documents: **inverse search** for detecting new matching records (phantoms) for stored predicates. [ABS, docs] |
| Product: negative evidence | MySQL query cache | Deprecated 5.7.20; removed in 8.0 (MySQL blog "Retiring support for the query cache"; WL#10824) | **Table-level invalidation plus global locking did not scale**: any write to a table invalidated every cached query using it. A cautionary precedent for coarse result caches. [ABS] |
| Web caching | IETF RFC 9111 "HTTP Caching" (2022), doi:10.17487/RFC9111; RFC 9110 (validators, ETag, conditional requests) | IETF | TTL freshness plus validator-based revalidation (`304 Not Modified`): the canonical "cheap validate, else refetch" protocol. [META] |
| LLM prompt caching | Anthropic prompt caching docs | platform.claude.com docs | Cache read 0.1× base input. Cache write 1.25× (5-minute TTL) or 2× (1-hour TTL). Reads refresh the TTL. Rates change over time, so any cost model must parameterize them. [ABS, docs] |

---

## 5. Comparative analysis along the dimensions requested in RES-01

### 5.1 Observation vs dependency vs justified scope

These are three different things, and the literature conflates them:

| Concept | Definition used here | Who implements it |
|---|---|---|
| **Observation** | What was actually read: record IDs, query text, result, version. | MAP-Graph (sources and tool outputs observed); 2611.19859 (read traces); PlanFence (record IDs placed in context, "log, not infer"); OCC/SSI read sets |
| **Dependency** | What a derived item's truth rests on. This can be stated, inferred or counterfactually tested. | Tether (LLM writer from transcript); PACMI (annotated); MemoRepair (assumed complete); StateGraph (counterfactual LLM test) |
| **Justified scope** | What the *conclusion* claims, including universally quantified or absence parts. Examples: "cheapest", "none overdue", "all approvals present". | **No agent-memory paper models this explicitly.** Classical analogues: predicate locks (phantoms), view irrelevance conditions (Blakeley et al.), Oracle QRCN. |

**Key evidence (2611.19859 §7).** Read traces capture what was *visited*. They do not capture the *unvisited alternatives* that become relevant after a change. Observation ⊂ justified scope in general, so observation-derived provenance is incomplete *by construction* for non-monotone conclusions.

### 5.2 New records and phantoms
- **Agent-memory work largely does not handle new records that falsify a conclusion.**
  - Tether withdraws on *higher-version* appearance, which is a conservative trigger, but not on new sibling objects.
  - PlanFence covers only declared keys. A new key outside D(a) is unchecked (Assumption 1).
  - MAP-Graph has no supersession.
- Khan (2606.17182) formalizes a "phantom-tool" anomaly for agent runtimes [ABS].
- Classical solutions exist when the source is a DBMS: predicate locking (Eswaran et al.), SSI (Cahill et al.), result-change notification (Oracle QRCN), and percolation (Elasticsearch). **They require the source to expose predicates, snapshots or notifications.** External APIs and web sources usually expose none of these.

### 5.3 Completeness and pagination
- No paper examined validates the **completeness of paginated or limited reads** (top-k, page 1 of n, truncated tool output).
- 2610.03902 §6.3 documents a related failure: 225 of 520 extractions hit the output cap, so "part of memory's per-use saving there is content that never entered memory".
- **This is an open problem.** A receipt for a truncated observation must record that it was truncated. Otherwise reuse silently inherits incompleteness.

### 5.4 External snapshot limitations
- PlanFence: owner reads provide "neither a common multi-owner snapshot nor currentness at the external effect" (§3, App. E.3).
- MemTX: compensation is "a logged decision-level obligation rather than environment-level replay" (§3.4).
- **No agent-memory system obtains atomic snapshots across external sources.** Validation can only shrink the window between check and use. It cannot close that window (time-of-check to time-of-use, TOCTOU).

### 5.5 Authorization
- **Write-time scope intersection** (MAP-Graph §3.2) and **permission inheritance plus laundering block** (MemTX §3.2) are the existing mechanisms.
- Both fail when provenance is not declared or recorded. MemTX's source-scope family fails for every method (§5). EAL-Bench shows writers fabricate authority (≤45.4%) and executors act on it (99.0%).
- **Open:** reuse by a *different principal* than the deriver; revocation after derivation (MAP-Graph recomputes only direct references eagerly, §3.2); inference leakage (a derived conclusion can reveal properties of inputs the reader may not see). Classical foundations here are view-based access control and information-flow control. CaMeL and FIDES are cited by 2610.03902 §2, but I did not verify them for this document.

### 5.6 Commit-time vs reuse-time validation
- **Commit-time (write) validation:** MemTX commit pipeline, MemTxn PatchTest, Cordon pre-commit validation, the 2610.03902 read-set commit gate, DerivAudit.
- **Reuse/action-time validation:** PlanFence (before a protected action), Tether (withdrawal recomputed at every read), MAP-Graph (gate before action), MemLineage (gate on sensitive actions).
- **Our framing note:** commit-time validation checks that a derivation was valid *when made*. Reuse-time validation checks that it is *still* valid. These are different properties. A conclusion can pass commit validation and later go stale. Only reuse-time validation addresses staleness, and it costs on every reuse.

### 5.7 Cost accounting quality

| Paper | Cost unit | Includes ingest/derivation? | Cached-input pricing? |
|---|---|---|---|
| Tether | verification calls | no | no |
| PACMI | LLM calls | partial | no |
| MemoRepair | normalized repair-operator cost | repair only | no |
| MemTX | tokens, latency | yes | no |
| 2610.03902 | tokens (full pipeline) | **yes** | Qwen only (§6.3) |
| 2611.19859 | $ at gateway rates | **yes** | ignored |
| Context Has a Price | $ via token streams | yes | **yes** |
| MERIT | tokens + $ | yes | not verified |
| PlanFence | ms stall, KiB traffic | n/a | n/a |

**Implication.** Most invalidation papers (Tether, PACMI, MemoRepair) do **not** price the full pipeline. The two papers that do (2610.03902; 2611.19859) find re-reading or full recomputation competitive or dominant in their regimes.

### 5.8 Benchmark evidence quality
- Nearly all of it uses synthetic, author-constructed worlds; the exceptions are 2610.03902 (public eICU demo) and 2609.25130 (real Python repositories with execution oracles).
- Most papers use a single run or temperature 0. Several use LLM judges from the same family as the generator (PACMI).
- Pre-registration is internal only (2610.03902, 2611.22238 "preregistered test"), or absent.
- Execution-based oracles exist in Fallout (Eq. 1), MemShift (deterministic scorer), 2611.19859 (executable references) and 2609.25130 (tests). **These are the models to follow.**

---

## 6. What is already solved, what is open, and what undermines our thesis

### 6.1 Already solved (do not claim novelty)
1. **Dependency-directed retraction.** TMS (1979/1986) → view maintenance → build systems → agent memory (Tether, PACMI, MemoRepair, MemTX).
2. **Version-equality validation of derived artefacts before use.** OCC (1981), HTTP validators, build-system verifying traces, Invalidation Contracts, and PlanFence (with a conditional soundness proposition).
3. **Read-set validation at commit** (OCC; the 2610.03902 commit gate; MemTX snapshot transactions).
4. **Irrelevant-update detection for restricted query classes.** PSJ views (Blakeley et al.), Datalog fragments (Elkan; Levy & Sagiv), and **in production** via Oracle QRCN with documented fallbacks.
5. **Early cutoff.** Stop propagating when a recomputed intermediate is unchanged (build systems à la carte).
6. **Permission propagation by scope intersection** (MAP-Graph) and laundering blocks (MemTX), conditional on declared provenance.
7. **Exact, incremental result caching inside one DBMS** (Noria, DBSP, Materialize-style IVM).

### 6.2 Evidence that undermines the EpistemicDB thesis
1. **Re-reading is often cheaper.**
   - Source-filtered re-reading was the cheapest pipeline at every level tested (2610.03902 §6.3).
   - Full-context recomputation dominated trace-derived graph repair at quality parity (2611.19859 Table 1).
   - Dedicated memory often underperforms naive RAG (2610.34073).
2. **Prompt caching erodes savings.** At a 0.1× cached-input price, adding or removing context can *raise* cost (2611.22238; E7, E8). In 2610.03902 Table 24, the cached-input discount turns local repair's advantage at XL (0.78×) into a loss (1.67×) (E5). Cheap re-reading of a stable prefix is the strongest economic competitor to any reuse scheme. **Scope caveat:** both studies use open models, token-stream pricing rather than invoices, and specific workloads (coding agents; ICU medication lists).
3. **Dependency coverage is fragile, and failures are silent.**
   - 1% of edges dropped gives 17.7% leak (MemoRepair §5).
   - 30% of edges dropped gives 0% plan accuracy (2611.19859 Table 2).
   - Split-session relations give 9.4–22.1% unsafe (Tether §6.6).
   - Undeclared transcription defeats lineage entirely (MemTX §5).
   - Execution-derived observation is *better* than LLM-stated dependency, because it logs rather than infers (PlanFence Assumption 1). But it still misses justified scope (§5.1).
4. **Reachability ≠ materiality.** Coverage-derived dependencies reach 0.415 precision at 0.868 recall for "will this claim flip?" (2609.25130). So dependency-based invalidation over-fires heavily. Claim-specific *LLM* judgements did much better (0.705–0.974 precision), but they are empirical, not sound.
5. **Over-withdrawal costs recur.** Tether's version trigger withdraws 9.6% of still-valid records and re-charges on every read (§6.2).
6. **Provenance is not used even when present.** Agents rarely inspect provenance under budget (2608.25553).

### 6.3 Plausibly open (stated cautiously; partial overlaps noted)
1. **Justified-scope (absence and quantifier) validation for agent knowledge derived from heterogeneous tools without DBMS support.** For example, re-checking "no cheaper eligible supplier exists" when the source is an API without predicate notifications. The classical solutions assume a DBMS. 2611.19859 identifies the failure but does not solve it.
2. **Graded soundness classes for validators** (deterministic implication vs empirical vs LLM), made explicit and enforced (fail-closed) at reuse time. PlanFence offers one deterministic class (version equality). 2609.25130 shows that LLM claim-checks work empirically. Nobody combines them with explicit labels and cost.
3. **A cost and reliability regime map that includes exact lazy query replay** as a first-class baseline, distinct from transcript replay or document re-reading. 2610.03902 maps memory vs re-reading on one clinical dataset with 7B models. MERIT's "full replay" is transcript replay. I found **no** study that compares exact replay of an agent's *recorded queries* (compare result hashes; recompute inference only on change) against materiality validation and TTL under priced prompt caching.
4. **Completeness receipts** for paginated, truncated or limited observations (§5.3).
5. **Cross-principal reuse of derived knowledge** under revocation (§5.5).

**Honesty note.** Items 1–3 are *gaps in what I found*, not proven gaps. The 2026 literature is moving weekly: at least eight of the papers above were first posted after 2026-10-01. A further search before G02 is warranted.

---

## 7. Implications for E001 (summary; detail in E001_PROPOSAL.md)
- Treat **source-filtered exact replay with prompt caching** as the baseline to beat, not a straw man.
- Measure **full-pipeline cost**, with cached and uncached input and output tokens priced separately, plus query cost and latency.
- Use an **execution oracle**, not an LLM judge.
- Materiality validators must declare a **soundness class**. Unknown coverage must **fail closed**.
- Expect any win to be **regime-bound**: high replay cost, high immaterial-change rate, cheap validators. Validation cost depends on **access paths, not monotonicity alone**. An indexed absence or min check can be cheap, and a monotone conclusion can need an expensive check. E001_PROPOSAL H2 treats this as a hypothesis to test, not a result.

---

## Appendix A. Evidence ledger (authoritative read levels)

- Every row was read on 2026-11-27 from `https://arxiv.org/html/<id><ver>`. The version stamp inside each downloaded file matched the pinned version.
- "Sections read" lists everything I read; nothing else in the paper was read.
- **No paper was read cover to cover.** Papers with only abstract-level reading are [ABS] and are not listed here.

| arXiv ID (pinned) | Short name | Sections read (full text) | Level |
|---|---|---|---|
| [2610.25123v2](https://arxiv.org/abs/2610.25123v2) | Tether | Abstract; §2–§6.3; Table 1; §6.6 + Table 3; §7; §8; §9 | FT, partial |
| [2610.03902v1](https://arxiv.org/abs/2610.03902v1) | When Evidence Changes | Abstract; §1–§2; §4; §5; §6.2; §6.3; §7; §8; §9; App. A excerpts; captions of Tables 1–29; rows of Table 24 | FT, partial |
| [2609.03340v2](https://arxiv.org/abs/2609.03340v2) | PlanFence | Abstract; §3 (Def. 1, Assumption 1, Prop. 1); §4; §6.2 opening; §7; App. E.3 | FT, partial |
| [2611.19859v1](https://arxiv.org/abs/2611.19859v1) | When Facts Change | §1–§5.2 (Tables 1–2); §7–§9 | FT, near-complete (§5.3–§6 not read) |
| [2607.23929v2](https://arxiv.org/abs/2607.23929v2) | MemTX | Abstract; §3.1–§3.5; §5 ("Scope blind spots", "Ablations", "Pricing the strong gate", "Cost and isolation") | FT, partial |
| [2608.10509v1](https://arxiv.org/abs/2608.10509v1) | MAP-Graph | Abstract; §3.2–§3.4; §4.4 "Scope and limitations"; §4.5 opening | FT, partial |
| [2605.07242v1](https://arxiv.org/abs/2605.07242v1) | MemoRepair | Abstract; §2.4 (problem statement); §3.4 Table 4 caption and text; §5 | FT, partial |
| [2610.05732v1](https://arxiv.org/abs/2610.05732v1) | PACMI | Abstract; §6; §7 | FT, partial |
| [2607.04089v1](https://arxiv.org/abs/2607.04089v1) | PLACEMEM | Abstract; §5 | FT, partial |
| [2611.02338v1](https://arxiv.org/abs/2611.02338v1) | MemShift | Abstract; Limitations | FT, partial |
| [2609.00243v1](https://arxiv.org/abs/2609.00243v1) | Invalidation Contracts | Abstract; §6.4 | FT, partial |
| [2611.22238v1](https://arxiv.org/abs/2611.22238v1) | Context Has a Price | Abstract; §2 "Reference prices"; §4 "Cost rises"; §5 "The sign flips"; table captions; Limitations | FT, partial |
| [2609.25130v1](https://arxiv.org/abs/2609.25130v1) | Impact Is Not Invalidation | Abstract; §V-B (Table III); §VI | FT, partial |
| [2609.01836v2](https://arxiv.org/abs/2609.01836v2) | EAL-Bench | Abstract; §4.2 excerpt; Limitations | FT, partial |
| [2609.00749v1](https://arxiv.org/abs/2609.00749v1) | ContextPipe | Abstract (incl. workshop reference line) | ABS |

All other arXiv sources in §3 are [ABS], from arXiv API metadata and abstracts. Classical sources in §4 are [META] unless marked.

## Appendix B. Not verified / deferred
- SSRN record 7480138 (PACMI date discrepancy): not fetched.
- CaMeL, FIDES, AgentDojo, TMA-NM, Infini Memory, EvoMem, MemSecBench, AgingBench and Spencer (2607.21962): cited only via 2610.03902. Not verified.
- McSherry et al., "Differential Dataflow" (CIDR 2013): not verified in this pass.
- The NeurIPS 2026 near-miss titles listed in §1.1: titles only.
- Microsoft SQL Server Query Notifications and Materialize: not verified in this pass.
- EAL-Bench (2609.01836v2): I did not locate the per-writer table behind "up to 45.4%". The figure is cited from the abstract only.
- MemTX (2607.23929v2): the number of cases in the source-scope family was not extracted.
- MemoRepair (2605.07242v1): which benchmark the 1%-edge-drop ablation ran on was not confirmed beyond the Table 4 text.
- **Director-side verification gap:** the director's environment could not fetch the HTML of 2610.03902, 2610.25123 and 2611.19859. Every key claim in §2.0 names a pinned version and a section or table, so it can be checked against the PDF (`https://arxiv.org/pdf/<id>v<n>`).
