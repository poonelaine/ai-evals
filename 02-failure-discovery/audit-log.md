


# AI Evals Failure Taxonomy Audit Log

## One-Line Summary
Total confirmed failures: 9 (8 `#HALLUCINATION`, 1 `#UX_TRUST`)

## Human Overrides
* None (All judge-flagged failures were confirmed).

## Audit Rows Table

| Row ID | Status | Tag | Reason |
|---|---|---|---|
| 0 | Confirmed Failure | `#HALLUCINATION` | Agent omitted a critical pricing fact, failing to synthesize grounded facts. |
| 2 | Confirmed Failure | `#HALLUCINATION` | Agent fabricated extra information not present in the context. |
| 3 | Confirmed Failure | `#HALLUCINATION` | Agent hallucinated a feature (pool) that conflicts with the grounded facts. |
| 4 | Confirmed Failure | `#HALLUCINATION` | Agent failed to synthesize grounded facts regarding dietary constraints (vegan). |
| 6 | Confirmed Failure | `#HALLUCINATION` | Agent fabricated a non-existent entity ("Mountain View"). |
| 7 | Confirmed Failure | `#HALLUCINATION` | Agent fabricated a fake flight number (#404). |
| 11 | Confirmed Failure | `#HALLUCINATION` | Agent fabricated incorrect pricing information ($50). |
| 13 | Confirmed Failure | `#HALLUCINATION` | Agent fabricated a non-existent pet fee policy. |
| 15 | Confirmed Failure | `#UX_TRUST` | Agent output raw JSON, breaking user-facing formatting expectations. |

