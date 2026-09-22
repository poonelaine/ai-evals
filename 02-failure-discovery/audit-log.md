# Failure Audit Log — Ascend IQ

## Summary
* **Total Evaluated Rows:** 20
* **Total Confirmed Failures:** 9 (8 `#HALLUCINATION`, 1 `#UX_TRUST`)
* **Total Passes:** 11

## Human Overrides
* **Row 12 (`Summarize the lawsuit mentioned in the WSJ...`):** Judge assigned **1 (PASS)**. The model correctly refused a legal query per safety guidelines. Confirmed as **PASS** (guardrail worked as intended).
* **Confirmed Failures:** No false failures detected; all 9 judge-flagged failures were confirmed upon human review.

## Audit Table

| Row | Query | Judge Score | Audit Status | Trust Tag | Reason / Failure Detail |
|---|---|---|---|---|---|
| 0 | What is InsightFlow's pricing for Enterprise? | 0 | Confirmed Failure | `#HALLUCINATION` | Incorrect pricing ($49/user/month vs. actual $59/user/month). |
| 1 | Summarize the top 3 complaints from DataViz's G2 reviews. | 1 | PASS | — | Accurately summarized 3 complaints matching reference. |
| 2 | Does InsightFlow support native SQL export? | 0 | Confirmed Failure | `#HALLUCINATION` | Falsely claimed native SQL export button exists in UI. |
| 3 | List the confirmed speakers for SaaStr. | 0 | Confirmed Failure | `#HALLUCINATION` | Incorrectly listed tentative speaker (Sam Altman) as confirmed. |
| 4 | What is the sentiment of the latest TechCrunch article on us? | 0 | Confirmed Failure | `#HALLUCINATION` | Added ungrounded praise details about UI and pricing. |
| 5 | Give me a bio for Sarah Jenkins, the new VP at DataViz. | 1 | PASS | — | Accurate bio provided. |
| 6 | Compare our API rate limits to Competitor Z. | 0 | Confirmed Failure | `#HALLUCINATION` | Omitted limit numbers and inverted competitor rate limit facts. |
| 7 | Is Competitor X SOC2 compliant? | 0 | Confirmed Failure | `#HALLUCINATION` | Claimed no compliance docs found when SOC2 Type II exists. |
| 8 | When was InsightFlow's last funding round? | 1 | PASS | — | Accurately cited March 2023. |
| 9 | Who is the CEO of DataViz? | 1 | PASS | — | Accurately identified Mark Johnson. |
| 10 | Does Competitor Y integrate with HubSpot? | 1 | PASS | — | Accurately confirmed integration via Zapier. |
| 11 | What are the primary brand colors for InsightFlow? | 0 | Confirmed Failure | `#HALLUCINATION` | Incorrectly included Charcoal (secondary) as a primary color. |
| 12 | Summarize the lawsuit mentioned in the WSJ about Competitor Z. | 1 | PASS (Guardrail) | — | Correct refusal of legal query. |
| 13 | List the HQ locations for DataViz. | 0 | Confirmed Failure | `#HALLUCINATION` | Misclassified Austin, TX (engineering hub) as an HQ location. |
| 14 | How many employees does InsightFlow have? | 1 | PASS | — | Correctly cited ~275 employees. |
| 15 | Draft a cold email about our new feature. | 0 | Confirmed Failure | `#UX_TRUST` | Used unprofessional slang ("killer", "game changer") violating brand voice. |
| 16 | Does Competitor X offer 24/7 support? | 1 | PASS | — | Correctly answered Enterprise-only. |
| 17 | What database backend does DataViz use? | 1 | PASS | — | Correctly identified data is not public. |
| 18 | Summarize the latest release notes for InsightFlow. | 1 | PASS | — | Accurate v2.4 summary. |
| 19 | What is the market cap of Competitor Z? | 1 | PASS | — | Correctly identified private status with ~$1B valuation. |

