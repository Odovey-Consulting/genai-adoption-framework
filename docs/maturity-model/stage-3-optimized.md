# Stage 3: Optimized

Stage 3 is not a destination. It is an operating mode. All five layers are fully active, and the focus shifts from building capabilities to continuously improving them through automation, measurement, and feedback loops. Manual processes that were appropriate at earlier stages are replaced by automated systems. Reactive postures become proactive. Decisions are data-driven rather than intuition-driven.

At this stage, **all five layers are primary**. Governance is automated and version-controlled. The gateway performs sophisticated multi-signal routing. Operations proactively forecasts costs and tunes guardrails. Workloads have comprehensive evaluation coverage with automated regression detection. Agent security enforces fine-grained authorization and discovers unsanctioned agents.

Stage 3 maturity is not static. Organizations continuously measure, refine, and adapt. The exit criteria for Stage 3 are not about reaching a final state but about demonstrating that the mechanisms for continuous improvement are in place and functioning.

---

## Layer 1: Governance

### Entry Criteria

- All Stage 2 Layer 1 exit criteria met.
- Governance review board has completed at least two full quarterly review cycles.
- Sufficient usage data exists (minimum one quarter of operational data) to perform data-driven policy analysis.

### Capabilities at This Stage

**Data-Driven Policy Refinement.** Governance policies are refined based on quantitative evidence from operations data, not assumptions. The review board analyzes trends in blocked requests, exemption frequency, cost patterns, and security incidents to identify policies that are too restrictive (generating excessive exemptions), too permissive (failing to prevent incidents), or misaligned with actual usage patterns. Policy changes are proposed with supporting data, reviewed, and tracked for impact after implementation.

**Automated Compliance Reporting.** Compliance status against governance policies is generated automatically from gateway and operations telemetry. Reports cover: percentage of traffic routed through the gateway, data loss prevention violation rate, budget adherence per team, model catalog compliance (requests only to approved models), and workload evaluation pass rates. Reports are generated on a defined cadence, at minimum monthly, and distributed to the review board and executive sponsor without manual assembly.

**Version-Controlled Governance Artifacts.** All governance documents, including the AUP, data classification scheme, model catalog, exemption records, and review board minutes, are stored in a version control system. Changes follow a review-and-approve workflow analogous to code review. History is immutable and auditable. Automated checks validate that governance artifacts are internally consistent: for example, that every model referenced in a workload brief exists in the model catalog, or that every exemption has an expiration date.

**Regulatory Mapping.** Governance policies are mapped to applicable regulatory requirements and industry standards. When regulations change, the governance team can trace which policies are affected and which workloads are impacted. This mapping is maintained alongside governance artifacts and reviewed at each quarterly cycle.

### Exit Criteria

- At least two policy changes have been made based on quantitative evidence from operations data, with documented rationale and measured impact.
- Compliance reports are generated automatically and have been distributed for at least two consecutive reporting periods.
- All governance artifacts are version-controlled with change history, and at least one automated consistency check is in place.
- Regulatory mapping exists and has been reviewed in the most recent quarterly cycle.

---

## Layer 2: Gateway

### Entry Criteria

- All Stage 2 Layer 2 exit criteria met.
- Traffic volume is sufficient to justify routing optimization (minimum of multiple teams with active workloads).
- Regional deployment requirements identified, if applicable.

### Capabilities at This Stage

**Multi-Signal Routing.** The gateway makes routing decisions based on multiple signals simultaneously: cost per token, latency requirements, model capability matching, provider availability, and team-specific preferences. Routing logic is configurable per workload or per team. For example, a latency-sensitive workload may route to the fastest provider regardless of cost, while a batch processing workload may route to the cheapest provider regardless of latency. Routing rules are versioned and auditable.

**Automated Failover Testing.** Failover is not just configured but regularly tested. Automated tests simulate provider outages on a scheduled cadence, at minimum monthly, and verify that traffic reroutes correctly, that fallback models are functional, and that recovery occurs within defined time bounds. Test results are logged and reviewed. Failover that has never been tested since configuration is not trustworthy.

**Regional Deployment.** For organizations with data residency requirements or geographically distributed teams, the gateway is deployed across multiple regions. Routing respects data residency policies: requests involving data classified under specific regional regulations are routed only to gateway instances and model providers that satisfy those requirements. Regional deployment architecture is documented and validated.

**Tiered Prompt Logging.** Prompt and response content logging is tiered by data classification. Requests involving Public-tier data have full content logging enabled for debugging and evaluation. Requests involving Internal-tier data have content logged but with restricted access controls. Requests involving Restricted-tier data have content redacted or excluded from logs, with only metadata (timestamp, identity, model, token count) retained. Logging tiers are enforced automatically by the gateway based on the data classification declared by the workload or detected by the data loss prevention system.

**Cache and Deduplication.** The gateway implements semantic or exact-match caching for repeated requests where appropriate. Caching reduces cost and latency for workloads with predictable or repetitive request patterns. Cache behavior is configurable per workload. Cache entries respect data classification: Restricted-tier content is never cached.

### Exit Criteria

- Multi-signal routing is active and at least two workloads have distinct routing configurations based on different optimization criteria.
- Automated failover tests have run for at least two consecutive months with documented results.
- Regional deployment is operational (if applicable) or documented as not required with rationale.
- Tiered prompt logging is enforced and at least one audit has verified that Restricted-tier content is not present in accessible logs.

---

## Layer 3: Operations

### Entry Criteria

- All Stage 2 Layer 3 exit criteria met.
- Sufficient historical data exists (minimum three months) to build forecasting models and baseline guardrail performance.
- Developer satisfaction feedback has been collected at least once.

### Capabilities at This Stage

**Automated Guardrail Tuning.** Guardrails, including data loss prevention rules, rate limits, token limits, and cost thresholds, are tuned based on observed traffic patterns rather than static configuration. The system identifies guardrails that are firing excessively (indicating they may be too aggressive) or never firing (indicating they may be misconfigured or irrelevant). Tuning recommendations are generated automatically and presented to the operations team for review. Fully automated tuning, where guardrail parameters adjust without human approval, is permitted only for non-security guardrails such as rate limits and token limits.

**Proactive Cost Forecasting.** Cost projections are generated based on historical usage trends, planned workload changes, and model pricing updates. Forecasts project at minimum 30, 60, and 90 days forward. Finance stakeholders receive forecasts alongside actuals. The operations team uses forecasts to identify teams trending toward budget exhaustion and to recommend capacity or budget adjustments before breaches occur, not after.

**Developer Net Promoter Score Tracking.** Developer satisfaction with the generative AI platform is measured on a regular cadence, at minimum quarterly, using a structured survey. The survey covers: ease of onboarding, API key issuance speed, documentation quality, gateway reliability, dashboard usefulness, and support responsiveness. Results are tracked over time and used to prioritize platform improvements. A declining satisfaction score triggers investigation and remediation.

**Automated Compliance Reporting.** Compliance reports required by the governance layer are generated automatically from operations telemetry. No manual data gathering is required. Reports are version-controlled and distributed on schedule. Discrepancies between reported data and actuals are flagged automatically.

**Incident Response Automation.** Common incident types, such as provider outages, budget breaches, and data loss prevention spikes, have automated runbooks. Runbooks execute initial diagnosis and remediation steps automatically, escalating to human responders only when automated steps are insufficient. Mean time to detection and mean time to resolution are tracked and targeted for continuous reduction.

### Exit Criteria

- At least one guardrail has been tuned based on automated analysis, and the impact of the tuning is documented.
- Cost forecasts have been generated for at least two consecutive months and compared against actuals. Forecast accuracy is within 15 percent.
- Developer satisfaction has been surveyed at least once and results have been reviewed by the operations team.
- Compliance reports are generated without manual intervention for at least two consecutive reporting periods.
- At least one incident type has an automated runbook that has been triggered (real or test) with documented results.

---

## Layer 4: Workloads

### Entry Criteria

- All Stage 2 Layer 4 exit criteria met.
- At least three workloads are actively running with golden datasets and CI-based evaluation.
- Historical evaluation data exists to baseline model performance across workloads.

### Capabilities at This Stage

**Comprehensive Golden Datasets.** Every active workload maintains a golden dataset with at least 100 examples. Datasets are versioned, reviewed for quality on a scheduled cadence, and expanded as edge cases are discovered in production. Dataset coverage is tracked: the percentage of known input categories that are represented in the golden dataset is measured and targeted for improvement.

**Adversarial Tests in CI for All Workloads.** Adversarial testing is no longer limited to workloads handling sensitive data. Every workload, regardless of data classification, has adversarial test coverage running in CI. Adversarial suites are maintained as living test libraries that evolve as new attack patterns are discovered. Test coverage metrics are tracked and reported alongside functional evaluation metrics.

**Production Quality Sampling.** A percentage of production requests and responses are sampled and evaluated against quality criteria. Sampling is automated and runs continuously. Evaluation may be automated (using scoring models or heuristic rules) or manual (using human review queues) depending on the workload. Sampling rates are configurable per workload. Quality trends are tracked and regressions trigger alerts.

**Automated Model Upgrade Re-Evaluation.** When a model provider releases an updated version, the evaluation pipeline automatically re-runs the golden dataset and adversarial suite against the new version. Results are compared to the current production baseline. If the new version meets or exceeds baseline performance, it is recommended for promotion. If it regresses, it is flagged. This removes the manual effort of evaluating model updates and ensures that upgrades are validated before deployment.

**Workload Lifecycle Management.** Workloads have defined lifecycle states: development, staging, production, deprecated, and decommissioned. Transitions between states require defined criteria and approvals. Deprecated workloads have sunset dates and migration plans. Decommissioned workloads have their resources reclaimed and their golden datasets archived. No workload runs indefinitely without review.

### Exit Criteria

- Every active workload has a golden dataset with at least 100 examples, and dataset coverage metrics are tracked.
- Adversarial test suites run in CI for all active workloads, not only those handling sensitive data.
- Production quality sampling is active for all production workloads and quality trends are visible on dashboards.
- At least one model upgrade has been evaluated automatically and the recommendation (promote or flag) is documented.
- Workload lifecycle states are defined and at least one workload has transitioned through a lifecycle stage with documented approval.

---

## Layer 5: Agent Security

### Entry Criteria

- All Stage 2 Layer 5 exit criteria met.
- Agent registry contains at least three registered agents.
- Security operations team has reviewed agent audit logs for at least two consecutive review cycles.

### Capabilities at This Stage

**Tool and Parameter Authorization.** Agent permissions are defined not only at the level of which tools an agent may invoke but also at the level of which parameters it may use. An agent authorized to query a database may be restricted to specific tables or specific query patterns. An agent authorized to send messages may be restricted to specific recipients or channels. Authorization policies are enforced at the gateway or at a dedicated agent authorization layer. Violations are logged and blocked.

**Shadow Agent Discovery.** Active mechanisms detect agents operating outside the registry. Detection methods may include: traffic analysis to identify non-human request patterns, anomaly detection on request volume and timing, and integration with endpoint management to detect unauthorized agent processes. Discovered shadow agents are flagged, investigated, and either registered or terminated.

**Delegation Chain Enforcement.** When an agent invokes another agent or delegates a task to a sub-agent, the delegation chain is recorded and enforced. Delegation depth limits are configured. Every agent in the chain must be registered and authorized for the delegated action. Delegation chains are visible in audit logs and can be traced end-to-end. Circular delegation is detected and blocked.

**Mutual Authentication for Agent-to-Agent Communication.** When agents communicate with each other, both parties authenticate. One-way authentication is insufficient because a malicious agent could impersonate a legitimate one. Mutual authentication ensures that both the requesting agent and the responding agent verify each other's identity before exchanging data or delegating actions.

**Quarterly Agent Access Reviews.** Agent permissions are reviewed on a quarterly cadence. Reviews verify that each agent's declared capabilities and tool authorizations still align with its current function. Agents that are no longer active are deregistered. Agents whose scope has changed have their permissions updated. Reviews are documented and tracked.

**Agent Behavioral Baselines.** Baseline behavioral profiles are established for each registered agent based on historical activity: typical request volume, timing patterns, tools invoked, and data accessed. Deviations from baseline trigger alerts for security review. Baselines are updated as agent behavior evolves, but sudden changes are always investigated before the baseline is adjusted.

### Exit Criteria

- Tool and parameter-level authorization is enforced for all registered agents, and at least one parameter-level restriction is in place.
- Shadow agent discovery has been run and results have been reviewed. If shadow agents were found, they have been resolved.
- Delegation chains are enforced with depth limits, and at least one multi-agent delegation has been traced end-to-end in audit logs.
- Mutual authentication is in place for all agent-to-agent communication.
- At least one quarterly agent access review has been completed with documented outcomes.
- Behavioral baselines exist for all agents with more than 30 days of activity, and at least one deviation alert has been investigated.

---

## Sustaining Stage 3

Stage 3 is sustained through continuous measurement and adaptation. The following practices prevent regression:

**Quarterly Maturity Reassessment.** Run the full maturity assessment every quarter. Track scores over time. Investigate any score decreases. Maturity is not a ratchet; it can degrade when teams change, priorities shift, or processes atrophy.

**Annual Framework Review.** Review the entire adoption framework annually. Generative AI capabilities evolve rapidly. Models that did not exist at the time of initial adoption may change what is possible and what is risky. Governance policies, gateway configurations, and agent security models should be evaluated against the current state of the technology.

**Feedback Loops.** Every automated system, from guardrail tuning to cost forecasting to agent discovery, must have a feedback loop. Automated recommendations must be reviewed. Automated actions must be auditable. Automation without oversight is not optimization; it is abdication.

**Knowledge Sharing.** Lessons learned, incident reports, evaluation results, and governance decisions are shared across teams. A centralized knowledge base ensures that institutional learning is preserved and accessible. New teams onboarding to the platform can learn from the experience of early adopters rather than repeating their mistakes.
