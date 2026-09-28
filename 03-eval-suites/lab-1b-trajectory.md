# Lab 1b — Trajectory Eval

**Task:** Ascend IQ usage-drop task (TASK-A) — "Enterprise weekly report shows active users down 30%: pull last 4 weeks of usage, check for a data-ingestion gap, compare week-over-week, draft a reply explaining the cause."
**Trajectory graded:** `T-03-A`
**Reference:** `T-GOLD-A`
**Source:** `trajectory-traces.csv`

## Matching mode

**Strict (ordered).** Chosen because this task has a genuine causal dependency: the cause of the usage drop must be verified (`get_ingestion_status`, `compare_weeks`) *before* it can be asserted in the customer-facing reply (`draft_reply`). Unordered matching would credit the right tool calls happening in any sequence, but here the sequence itself is the thing that determines whether the output is true or false — grading it unordered would hide the exact failure this trace exhibits.

## Trajectories compared

| Step | Gold (`T-GOLD-A`) | T-03-A (actual) |
|---|---|---|
| 1 | `get_account` | `get_account` — ok |
| 2 | `get_usage` (4 weeks) | `get_usage` (4 weeks) — ok |
| 3 | `get_ingestion_status` | **`draft_reply`** — "churn risk, recommend an outreach campaign" (drafted before cause verified) |
| 4 | `compare_weeks` | `get_ingestion_status` — finds the ingestion gap, **contradicting** the draft already written |
| 5 | `draft_reply` (grounded in verified cause) | *(none)* — draft is never revised or retracted after the contradiction |

`compare_weeks` is never called at any point in T-03-A.

## Six dimensions

| Dimension | Score | Evidence |
|---|---|---|
| **Tool selection** | PARTIAL | All 4 tools called are legitimate (no hallucinated tools). But `compare_weeks` — explicitly required by the task ("compare week-over-week") — is never invoked. |
| **Argument correctness** | PASS | Correct account ID (`ACME-2231`) and `weeks=4` used consistently throughout; no wrong-argument errors. |
| **No redundant/looping steps** | PASS | No repeated calls, no loops. |
| **Recovery** | FAIL | `get_ingestion_status` (step 4) returns evidence that directly contradicts the already-drafted reply. The agent never revises or retracts it — the exact moment recovery was needed, and it didn't happen. |
| **Plan coherence** | FAIL | Core ordering violation: the reply is drafted at step 3, before the cause is verified at step 4, and before `compare_weeks` is ever called. Drafting a conclusion before gathering the evidence that determines it is a causal-order violation, not a stylistic one. |
| **Task completion** | FAIL | The task asked for a reply "explaining the cause." The shipped reply attributes the drop to churn risk; the actual cause (a 3-day ingestion gap, Jul 8–10) was discovered one step later and never incorporated. The final artifact is factually wrong. |

## Verdict

**HOLD.**

A plausible-sounding final answer does not excuse a failed path on a P0. T-03-A's draft reads like a reasonable CS response ("churn risk → recommend outreach"), which is exactly the risk this eval is designed to catch: fluent, confident output hiding a broken path. Three of six dimensions fail (recovery, plan coherence, task completion), and the failure is causal — the agent generated its conclusion before it had evidence, then failed to correct course when contradicting evidence arrived one step later. Shipping this trajectory would mean shipping a customer-facing reply that misattributes a data-ingestion bug as a churn signal, with no verification step ever closing that gap.
