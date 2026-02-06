# Stage 2: Operational

Stage 2 transforms the foundation into a production-grade operating environment. The governance and gateway layers established in Stage 1 now support a fully mature operations layer, formal workload onboarding, and the first steps of agent security. This is where the organization transitions from "we have a gateway" to "we run generative AI as a managed capability."

The primary focus at this stage is **Layer 3 (Operations)** maturing into a full observability and developer-experience stack, **Layer 4 (Workloads)** becoming active with formal onboarding and evaluation, and **Layer 5 (Agent Security)** emerging with basic registration and identity. **Layers 1 (Governance)** and **Layer 2 (Gateway)** continue to mature, shifting from initial deployment to enforcement and multi-provider resilience.

The typical duration of Stage 2 is 8 to 16 weeks. Organizations with high workload volume or complex regulatory environments should expect the longer end.

---

## Layer 1: Governance

### Entry Criteria

- All Stage 1 Layer 1 exit criteria met.
- AUP has been circulated and acknowledged by all teams using generative AI.
- At least one cycle of real-world usage data is available from the gateway to inform policy refinement.

### Capabilities at This Stage

**Policy Ratification and Scheduled Review.** The AUP moves from draft to ratified. Ratification means formal approval by the executive sponsor and acknowledgment by all teams. A review cadence is established, typically quarterly, to revisit policies in light of new usage patterns, new model capabilities, and evolving regulations. Policies are living documents, not shelf-ware.

**Governance Review Board.** A standing review board is operational with a defined charter, membership, and meeting cadence. The board reviews new model requests, evaluates exemption applications, adjudicates policy disputes, and monitors compliance metrics. Minutes are recorded. Decisions are traceable.

**Exemption Process.** A formal process exists for teams to request exemptions from governance policies. Exemptions might include: using a model not in the catalog for a specific use case, sending data above the default classification tier, or exceeding cost ceilings for a time-bound initiative. Each exemption must document the request, the risk assessment, the approver, the duration, and the conditions for revocation.

**Expanded Model Catalog.** The model catalog is expanded to include multiple models across providers. Each entry includes updated pricing, capability descriptions, approved classification tiers, and any use-case restrictions. The catalog is the single source of truth and is referenced by the gateway for routing decisions.

### Exit Criteria

- AUP is ratified with executive sign-off and team acknowledgment on record.
- Review board has met at least twice and has documented minutes.
- Exemption process is documented, has been used at least once, and the outcome is recorded.
- Model catalog contains models from at least two providers with complete metadata.

---

## Layer 2: Gateway

### Entry Criteria

- All Stage 1 Layer 2 exit criteria met.
- At least two model providers have active accounts with API credentials.
- Data loss prevention requirements documented by the governance team.

### Capabilities at This Stage

**Multi-Provider Routing.** The gateway can route requests to multiple model providers based on defined rules. Routing decisions may consider model capability, cost, latency, or team-specific configuration. Teams can specify a preferred model, and the gateway can substitute an equivalent model during outages or budget constraints.

**Failover Configuration.** If a primary model provider is unavailable, the gateway automatically routes to a configured fallback. Failover behavior is documented and tested. Teams are notified when failover activates. Failover does not bypass governance policies: the fallback model must also be in the approved catalog.

**Data Loss Prevention.** Active content inspection on requests passing through the gateway. At minimum, the gateway detects and blocks requests containing patterns that match Restricted-tier data as defined by the classification scheme. This includes patterns such as structured personal identifiers, financial account numbers, and any organization-specific sensitive patterns. Blocked requests are logged with the reason and the originating identity.

**Per-Team Budget Enforcement.** Cost ceilings are enforced at the team or application level, not just the organization level. Each team has an allocated budget. When a team approaches its limit, the gateway can warn, throttle, or hard-stop based on configured policy. Budget consumption is visible to team leads in near-real-time.

### Exit Criteria

- At least two model providers are configured and traffic has been routed to both.
- Failover has been tested (either through a real outage or a simulated one) and recovery was successful.
- Data loss prevention is active, has blocked at least one request (real or test), and the block is logged.
- Per-team budgets are configured for all active teams and at least one budget alert has fired.

---

## Layer 3: Operations

### Entry Criteria

- All Stage 1 Layer 3 exit criteria met.
- Gateway is producing sufficient telemetry volume to justify a full observability stack.
- Developer-experience requirements gathered from at least three active teams.

### Capabilities at This Stage

**Full Observability Stack.** Operations moves from basic logging to comprehensive observability. This includes structured logs, metrics (request volume, latency percentiles, error rates, token usage), and traces that can follow a request from client through the gateway to the model provider and back. Dashboards are available for operations, security, and leadership audiences with different views appropriate to each.

**Cost Dashboards Per Team.** Every team with an active budget allocation has a cost dashboard showing spend over time, broken down by model, use case, and request volume. Dashboards update at least daily. Trend lines and forecasts are available to help teams anticipate budget exhaustion. Finance stakeholders have access to an aggregate view across all teams.

**Security Operations Review.** A weekly review of blocked requests, data loss prevention triggers, authentication failures, and anomalous usage patterns. The security team examines events from the past week, investigates flagged items, and escalates confirmed incidents through the organization's incident response process. Findings inform governance policy refinements.

**Developer Self-Service.** Developers can onboard themselves to the gateway with minimal friction. This includes: requesting and receiving API keys through a self-service portal or automated workflow, accessing documentation for the gateway and approved models, viewing their team's usage and cost data, and submitting support requests. The goal is to eliminate the operations team as a bottleneck for routine developer needs.

### Exit Criteria

- Observability dashboards are live and used by at least three distinct audiences (engineering, security, leadership).
- Cost dashboards are available for every active team and data matches provider invoices within 5 percent tolerance.
- At least four consecutive weekly security reviews have been conducted with documented outcomes.
- Developer self-service for API key issuance is operational and at least 80 percent of new key requests are fulfilled without manual intervention.

---

## Layer 4: Workloads

### Entry Criteria

- All Stage 2 Layer 3 exit criteria met. Workloads require observability to be evaluated and monitored.
- Governance review board has approved the workload onboarding process.
- At least one team has a use case ready for formal onboarding.

### Capabilities at This Stage

**Workload Brief Template.** Every workload seeking production deployment must complete a workload brief. The brief captures: the business objective, the model or models used, the data classification of inputs and outputs, success metrics, latency and throughput requirements, rollback criteria, and the responsible team. The brief is reviewed by the governance board or a delegated approver before the workload proceeds to evaluation.

**Golden Datasets.** Each active workload maintains a golden dataset: a curated set of inputs with expected outputs or quality criteria. Golden datasets are used to evaluate model performance before deployment and to detect regression when models are updated. At Stage 2, golden datasets should contain at least 25 examples per workload, with plans to expand. Golden datasets are versioned and stored alongside the workload configuration.

**CI-Based Evaluation.** Workloads run automated evaluation against their golden datasets as part of the continuous integration pipeline. Every code change, prompt modification, or model update triggers evaluation. Results are compared against baseline scores. Regressions beyond a defined threshold block deployment. Evaluation metrics are workload-specific but must include at minimum: accuracy or quality score, latency, and cost per request.

**Adversarial Test Suite.** Critical workloads have adversarial test suites covering prompt injection, jailbreak attempts, data exfiltration probes, and domain-specific misuse scenarios. Adversarial tests run in CI alongside golden dataset evaluation. Failures block deployment. At Stage 2, adversarial coverage is required for workloads handling Restricted-tier or Internal-tier data. Coverage for other workloads is recommended but not mandatory.

### Exit Criteria

- Workload brief template is in use and at least three workloads have completed briefs reviewed by the governance board.
- Every active workload has a golden dataset with at least 25 examples.
- CI-based evaluation is running for every active workload and has blocked at least one deployment due to regression.
- Adversarial test suites exist for all workloads handling Internal-tier or higher data, and the suites run in CI.

---

## Layer 5: Agent Security

### Entry Criteria

- All Stage 2 Layer 3 exit criteria met. Agent actions must be observable before agents are sanctioned.
- Governance review board has defined an initial agent policy covering permitted capabilities and boundaries.
- At least one team has an agent use case ready for registration.

### Capabilities at This Stage

**Agent Registry.** A centralized registry of all sanctioned agents. Each entry includes: agent identifier, owning team, declared capabilities, permitted tools, data classification scope, and approval status. Agents not in the registry are unsanctioned. The registry is the authoritative source for agent identity and permissions.

**Basic Agent Identity Model.** Every registered agent has a unique identity that is authenticated through the gateway. Agent identity is distinct from the identity of the developer or team that created the agent. When an agent makes a model request, the gateway log captures both the agent identity and the owning team identity. This enables audit trails that distinguish between human-initiated and agent-initiated requests.

**Audit Logging for Agent Actions.** Every action taken by a registered agent is logged. This includes model requests, tool invocations, data access, and external communications. Logs capture what the agent did, when, with what inputs, and what the outcome was. Audit logs are accessible to the security operations team and are included in the weekly security review.

### Exit Criteria

- Agent registry exists and contains at least one registered agent with complete metadata.
- Registered agents authenticate through the gateway with their own identity, and logs distinguish agent requests from human requests.
- Audit logging for agent actions is operational, and agent actions have been reviewed in at least one security operations review cycle.

---

## Advancing to Stage 3

An organization is ready to advance to Stage 3 when **all exit criteria for all five layers at Stage 2 are met**. Stage 3 represents continuous optimization, and it requires a fully operational foundation across every layer.

The transition to Stage 3 should be validated by the governance review board. The board should confirm that all exit criteria are met, that the teams have the capacity and maturity to adopt automated optimization practices, and that the data volume is sufficient to drive data-informed decisions.

Common reasons organizations stall at Stage 2:

- **Workload evaluation gaps.** Teams deploy workloads without golden datasets or adversarial tests because evaluation is seen as overhead. Without evaluation, model upgrades and prompt changes cannot be validated, and quality degrades silently.
- **Agent sprawl.** Agents are deployed outside the registry, creating unobserved autonomous systems. If the gateway cannot see agent traffic, agent security is theater.
- **Operations bottleneck.** Developer self-service is incomplete, and the operations team becomes a gatekeeper rather than an enabler. Teams route around the bottleneck, undermining the centralized model.
- **Security review fatigue.** Weekly security reviews become rote or are skipped. Without consistent review, data loss prevention triggers and anomalous patterns go uninvestigated.

Address these blockers before attempting to advance.
