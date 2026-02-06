# Workload Decision Guide

> Layer 4 of the GenAI Adoption Framework

This guide provides structured decision frameworks for the five most consequential choices teams face when building and operating generative AI workloads. Each section presents the decision, the factors that influence it, and concrete thresholds to guide action.

---

## Decision 1: Golden Dataset Size

**The question:** How many inputs should the golden dataset contain?

The golden dataset is the empirical foundation for every quality decision. Too small and the evaluation is unreliable — a single outlier can swing aggregate scores. Too large and the dataset becomes expensive to maintain and slow to evaluate. The right size balances statistical reliability against maintenance cost.

**Minimum viable: 30 inputs.** This is the floor for any workload entering production. Thirty inputs provide enough coverage to detect gross regressions and validate basic functionality. At this size, you can observe whether the workload handles the most common input patterns correctly. You cannot make fine-grained quality claims or detect subtle regressions.

**Recommended: 100+ inputs.** At 100 inputs, the evaluation becomes statistically meaningful. Score distributions stabilize. You can detect moderate regressions (5-10% score drops) with reasonable confidence. You have room for dedicated edge-case coverage without sacrificing happy-path representation. This is the target for any workload that serves more than a small internal team.

**When to invest in more.** Expand beyond 100 inputs when:
- The workload serves external users or processes sensitive data — the cost of undetected quality failures is high.
- The input distribution is highly diverse — many languages, many domains, many formats — and 100 inputs cannot cover the variation.
- You are making fine-grained comparisons between prompt versions or model versions and need statistical power to detect small differences.
- Production monitoring has revealed edge cases that are not represented in the current dataset.

**Practical guidance.** Grow the dataset incrementally. Start with 30 for initial deployment. Add 10-20 inputs per month from production discoveries, user feedback, and adversarial test findings. Prune inputs that are redundant or no longer represent current usage patterns. Version the dataset alongside the prompt.

---

## Decision 2: Evaluation Dimensions

**The question:** Which scoring dimensions should the evaluation scorecard include, and how should they be weighted?

Not all dimensions matter equally for all workload types. A classification workload lives or dies on accuracy; tone barely matters. A customer-facing generation workload must balance accuracy, tone, format, and safety. Choosing the wrong dimensions — or weighting them equally when they are not equally important — produces evaluations that fail to catch the failures that matter.

**Dimension selection by workload type:**

| Dimension | Summarization | Classification | Generation | Extraction | Transformation | Conversational |
|---|---|---|---|---|---|---|
| Accuracy | High | Critical | Medium | Critical | High | Medium |
| Completeness | High | Low | Medium | High | Medium | Medium |
| Format compliance | Medium | High | High | Critical | High | Low |
| Tone / Style | Medium | Low | High | Low | Medium | High |
| Safety | Medium | Medium | High | Low | Low | Critical |
| Conciseness | High | Low | Medium | Low | Medium | Medium |

**Critical** means a failure in this dimension is a deployment blocker regardless of other scores. **High** means this dimension should receive significant weight in the aggregate score. **Medium** means it contributes to overall quality but is not independently gating. **Low** means it is tracked but has minimal influence on deployment decisions.

**Practical guidance.** Start with three to five dimensions. It is better to score three dimensions rigorously than seven dimensions superficially. Add dimensions when you discover that quality failures are occurring in areas you are not measuring. Drop dimensions that consistently score perfectly and add no decision-making value.

---

## Decision 3: Adversarial Test Coverage

**The question:** How many adversarial test cases are needed, and what categories must they cover?

Adversarial testing is not about hitting a magic number of test cases. It is about covering the attack categories relevant to the workload's risk profile. A low-risk internal summarization workload needs fewer adversarial tests than a high-risk customer-facing conversational workload — but neither gets zero.

**Minimum test cases by workload type:**

| Attack Category | Internal, Non-Sensitive | Internal, Sensitive | External-Facing |
|---|---|---|---|
| Direct prompt injection | 3-5 | 5-10 | 10-20 |
| Indirect prompt injection | 0-2 | 5-10 | 10-20 |
| Output format override | 2-3 | 5-8 | 8-15 |
| Information exfiltration | 1-2 | 5-10 | 10-20 |
| Extreme inputs | 3-5 | 5-8 | 8-12 |

**How to read this table.** An internal workload processing non-sensitive data should have at minimum 9-17 adversarial test cases across all categories. An external-facing workload should have at minimum 46-87. These are minimums, not targets.

**When to expand coverage.** Add adversarial test cases when:
- A new attack pattern is published in security research or industry advisories.
- Production monitoring detects an attempted attack that is not covered by existing tests.
- The workload's risk profile changes — for example, it begins processing a higher data classification tier or becomes available to external users.
- A model update changes the model's susceptibility to known attack patterns.

**Practical guidance.** Maintain a shared library of adversarial test cases that can be applied across workloads, adapted by workload type. Track adversarial test results over time to identify trends. When a test that previously passed starts failing, investigate immediately — it may indicate a model behavior change.

---

## Decision 4: Monitoring Strategy

**The question:** What sampling rate, quality thresholds, and alert levels are appropriate for production monitoring?

Monitoring cost scales with sampling rate. Over-monitoring wastes resources; under-monitoring misses degradation. The right strategy depends on the workload's risk profile, traffic volume, and the cost of quality failures.

**Sampling rate:**
- **Low-risk, low-volume workloads (fewer than 100 invocations per day):** 10% sampling rate. At this volume, 10% may only yield a handful of samples per day, so weekly scoring cadence is appropriate.
- **Medium-risk or medium-volume workloads (100-10,000 invocations per day):** 5% sampling rate. This yields enough daily samples for meaningful weekly analysis.
- **High-risk or high-volume workloads (more than 10,000 invocations per day):** 1-3% sampling rate. At high volume, even low sampling rates produce large sample sets. Focus on statistical analysis rather than individual review.

**Quality drift thresholds:**
- **Alert threshold:** Any evaluation dimension drops more than 5% below the deployment baseline score, sustained over two consecutive scoring periods. This triggers investigation but does not require immediate action.
- **Action threshold:** Any evaluation dimension drops more than 10% below baseline, or a critical dimension drops more than 5%. This requires prompt investigation and remediation within a defined time window.
- **Emergency threshold:** Any safety-related dimension drops more than 15% below baseline, or a critical failure (harmful output, data leakage) is detected in any sample. This triggers immediate investigation and potential workload suspension.

**Alert versus log:**
- **Log only:** Dimension scores are within normal variance (less than 5% drift). Scores are recorded for trend analysis but no notification is sent.
- **Alert to workload team:** Alert threshold is breached. The team responsible for the workload is notified and expected to investigate within the current sprint.
- **Alert to workload team and platform team:** Action threshold is breached. Both teams are notified. The platform team verifies that the issue is workload-specific and not a platform-wide problem.
- **Escalation and potential suspension:** Emergency threshold is breached. The workload may be suspended or rolled back while investigation proceeds.

---

## Decision 5: Model Selection

**The question:** When should a workload use a smaller, cheaper model versus a larger, more capable model?

Model selection is a cost-quality trade-off, not a quality-maximization exercise. The most capable model is not always the best choice. Smaller models are cheaper per invocation, faster, and often sufficient for well-scoped tasks. The goal is to find the least expensive model that meets the workload's quality requirements.

**Start small, scale up.** For every new workload, begin evaluation with the smallest viable model. Run the full evaluation suite. If the model meets all quality thresholds defined in the workload brief, there is no reason to use a larger model. Only move to a larger model if the smaller model fails to meet thresholds and prompt optimization cannot close the gap.

**Factors favoring smaller models:**
- The task is well-defined and narrow (classification, extraction, simple transformation).
- The prompt includes clear structure and few-shot examples that compensate for reduced model capability.
- Latency requirements are tight and larger models introduce unacceptable delay.
- Volume is high and per-invocation cost savings compound significantly.
- The workload processes non-sensitive data where the consequences of occasional lower-quality outputs are minimal.

**Factors favoring larger models:**
- The task requires nuanced reasoning, multi-step logic, or handling of complex edge cases.
- The input distribution is highly variable and the workload must generalize broadly.
- The workload is customer-facing and quality failures have reputational or legal consequences.
- Prompt optimization with smaller models has been attempted and failed to meet quality thresholds.
- The task involves safety-critical decisions where higher accuracy justifies higher cost.

**Practical guidance.** Maintain evaluation results across model sizes for each workload. This creates an empirical record that supports future model selection decisions. When a model provider releases a new smaller model, re-evaluate existing workloads to see if they can be migrated down. When costs need to be reduced, model downsizing informed by evaluation data is one of the most effective levers.
