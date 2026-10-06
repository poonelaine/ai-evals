# Lab 2 — CI Gate Policy (PR #218 Replay)

**Replay set:** 30-case regression golden set, deterministic replay (no live model calls)
**Builds on:** `lab-1-gate-map.md` (severity × stage placements) + M2/M3 failures

## Per-Dimension Policy

| Dimension | Floor | Max Regression | Blocking? |
|---|---|---|---|
| Faithfulness | 95% | 2pp | **Blocking** |
| Task Completion | 90% | 5pp | **Blocking** |
| Tool Selection | 95% | 3pp | **Blocking** |
| Safety / Policy | 99% | 1pp | **Blocking** |
| Latency (p95) | ≤ 3s | +500ms | Warn-only |
| Cost per task | ≤ $0.02/query | +20% | Warn-only |

> Policy is per-dimension, never a blended "quality" score. Each dimension is evaluated and gated independently.

## PR #218 Replay — Main vs. PR Deltas

| Dimension | Main | PR | Δ | Blocking? | Result |
|---|---:|---:|---:|---|---|
| Faithfulness (grounding) | 96 | 87 | −9 | Blocking | 🔴 **FAIL** — below floor (87 < 95%) and exceeds max regression (−9pp > −2pp cap) |
| Task Completion | 92 | 93 | +1 | Blocking | 🟢 PASS — above floor, net improvement |
| Tool Selection | 90 | 88 | −2 | Blocking | 🔴 **FAIL** — below floor (88 < 95%); Δ itself is within the 3pp max-regression cap, but the floor breach alone fails the gate |
| Safety / Policy | 99 | 99 | 0 | Blocking | 🟢 PASS |
| Latency | 84 | 80 | −4 | Warn-only | 🟡 WARN — degraded, non-blocking (units: normalized score, not yet reconciled to p95 seconds — see Open Item below) |
| Cost per task | 88 | 82 | −6 | Warn-only | 🟡 WARN — degraded, non-blocking (units: normalized score, not yet reconciled to $/query — see Open Item below) |

## Gate Result: 🔴 BLOCK

## Merge Decision: **DO NOT MERGE** — PR #218 is blocked.

**Reason:** Two blocking dimensions fail their gate:

1. **Faithfulness** dropped 9pp (96 → 87), breaching both the 95% floor and the 2pp max-regression cap. This is the dimension that covers the P0 Enterprise-pricing hallucination ($49 vs $59) — a regression of this size is not a borderline call and must not reach staging.
2. **Tool Selection** fell to 88, below its 95% floor. This dimension maps directly to the T-03-A trajectory/ordering failure (HOLD verdict, Lab 1b) — a sub-floor score here indicates the workflow-ordering risk may be regressing, not just the single known case.

Task Completion and Safety both pass cleanly. Latency and Cost degraded but are warn-only by policy and do not factor into the merge call — they should be logged and monitored, not blocked on.

## Open Item
Latency and Cost units in the replay output (0–100 scale) have not yet been reconciled against the floor units defined in policy (p95 seconds, $/query). This does not affect the current merge call since both dimensions are warn-only, but should be clarified before relying on these two rows for trend monitoring.
