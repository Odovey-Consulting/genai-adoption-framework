# Operations Decision Guide

> Layer 3 of the GenAI Adoption Framework

Operating a generative AI platform requires a series of design decisions that shape daily workflows, team responsibilities, and organizational dynamics. This guide presents five key decisions, the options available, criteria for evaluating them, and the tradeoffs each option introduces.

---

## Decision 1: Which Metrics to Prioritize in the First Dashboard

**Context:** The platform can emit dozens of metrics from day one. Building a dashboard that displays all of them produces noise. Building one that displays too few leaves operational blind spots. The first dashboard sets the tone for operational culture: it signals what the platform team considers important.

**Options:**

- **Option A: Cost-centric.** Lead with cost per request, daily spend by team, and budget burn rate. Add latency and errors as secondary panels.
- **Option B: Reliability-centric.** Lead with error rates, latency percentiles (p50, p95, p99), time to first token, and upstream provider availability. Add cost as a secondary panel.
- **Option C: Adoption-centric.** Lead with active teams, request volume trends, onboarding funnel metrics, and time to first successful request. Add cost and reliability as secondary panels.

**Evaluation Criteria:**

| Criterion | Cost-centric | Reliability-centric | Adoption-centric |
|---|---|---|---|
| Addresses the most likely early concern | High (if leadership is cost-sensitive) | High (if reliability is the primary SLA) | High (if platform is new and needs traction) |
| Actionability for the platform team | Medium (cost issues often require business decisions) | High (reliability issues are directly addressable) | Medium (adoption issues may require organizational changes) |
| Stakeholder alignment | Finance and leadership | Engineering and SRE teams | Product and platform leadership |

**Tradeoffs:** Cost-centric dashboards satisfy finance but may alarm leadership before usage patterns stabilize. Reliability-centric dashboards serve the on-call rotation but do not answer the question leadership will ask first: "how much does this cost?" Adoption-centric dashboards demonstrate platform value but may defer early detection of cost or reliability problems.

**Recommendation:** Start with a reliability-centric dashboard because the platform team can act on those signals immediately. Add a cost summary panel from day one, even if it is a single number. Build the adoption dashboard within the first month, once there is enough data to show meaningful trends.

---

## Decision 2: Alert Threshold Calibration

**Context:** Every alert threshold is a tradeoff between sensitivity and noise. Thresholds that are too tight generate false alarms that train teams to ignore alerts. Thresholds that are too loose miss real incidents until they escalate. Generative AI platforms have additional complexity because "normal" usage patterns are volatile in the early months.

**Options:**

- **Option A: Tight thresholds, tune down over time.** Start with aggressive thresholds and relax them as baseline patterns emerge. Accept higher initial alert volume.
- **Option B: Loose thresholds, tune up over time.** Start with conservative thresholds and tighten them as confidence in normal ranges increases. Accept the risk of missing early incidents.
- **Option C: No static thresholds; anomaly detection only.** Use statistical anomaly detection from the start, alerting only when metrics deviate significantly from their rolling baseline.

**Evaluation Criteria:**

| Criterion | Tight thresholds | Loose thresholds | Anomaly detection |
|---|---|---|---|
| Risk of alert fatigue | High in early weeks | Low | Low, if model is well-calibrated |
| Risk of missed incidents | Low | Moderate in early weeks | Depends on baseline quality |
| Implementation effort | Low (static values) | Low (static values) | High (requires baseline data and tuning) |
| Appropriateness for early-stage platform | Moderate (noisy but safe) | Moderate (quiet but risky) | Low (insufficient baseline data) |

**Tradeoffs:** Tight thresholds are safer but risk creating alert fatigue during the first weeks when the team's tolerance is lowest and the platform's credibility is being established. Loose thresholds are quieter but may allow the first real incident to go unnoticed, which damages trust in the alerting system. Anomaly detection is elegant but requires a stable baseline that does not exist in the first weeks of operation.

**Recommendation:** Start with moderately tight static thresholds for critical metrics (error rate above 5 percent, latency p95 above a defined ceiling, cost burn rate exceeding 120 percent of daily budget). Use loose thresholds for informational metrics. Plan a threshold review after 30 days of production data. Introduce anomaly detection as a supplementary layer after 60 to 90 days, once baselines are stable.

---

## Decision 3: Cost Governance Model

**Context:** Someone must own the budget for generative AI spend. The ownership model determines who makes allocation decisions, who receives cost alerts, and who bears accountability for overruns. The wrong model creates either a bottleneck (all decisions flow through one team) or a vacuum (nobody owns the budget and overruns are discovered retroactively).

**Options:**

- **Option A: Platform team-owned.** The platform team holds the total budget and allocates quotas to consuming teams. The platform team approves budget increases and manages the upstream provider relationship.
- **Option B: Business unit-owned.** Each business unit has its own budget for generative AI usage. The platform team provides tooling and reporting but does not control spending decisions.
- **Option C: Hybrid.** The platform team owns a shared infrastructure budget (base platform costs, shared caching, tooling). Business units own their usage budgets and make their own allocation decisions within guidelines set by the platform team.

**Evaluation Criteria:**

| Criterion | Platform-owned | Business unit-owned | Hybrid |
|---|---|---|---|
| Budget accountability clarity | Clear (one owner) | Clear (distributed owners) | Moderate (split ownership) |
| Platform team bottleneck risk | High | Low | Low |
| Business unit autonomy | Low | High | High |
| Cross-organization cost optimization | Easy (centralized view) | Difficult (fragmented data) | Moderate (requires aggregation) |

**Tradeoffs:** Platform-owned budgets give central visibility and control but turn the platform team into a procurement bottleneck. Every budget increase request flows through them, slowing down teams and creating resentment. Business unit-owned budgets maximize autonomy but fragment cost data and make cross-organization optimization difficult. The platform team cannot negotiate volume commitments without aggregated demand signals. Hybrid models balance these concerns but introduce complexity in defining what falls under "shared infrastructure" versus "usage."

**Recommendation:** Adopt the hybrid model. The platform team should own shared infrastructure costs and provide the tooling for cost visibility. Business units should own their usage budgets with guardrails (rate limits, budget ceilings) enforced by the platform. The platform team aggregates demand data for capacity planning and upstream negotiations but does not approve individual budget requests.

---

## Decision 4: Developer Support Model

**Context:** Developers using the platform will have questions, encounter errors, and need guidance. The support model determines how quickly they get help, who provides it, and how knowledge is retained. The wrong model either starves developers of support or consumes the platform team's capacity.

**Options:**

- **Option A: Dedicated support channel.** A persistent messaging channel staffed by the platform team during business hours. Questions are answered asynchronously with a target response time.
- **Option B: Scheduled office hours.** The platform team holds regularly scheduled sessions (two to three times per week) where developers can ask questions synchronously. Between sessions, developers rely on documentation and runbooks.
- **Option C: Embedded engineers.** Platform team members are embedded in consuming teams for a defined period during onboarding. They provide hands-on guidance, build initial integrations, and transfer knowledge before rotating out.

**Evaluation Criteria:**

| Criterion | Dedicated channel | Office hours | Embedded engineers |
|---|---|---|---|
| Response latency | Low (hours) | Moderate (next session) | Very low (immediate) |
| Platform team capacity cost | Moderate (ongoing) | Low (bounded time) | High (dedicated headcount) |
| Knowledge transfer quality | Low (transactional answers) | Moderate (discussion format) | High (hands-on pairing) |
| Scalability with team count | High | High | Low (linear headcount scaling) |

**Tradeoffs:** A dedicated channel provides fast answers but creates a dependency where developers ask before reading documentation. It also requires continuous platform team attention. Office hours are capacity-efficient but frustrate developers who hit a blocking issue on a day without a session. Embedded engineers produce the best onboarding outcomes but do not scale: onboarding ten teams requires ten embedded rotations.

**Recommendation:** Layer the models based on platform maturity. Start with a dedicated support channel to build relationships and identify common questions during early adoption. Add scheduled office hours once the question volume justifies a structured format. Reserve embedded engineers for complex, high-value onboarding engagements (such as a business-critical production workload) rather than standard onboarding.

---

## Decision 5: Compliance Reporting Frequency

**Context:** Compliance evidence must be generated and reviewed at some cadence. The frequency determines how quickly policy violations are detected, how much manual effort is required, and whether compliance is a continuous posture or a periodic exercise.

**Options:**

- **Option A: Continuous.** Compliance checks run in real time or near-real time. Policy violations generate immediate alerts. Compliance dashboards reflect current state at all times.
- **Option B: Periodic.** Compliance reports are generated on a fixed schedule (weekly, monthly, or quarterly). Violations are detected and remediated in batches during the review cycle.
- **Option C: On-demand.** Compliance reports are generated when requested, typically before an audit, a regulatory deadline, or a security review. No scheduled cadence exists.

**Evaluation Criteria:**

| Criterion | Continuous | Periodic | On-demand |
|---|---|---|---|
| Violation detection speed | Immediate | Hours to weeks | Weeks to months |
| Implementation and maintenance effort | High | Moderate | Low |
| Audit readiness | Always ready | Ready at review intervals | Requires preparation time |
| Operational overhead | Moderate (alert management) | Low (batch review) | Low until audit, then high |

**Tradeoffs:** Continuous compliance provides the best security posture and audit readiness but requires investment in automated checks, alert management, and dashboard maintenance. It can also generate alert fatigue if violation thresholds are poorly calibrated. Periodic reporting is operationally efficient and aligns with many regulatory cadences but allows violations to persist between reviews. On-demand reporting minimizes ongoing effort but creates intense pressure before audits and allows violations to go undetected for extended periods.

**Recommendation:** Implement continuous monitoring for high-severity compliance requirements (data classification enforcement, access control violations, guardrail bypass attempts). Use periodic reporting (monthly) for cost governance compliance, usage policy adherence, and access matrix reviews. Reserve on-demand generation for ad hoc audit requests and stakeholder inquiries. This layered approach matches reporting effort to risk severity.
