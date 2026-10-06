# Lab 3 — Launch Strategy (Ascend IQ)

**Builds on:** `lab-1-gate-map.md` (severity × stage) + `lab-ci-gate-policy.md` (per-dimension CI policy) + M2/M3 failures

## 4.0 Release Criteria

| Gate | Stage | Failure | Metric | Threshold | Dataset | Method |
|---|---|---|---|---|---|---|
| **Hard** | PR | 1. Enterprise pricing hallucination ($49 vs $59), P0 | Pricing factual accuracy rate: % of pricing claims matching the current source-of-truth price | **100%** (0% miss tolerance) | Pricing-tagged cases in the ≥30-case golden set, including stale-cache cases. Case count not specified. | Code-based Layer 1 rule (`layer1_pricing_guard`): diff every quoted dollar figure against the current-price field; an "Old Price" match is flagged as a stale-cache trajectory failure |
| **Soft** | Staging | 2. Entity & competitive data inaccuracies, P1 | Entity accuracy rate: % of entity claims that are correct | **≥ 90%** | Entity-tagged cases in the ≥30-case golden set | LLM judge checked against a reference record |
| **Soft** | Staging | 4. T-03-A trajectory ordering violation (HOLD) | Trajectory ordering pass rate (Strict match) | **≥ 95%** | Trajectory cases in the ≥30-case golden set, including T-03-A | Strict trajectory matching, deterministic replay |
| **Advisory** | PR | 3. Brand voice & tone violations, P2 | Brand-voice violation rate: % of generated B2B emails containing unsanctioned slang (e.g. "killer", "game changer") | **≤ 5%** (warn only, never block) | Email-generation cases in the golden set plus a banned-phrase list. Case count and list location not specified. | Code-based Layer 1 banned-phrase check |

## 4.1 CI Gate Policy

- **Per-dimension, never a blended "quality" number.** Each dimension is gated independently.
- **Golden set:** ≥ 30 cases, run as **deterministic replay** with no live model calls.

| Dimension | Floor | Max Regression | Blocking? |
|---|---|---|---|
| Faithfulness | 95% | 2pp | **Blocking** |
| Task Completion | 90% | 5pp | **Blocking** |
| Tool Selection | 95% | 3pp | **Blocking** |
| Safety / Policy | 99% | 1pp | **Blocking** |
| Latency (p95) | ≤ 3s | +500ms | Warn-only |
| Cost per task | ≤ $0.02/query | +20% | Warn-only |

**How CI relates to the release gates:** CI gates the *aggregate dimension score* on every PR. The Soft gate on T-03-A is a *case-level* check at staging. The two do not conflict. Tool Selection can be a blocking CI dimension while T-03-A is separately a Soft @ Staging gate.

**Reference result:** PR #218 was blocked on Faithfulness (96 → 87) and Tool Selection (88, below the 95% floor). See `lab-ci-gate-policy.md`.

## 4.2 Mitigation Plan (Soft gates)

**Lever: Feature flag.**

**Applies to:** the Soft gates on entity & competitive data accuracy (Failure 2) and trajectory ordering (Failure 4).

**Rationale:** If we give a poor answer to the wrong client, we can do lasting damage to the relationship; should not deploy features without confidence.

**How it contains the risk while we ship:** The affected capability sits behind a flag, so it is not exposed to customers while a Soft gate is below threshold. The flag can be switched off instantly without a redeploy, which limits the damage to a client relationship from a wrong answer.

**Not yet specified (to be defined):** the flag's default state at launch, who can flip it, and the metric trigger for turning it off.
