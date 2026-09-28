# M3 · Lab 2 · Eval Spec, Ascend IQ P0

## Part 1 · The 5-Part Eval Spec

| Question | Answer |
|---|---|
| **01 · Target Risk** | InsightFlow Enterprise pricing hallucination (P0, carried from M2 taxonomy → Lab 1a → Lab 1b) |
| Risk Type | Trajectory risk. The failure is a workflow/retrieval-step gap — the agent answered a pricing question without first confirming it was querying a live, current-of-truth pricing source, not merely that the final text string happened to be wrong. |
| Trust Metric | Pricing factual accuracy rate — % of pricing claims that match the current source-of-truth price at response time.|
| **02 · Evaluator** | Code-Based. Layer 1 deterministic rule (`layer1_pricing_guard`, shipped in Lab 1a). |
| Detection logic | Parse the reference/source-of-truth record for the current-price field; diff every dollar figure quoted in the prediction against it. If a quoted price matches an "Old Price" field specifically, flag it as a stale-cache trajectory failure (not a generic mismatch). Runs at zero marginal LLM cost — no judge call required. |
| **03 · Threshold** | 100% catch rate (0% miss tolerance) on all pricing-related queries in the eval set. Any single undetected stale/mismatched price is a hard gate failure and blocks the response before it reaches the customer. |
| Strategy | Safety First |
| **04 · Business Stakes** | "Misleading prospects on Enterprise pricing ($49 vs $59), native SQL export capabilities, or SOC2 Type II compliance risks deal collapse, sales pipeline loss, legal exposure, and irreversible buyer trust damage." "A single misrepresentation of SOC2 compliance or Enterprise pricing during procurement or security review can derail a 6-figure enterprise deal or expose the company to breach-of-contract claims."|
| **05 · Owner** | Marketing Manager |

## Trajectory fields

_Only if Risk Type = Trajectory. Otherwise delete this section._

| Field | Answer |
|---|---|
| Dimensions scored | Tool selection |
| Matching mode | Strict |

## Part 2 · Three Audience Messages

### A. For Engineering (Jira ticket)

**acceptance criteria (GIVEN/WHEN/THEN)**

GIVEN a user query asks about Enterprise pricing, WHEN the agent generates a response containing a dollar-figure price claim, THEN the system must diff that price against the current-price field in the live pricing source before releasing the response, AND IF the quoted price matches a stale/old-price value THEN the response must be blocked and regenerated from the live source, WITH zero tolerance for stale-price release.


### B. For UX / Design

**fallback experience when the gate blocks**

When the pricing gate blocks a response, the user should never see an error or a stalled reply. Show a brief, natural message such as: "Let me confirm the latest Enterprise pricing for you — one moment," while the system re-queries the live source, then delivers the corrected price. No mention of "gate," "hallucination," or internal system logic should reach the user

### C. For Leadership (bi-weekly update)

**ROI bullet**

Deploying a zero-cost, zero-tolerance pricing gate eliminates the #1-ranked P0 hallucination risk (15% of evaluated queries) that could otherwise derail 6-figure Enterprise deals or trigger breach-of-contract exposure — tracked going forward via pricing factual accuracy rate, targeting 100%.
