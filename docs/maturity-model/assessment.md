# Maturity Assessment

This self-assessment measures your organization's generative AI adoption maturity across all five layers of the framework. The results identify your current maturity stage, highlight specific gaps, and provide a foundation for planning your next investments.

Run this assessment quarterly. Maturity is not permanent, and quarterly reassessment detects regression before it becomes costly.

---

## Scoring Instructions

Each question is scored on a four-point scale:

| Score | Label | Definition |
|-------|-------|------------|
| **0** | Not Started | No work has been done. The capability does not exist. |
| **1** | Partial | Some progress has been made, but the capability is incomplete. It may exist in draft form, cover only some teams, or lack enforcement. |
| **2** | Complete | The capability is fully implemented and operational. It covers all teams and workloads in scope. It is enforced, not aspirational. |
| **3** | Optimized | The capability is fully implemented and continuously improving. Automation, measurement, and feedback loops are in place to refine it over time. |

**Score honestly.** A capability that exists on paper but is not enforced scores 0 or 1, not 2. A process that was set up once but has not been reviewed or updated is not optimized.

**Score what exists today, not what is planned.** Roadmap items score 0 until they are operational.

---

## Layer 1: Governance

| # | Question | Score (0-3) |
|---|----------|-------------|
| 1.1 | Is there a written acceptable use policy that defines who may use generative AI, for what purposes, with what data, and under what constraints? | |
| 1.2 | Is a data classification scheme defined with at least three tiers, and has it been applied to all data sources used in generative AI workloads? | |
| 1.3 | Does a model catalog exist listing every approved model with provider, pricing, classification tier, and use-case restrictions? | |
| 1.4 | Are organization-wide and per-team cost ceilings defined and enforced with automated alerts or hard stops? | |
| 1.5 | Is a governance review board operational with a defined charter, membership, meeting cadence, and documented minutes? | |
| 1.6 | Does a formal exemption process exist that documents the request, risk assessment, approver, duration, and revocation conditions for each exemption? | |
| 1.7 | Are governance artifacts (policies, catalog, exemptions, minutes) stored in version control with change history and review workflows? | |
| 1.8 | Is compliance reporting against governance policies generated automatically from operational telemetry? | |

**Layer 1 Maximum Score: 24**

---

## Layer 2: Gateway

| # | Question | Score (0-3) |
|---|----------|-------------|
| 2.1 | Is every sanctioned model request routed through a centralized gateway, with no approved teams bypassing it? | |
| 2.2 | Is authentication enforced on every gateway request, with unique credentials per team or application that are revocable? | |
| 2.3 | Does the gateway log every request with at minimum: timestamp, authenticated identity, model targeted, token count, and latency? | |
| 2.4 | Are at least two model providers configured with the gateway capable of routing to either based on defined rules? | |
| 2.5 | Is automatic failover configured and tested (real or simulated) so that provider outages do not cause complete service disruption? | |
| 2.6 | Is data loss prevention active on the gateway, inspecting request content and blocking requests that contain data above the permitted classification tier? | |
| 2.7 | Does the gateway enforce per-team or per-application budget limits with automated alerts, throttling, or hard stops? | |
| 2.8 | Is prompt and response content logging tiered by data classification, with restricted-tier content redacted or excluded from accessible logs? | |

**Layer 2 Maximum Score: 24**

---

## Layer 3: Operations

| # | Question | Score (0-3) |
|---|----------|-------------|
| 3.1 | Are request logs queryable by the operations team, enabling them to answer questions about request volume, latency, error rates, and usage by team? | |
| 3.2 | Is cost data tracked per request and aggregated into dashboards that show spend by team, model, and time period, with data matching provider invoices within 5 percent? | |
| 3.3 | Are automated alerts configured for anomalies including cost spikes, error rate increases, latency degradation, and unusual request volumes? | |
| 3.4 | Is there a developer self-service mechanism for API key issuance, documentation access, and usage visibility that does not require manual intervention from the operations team? | |
| 3.5 | Does the security team conduct a regular (at minimum weekly) review of blocked requests, data loss prevention triggers, authentication failures, and anomalous patterns? | |
| 3.6 | Are cost forecasts generated (at minimum 30, 60, and 90 days forward) and shared with finance stakeholders alongside actuals? | |
| 3.7 | Is developer satisfaction with the generative AI platform measured on a regular cadence and used to prioritize platform improvements? | |
| 3.8 | Do common incident types have automated runbooks that execute initial diagnosis and remediation, escalating to human responders only when necessary? | |

**Layer 3 Maximum Score: 24**

---

## Layer 4: Workloads

| # | Question | Score (0-3) |
|---|----------|-------------|
| 4.1 | Does every workload seeking production deployment complete a workload brief that documents business objective, models used, data classification, success metrics, and rollback criteria? | |
| 4.2 | Does every active workload maintain a golden dataset with curated inputs and expected outputs, versioned and stored alongside the workload configuration? | |
| 4.3 | Does the continuous integration pipeline run automated evaluation of every workload against its golden dataset on every change, blocking deployment on regression? | |
| 4.4 | Do workloads handling internal-tier or higher data have adversarial test suites covering prompt injection, jailbreak, and data exfiltration scenarios, running in CI? | |
| 4.5 | Is a percentage of production requests and responses sampled and evaluated for quality on an ongoing basis, with trends tracked and regressions alerting? | |
| 4.6 | When a model provider releases an update, does the evaluation pipeline automatically re-run golden datasets and adversarial suites against the new version and compare to baseline? | |
| 4.7 | Do workloads have defined lifecycle states (development, staging, production, deprecated, decommissioned) with criteria and approvals for transitions? | |

**Layer 4 Maximum Score: 21**

---

## Layer 5: Agent Security

| # | Question | Score (0-3) |
|---|----------|-------------|
| 5.1 | Does a centralized agent registry exist listing every sanctioned agent with identifier, owning team, capabilities, permitted tools, and approval status? | |
| 5.2 | Does every registered agent authenticate through the gateway with its own identity, distinct from the developer or team that created it, with logs distinguishing agent from human requests? | |
| 5.3 | Is every action taken by a registered agent (model requests, tool invocations, data access) logged and included in security operations reviews? | |
| 5.4 | Are agent permissions enforced at the tool-and-parameter level, not just at the tool level, with violations logged and blocked? | |
| 5.5 | Are active mechanisms in place to discover agents operating outside the registry (shadow agents), with discovered agents flagged for investigation? | |
| 5.6 | Are delegation chains between agents recorded and enforced, with depth limits configured and circular delegation detected and blocked? | |
| 5.7 | Is mutual authentication required for all agent-to-agent communication, ensuring both parties verify identity before exchanging data? | |
| 5.8 | Are agent permissions reviewed on at least a quarterly cadence, with inactive agents deregistered and changed scopes reflected in updated permissions? | |

**Layer 5 Maximum Score: 24**

---

## Scoring Summary

Record your scores in the following table:

| Layer | Question Scores | Layer Total | Layer Maximum | Percentage |
|-------|----------------|-------------|---------------|------------|
| Layer 1: Governance | 1.1: __ 1.2: __ 1.3: __ 1.4: __ 1.5: __ 1.6: __ 1.7: __ 1.8: __ | __ / 24 | 24 | __% |
| Layer 2: Gateway | 2.1: __ 2.2: __ 2.3: __ 2.4: __ 2.5: __ 2.6: __ 2.7: __ 2.8: __ | __ / 24 | 24 | __% |
| Layer 3: Operations | 3.1: __ 3.2: __ 3.3: __ 3.4: __ 3.5: __ 3.6: __ 3.7: __ 3.8: __ | __ / 24 | 24 | __% |
| Layer 4: Workloads | 4.1: __ 4.2: __ 4.3: __ 4.4: __ 4.5: __ 4.6: __ 4.7: __ | __ / 21 | 21 | __% |
| Layer 5: Agent Security | 5.1: __ 5.2: __ 5.3: __ 5.4: __ 5.5: __ 5.6: __ 5.7: __ 5.8: __ | __ / 24 | 24 | __% |
| **Total** | | **__ / 117** | **117** | **__% ** |

---

## Stage Determination

Your overall maturity stage is determined by the combination of your layer scores, not just the total. The maturity model is sequential: higher layers cannot compensate for gaps in foundational layers.

### Stage 1: Foundation

You are at Stage 1 if **any** of the following are true:

- Layer 1 (Governance) total is below 8 (less than 50 percent).
- Layer 2 (Gateway) total is below 8 (less than 50 percent).
- Layer 3 (Operations) total is below 4 (less than 25 percent, meaning minimal operations are not yet in place).

**Interpretation:** Your foundational layers are incomplete. Investing in Workloads or Agent Security at this point will create unsupported capabilities. Focus on achieving Stage 1 exit criteria for Layers 1, 2, and 3.

### Stage 2: Operational

You are at Stage 2 if **all** of the following are true:

- Layer 1 (Governance) total is 8 or above.
- Layer 2 (Gateway) total is 8 or above.
- Layer 3 (Operations) total is 8 or above.

And **any** of the following are true:

- Layer 4 (Workloads) total is below 11 (less than 50 percent).
- Layer 5 (Agent Security) total is below 6 (less than 25 percent).
- Any single layer has no individual question scoring 3 (no capabilities are in continuous improvement mode).

**Interpretation:** Your foundations are solid and your operations are maturing, but your workload and agent practices are not yet fully developed or continuously improving. Focus on Stage 2 exit criteria for Layers 3, 4, and 5.

### Stage 3: Optimized

You are at Stage 3 if **all** of the following are true:

- Layer 1 (Governance) total is 16 or above (67 percent or higher).
- Layer 2 (Gateway) total is 16 or above.
- Layer 3 (Operations) total is 16 or above.
- Layer 4 (Workloads) total is 11 or above (50 percent or higher).
- Layer 5 (Agent Security) total is 12 or above (50 percent or higher).
- At least three layers have an average question score of 2.0 or above, indicating capabilities are complete, not just partial.

**Interpretation:** All layers are active and operational. Multiple capabilities are in continuous improvement mode. Focus on sustaining optimization, closing any remaining gaps, and preventing regression.

---

## Gap Identification

After scoring, identify gaps using the following approach:

### Critical Gaps (Address Immediately)

Any question scoring **0** in Layers 1 or 2 is a critical gap. These are foundational capabilities that all other layers depend on. A gateway with no authentication (2.2 = 0) or governance with no data classification (1.2 = 0) undermines everything built on top.

**Action:** Treat these as blockers. Assign ownership, set a target date within 30 days, and track to completion.

### Structural Gaps (Address Before Advancing)

Any question scoring **0** or **1** in the layer corresponding to your target maturity stage is a structural gap. For example, if you are at Stage 1 and targeting Stage 2, any question in Layers 3 or 4 scoring 0 or 1 represents a structural gap you must close to advance.

**Action:** Include these in your Stage advancement plan. Assign ownership and target dates aligned with your Stage transition timeline.

### Optimization Gaps (Address for Continuous Improvement)

Any question scoring **2** in a layer where you are targeting Stage 3 is an optimization gap. The capability exists but is not yet continuously improving. This is the difference between "we do this" and "we measure and improve how we do this."

**Action:** Identify which capabilities would benefit most from automation and measurement. Prioritize based on risk and operational impact.

### Layer Imbalance

Compare percentage scores across layers. If any layer is more than 25 percentage points below the highest-scoring layer, there is a significant imbalance. For example, if Layer 1 is at 80 percent but Layer 3 is at 40 percent, the operations layer is significantly lagging governance and will limit the organization's ability to support workloads and agents effectively.

**Action:** Rebalance investment toward the lagging layer. Cross-layer dependencies mean that the weakest layer constrains overall maturity.

---

## Reassessment Cadence

**Quarterly reassessment is the recommended cadence.** This aligns with the governance review board's quarterly policy review cycle and provides sufficient time between assessments for meaningful progress.

Each reassessment should:

1. **Re-score all questions** using the same criteria. Do not carry forward previous scores without re-evaluation.
2. **Compare to the previous assessment.** Identify scores that increased, decreased, or remained flat.
3. **Investigate decreases.** A score that drops between assessments indicates regression. Determine the cause: team turnover, process atrophy, infrastructure changes, or shifting priorities.
4. **Update the gap analysis.** Reprioritize gaps based on the new scores and any changes in organizational priorities.
5. **Report to the governance review board.** Assessment results and gap analysis should be a standing agenda item for the review board.

Between quarterly assessments, use the scoring table as a living tracker. When a capability is completed or improved, update the score and note the date. This provides a continuous view of progress rather than quarterly snapshots.

---

## Assessment Tips

- **Involve multiple perspectives.** A single person's assessment will reflect their blind spots. Include representatives from engineering, security, operations, and governance.
- **Use evidence, not memory.** For each question, ask "Can I point to the artifact, log, dashboard, or document that proves this?" If you cannot, the score is likely lower than you think.
- **Score the worst case, not the best case.** If three out of five teams have API keys through self-service but two still require manual issuance, score question 3.4 as 1 (Partial), not 2 (Complete).
- **Distinguish between existence and enforcement.** A policy that exists but is not enforced scores 1 at most. A gateway that is deployed but has teams bypassing it scores 1 at most. Enforcement is what makes a capability real.
- **Do not inflate scores to reach a target stage.** An honest assessment that reveals gaps is far more valuable than an inflated assessment that hides them. The purpose of the assessment is to direct investment, not to generate a passing grade.
