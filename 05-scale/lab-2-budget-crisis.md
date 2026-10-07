# Lab 2: Budget Crisis (Ascend IQ)

## Portfolio decision grid ($200K/quarter cap, max 3 L3)

| Failure | Trust metric | Risk | Level | Cost |
|---|---|---|---|---|
| Data Fabrication | Hallucination Rate | P0 | L3 | $85K |
| Context Specificity | UX Trust | P1 | L2 | $7K |
| Source Attribution Failure | Robustness | P1 | L3 | $65K |
| Data Bias | Fairness | P2 | L1 | $2.75K |
| Cost Overruns | Latency | P3 | L3 | $25K |

**Total spend:** $184.75K of $200K ($15.25K headroom)
**L3 slots used:** 3 of 3

## Fallback methods

| Non-L3 item | Level | Fallback method |
|---|---|---|
| Context Specificity (P1) | L2 | (a) Weekly sample of ~20 traces, with an LLM judge checking each answer against its retrieved source. (b) Code-based rule that flags answers citing source data past a set age. |
| Data Bias (P2) | L1 | (b) Review of flagged outputs and sales-rep feedback. (c) Re-decision at the end of Q2, consistent with the Lab 1 accepted gap. |

### Incident-review story: Context Specificity at L2 (DRAFT, confirm or edit)

> We held Context Specificity at L2 instead of L3 because the budget cap allowed only three L3 slots, and we spent them on the failures that cause the most severe customer harm: Data Fabrication (P0, Enterprise pricing, SQL export and SOC2 claims), Source Attribution Failure (P1), and Cost Overruns. Context still had active controls: a weekly LLM-judge sample against retrieved sources and a code-based staleness rule, plus the Drift Monitoring mitigation (pricing/claims re-check on source change and scheduled replay against the launch baseline) due 31 Mar 2027.
>
> We would raise Context to L3 if a context-related failure reached a customer or prospect, or if the weekly sample or staleness rule showed a rising failure trend, and we would fund it by trading down one of the other L3 slots.
