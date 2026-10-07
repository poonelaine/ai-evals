# Ship/Hold Memo: Ascend IQ

**To:** [TBD: addressee to be confirmed]
**From:** Elaine Poon, Sr Product Manager
**Decision:** SHIP with conditions

---

## The Answer

**Recommendation: SHIP Ascend IQ with conditions, because quoting the wrong Enterprise price ($49 vs $59) to a prospect would do lasting damage to buyer trust, and the conditions below keep that failure from reaching customers.**

### Conditions

| # | Condition | Gap it closes | Source |
|---|---|---|---|
| A | PR #218 stays unmerged until Faithfulness is ≥95% (currently 87%) and Tool Selection is ≥95% (currently 88%) | Two blocking CI failures | M4 lab-ci-gate-policy.md |
| B | Hard pricing gate shows 100% on the full pricing-tagged golden set, not just one case and two controls | No full-set pricing result | M3 lab-1-eval-suite.md · M4 lab-2-launch-strategy.md |
| C | Entity and trajectory features stay behind the feature flag until their Soft gates pass (entity ≥90%, trajectory ordering ≥95%) | Soft gates have no results | M4 lab-2-launch-strategy.md |
| D | Drift monitoring is live by 31 Mar 2027, owned by the Marketing Manager | Critical coverage gap (Drift Monitoring ❌) | M5 lab-1-coverage-matrix.md · M5 lab-2-budget-crisis.md |

## Business Risk
+ SHIP (with conditions): ~$2.5M renewal revenue protected; reputational risk capped at a 30-day audit window, with documented mitigation
+ HOLD: 50 enterprise contracts at upward of $50,000 a year each (~$2.5M ARR) at high churn risk in Q3; competitive window closes in 8 weeks

## Next Step
+ Decision needed: Approve SHIP-with-conditions (pricing behind the Hard gate; entity and trajectory behind the feature flag; Conditions A-D) by 15 Oct 2026, ahead of the end of Q3 on 30 Oct 2026.

## Reflection
+ Defining UX Trust was the hardest part of "good enough." Because the Agent answers correctly but misses crucial nuances (summarizes all reviews instead of isolating negatives as requested). Potential churn, lower quality of service.

