# Lab 1: Coverage Matrix (Ascend IQ)

## Coverage row: Ascend IQ

| Product | Hallucination | Bias | Latency | Toxicity | Drift Monitoring |
|---|---|---|---|---|---|
| Ascend IQ | ⚠️ | ❌ | ✅ | ⚠️ | ❌ |

**Justifications**
- **Hallucination ⚠️:** Output does not match provided ground truth or context, misleading prospects on Enterprise pricing ($49 vs $59).
- **Bias ❌:** Results are not consistent for specific groups.
- **Latency ✅:** Measured, but failing the SLA (5s response time).
- **Toxicity ⚠️:** Output is not free of offensive / harmful / prohibited language; unsanctioned slang ("killer", "game changer") in B2B email generation.
- **Drift Monitoring ❌:** Model does not perform as well today as on launch day.

## Method + Ground Truth

| Risk | Method (how it's evaluated) | Ground truth (what it's graded against) |
|---|---|---|
| Hallucination ⚠️ | **Primary:** code-based Layer 1 rule checking pricing, SQL export and SOC2 claims. **Backup:** LLM judge checking that the answer is grounded. | Cached pricing page (Enterprise = $59), product docs, security attestation (SOC2 Type II). |
| Toxicity ⚠️ | **Primary:** banned-phrase check for unsanctioned slang (e.g. "killer", "game changer"). **Backup:** LLM judge on tone. | Brand voice guide and the list of sanctioned terms. |

## Strategic acceptance

- **Accepted gap:** Bias ❌
- **Why acceptable now:** No M2 failure.
- **Kill criterion:** Review at end of Q2.

## Critical mitigation

- **Critical gap:** Drift Monitoring ❌
- **Plan:** (a) Re-run the Layer 1 pricing/claims check against the source of truth whenever the pricing page or docs change, and (b) run a scheduled replay of the eval set against the launch baseline.
- **Date:** Q1
- **Owner:** Marketing Manager
