# Lab 1 — Eval Gate Map (Ascend IQ)

**Builds on:** M2 Failure Taxonomy (`02-failure-discovery/failure-taxonomy.md`) + M3 Eval Spec (`03-eval-specs/lab-2-eval-spec.md`)

## Gate Map

| # | Failure | Trust Tag | Frequency | M2 Severity (P-rank) | Gate Severity | Pipeline Stage | Rationale |
|---|---|---|---|---|---|---|---|
| 1 | Enterprise pricing hallucination ($49 vs $59) | #HALLUCINATION | HIGH (3/20) | P0 | **Hard** | **PR** | Deterministic, zero-cost detector (`layer1_pricing_guard`) already exists with 100% catch-rate threshold — no reason to let a known-bad price merge at all. Block wrong prices at the earliest stage. |
| 2 | Entity & Competitive Data Inaccuracies (competitor rate limits, office locations, exec bios) | #HALLUCINATION | HIGH (5/20) | P1 | **Soft** | **Staging** | Detection is judge/retrieval-based, not a clean string diff — noisier and more prone to false positives than pricing. Frequent and reputationally damaging enough to require a real checkpoint before customer exposure, but blocking every PR on it risks over-gating. |
| 3 | Brand Voice & Tone Compliance Violations (unsanctioned slang in B2B email gen) | #UX_TRUST | LOW (1/20) | P2 | **Advisory** | **PR** | M2 taxonomy explicitly notes "no legal/compliance risk." Low frequency, low stakes — flag early and cheaply as a non-blocking warning; don't block merges over a style issue. |
| 4 | T-03-A — Trajectory ordering violation (Lab 1b, Strict matching, HOLD verdict) | — | — | HOLD (unresolved) | **Soft** | **Staging** | HOLD verdict signals genuine evaluator uncertainty — not clean enough for an automatic Hard/PR block, but it's a workflow/trajectory defect (not a one-off wrong answer) that shouldn't reach real users unchecked. |

### Severity distribution check
- Hard: 1 (pricing)
- Soft: 2 (entity accuracy, trajectory ordering)
- Advisory: 1 (brand voice/tone)

✅ Meets the ≥1 Hard, ≥1 Soft, ≥1 Advisory requirement.

### Sample-interaction references
- **Failure 1:** M3 Lab 2 Eval Spec — Target Risk: *"InsightFlow Enterprise pricing hallucination (P0, carried from M2 taxonomy → Lab 1a → Lab 1b)"*; Evaluator: `layer1_pricing_guard` (Layer 1 deterministic rule, shipped in Lab 1a).
- **Failure 2:** M2 Failure Taxonomy — Rank 2, *"Inaccurate competitor rate limits, office locations, or executive bios degrades sales rep reliance on Ascend IQ and erodes brand authority."*
- **Failure 3:** M2 Failure Taxonomy — Rank 3, *"Unsanctioned slang ('killer', 'game changer') in B2B email generation damages enterprise brand positioning, though it carries no legal/compliance risk."*
- **Failure 4:** M3 Lab 1b Trajectory Eval — Case T-03-A, ordering violation, graded Strict, verdict: HOLD.
