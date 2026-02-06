# Stage 1: Foundation

Stage 1 is where every organization begins. The goal is to establish the governance and gateway layers that all future capabilities depend on. Without a ratified acceptable use policy, traffic routing through a centralized gateway, and a defined data classification scheme, nothing built on top will be stable. Stage 1 is not optional and it cannot be skipped.

The primary focus at this stage is **Layer 1 (Governance)** and **Layer 2 (Gateway)**. **Layer 3 (Operations)** exists in minimal form: basic logging, cost tracking, and at least one alert. **Layers 4 (Workloads)** and **5 (Agent Security)** are **not yet active**. Attempting to onboard workloads or register agents before governance and gateway are in place creates unsupported deployments that will need to be retroactively brought into compliance.

The typical duration of Stage 1 is 4 to 8 weeks for organizations with executive sponsorship and a dedicated team. Organizations without either should expect longer timelines and should address those gaps as prerequisites.

---

## Layer 1: Governance

### Entry Criteria

- Executive sponsor identified and committed to the initiative.
- A cross-functional working group formed, including at minimum: engineering, security, legal, and finance representatives.
- Inventory of known generative AI usage across the organization completed, even if informal. Shadow usage counts.

### Capabilities at This Stage

**Acceptable Use Policy (AUP).** Draft and circulate an AUP that defines who may use generative AI, for what purposes, with what data, and under what constraints. The AUP must be specific enough to be enforceable. "Use AI responsibly" is not a policy. "No customer personally identifiable information may be included in prompts sent to external model providers" is a policy. The AUP should address at minimum: permitted use cases, prohibited use cases, data handling requirements, intellectual property considerations, and human oversight requirements.

**Data Classification Scheme.** Define a minimum of three data classification tiers. A common structure is: Public (no restrictions on model input), Internal (may be sent to approved models under logging), and Restricted (must not be sent to external model providers without explicit exemption). Every dataset and data source used in prompts or fine-tuning must be classifiable under this scheme.

**Model Catalog.** Create a catalog of approved models. At Stage 1, this may contain only one or two entries. Each entry must document: the model identifier, the provider, the data classification tier it is approved for, cost per unit (token, request, or minute), and any use-case restrictions. The catalog is the authoritative source for what models teams are permitted to use.

**Cost Ceilings.** Set organization-wide cost ceilings for generative AI spend. At Stage 1, these may be simple monthly caps. The ceiling must be monitored, and breach of the ceiling must trigger a defined response, whether that is an alert, a throttle, or a hard stop.

### Exit Criteria

- AUP is drafted, reviewed by legal, and circulated to all teams that will use generative AI.
- Data classification scheme is documented with at least three tiers and has been applied to at least the initial set of data sources.
- Model catalog contains at least one approved model with full metadata.
- Cost ceiling is set and the mechanism to enforce it (alert or hard stop) is defined.

---

## Layer 2: Gateway

### Entry Criteria

- Layer 1 exit criteria met. The gateway must enforce policies that exist; deploying a gateway before governance is defined means the gateway has no rules to enforce.
- Infrastructure provisioning authority granted. The team must be able to deploy and operate the gateway in the organization's environment.
- At least one model provider account established with API credentials.

### Capabilities at This Stage

**Centralized Routing.** Deploy a gateway that routes all sanctioned generative AI traffic. Every model request from every team must pass through this gateway. Direct API calls to model providers that bypass the gateway are unsanctioned and must be detected and remediated. At Stage 1, this may be a single-region deployment handling a single model provider. That is sufficient.

**Authentication.** Every request through the gateway must be authenticated. At minimum, this means API key-based authentication with keys issued per team or per application. Keys must be revocable. Anonymous or shared-credential access must not be permitted.

**Logging.** Every request and response passing through the gateway must be logged. At Stage 1, logs must capture at minimum: timestamp, authenticated identity, model targeted, token count (input and output), and latency. Prompt and response content logging is recommended but may be deferred to Stage 2 if data classification concerns require additional safeguards. Logs must be stored in a location accessible to the operations team.

**Single Provider Configuration.** At least one model provider must be configured and operational through the gateway. Requests must successfully route from authenticated clients through the gateway to the provider and back. End-to-end functionality must be validated.

### Exit Criteria

- Gateway is deployed and handling all sanctioned model traffic. No approved teams are bypassing the gateway.
- Authentication is enforced on every request. API keys are issued per team or per application.
- Request logging is operational and logs are accessible.
- At least one model provider is configured and validated end-to-end.

---

## Layer 3: Operations

### Entry Criteria

- Layer 2 exit criteria met. Operations observability depends on data flowing through the gateway. Without the gateway, there is nothing to observe.

### Capabilities at This Stage

Operations at Stage 1 is intentionally minimal. The goal is not a full observability stack but rather proof that the foundational telemetry is flowing and that basic anomalies can be detected.

**Request Logging Visibility.** Logs generated by the gateway are accessible to the operations team in a queryable format. The team can answer basic questions: How many requests were made today? Which teams are making requests? What is the average latency?

**Cost Tracking.** Cost data is being captured per request based on token usage and the pricing metadata in the model catalog. A daily or weekly cost report can be generated, even if manually. The operations team can answer: What did we spend this week? Which team spent the most?

**At Least One Alert.** At least one automated alert is configured. Recommended first alerts: cost exceeding a daily threshold, error rate exceeding a percentage threshold, or a single request exceeding a token count threshold. The alert must notify a defined recipient. An alert that fires into the void does not count.

### Exit Criteria

- Request logs are queryable and the operations team has accessed them to answer at least one real question.
- Cost tracking is producing data that matches (within reasonable tolerance) the actual provider invoices.
- At least one alert is configured, has a defined recipient, and has been tested (either by a real event or a synthetic trigger).

---

## Layer 4: Workloads

### Entry Criteria

Not applicable at Stage 1.

### Capabilities at This Stage

**Layer 4 is not active at Stage 1.** Teams may be experimenting with generative AI, but formal workload onboarding, evaluation pipelines, golden datasets, and adversarial testing are Stage 2 concerns. Premature investment in workload infrastructure before governance, gateway, and basic operations are in place leads to workloads that cannot be governed, observed, or evaluated consistently.

Teams that are eager to begin workload development should be directed to use the gateway for their experimentation, which ensures their traffic is logged and governed, but formal workload onboarding processes are deferred.

### Exit Criteria

Not applicable at Stage 1.

---

## Layer 5: Agent Security

### Entry Criteria

Not applicable at Stage 1.

### Capabilities at This Stage

**Layer 5 is not active at Stage 1.** Autonomous agents introduce security concerns, such as tool authorization, delegation chains, and identity propagation, that require a mature governance and operations foundation to address. Deploying agents before the gateway can observe their traffic and before governance can define their permitted behaviors creates uncontrolled autonomous systems.

Any autonomous agent usage at Stage 1 should be treated as unsanctioned and flagged for the governance working group to evaluate as part of Stage 2 planning.

### Exit Criteria

Not applicable at Stage 1.

---

## Advancing to Stage 2

An organization is ready to advance to Stage 2 when **all exit criteria for Layers 1, 2, and 3 are met**. Partial completion is not sufficient. If the AUP is drafted but the gateway is not deployed, the organization remains at Stage 1. If the gateway is deployed but cost tracking is not producing accurate data, the organization remains at Stage 1.

The transition to Stage 2 should be a deliberate decision, reviewed by the governance working group, and documented. It is not an automatic promotion. The working group should verify exit criteria completion, identify any residual risks, and confirm that the team capacity exists to take on Stage 2 scope.

Common reasons organizations stall at Stage 1:

- **No executive sponsor.** Governance policies without executive backing are ignored.
- **Gateway bypass.** Teams continue calling model providers directly, undermining centralized observability and policy enforcement.
- **Incomplete data classification.** Without clear tiers, teams cannot determine what data is permissible in prompts, and they either avoid usage entirely or use everything recklessly.
- **Cost ceilings without enforcement.** A cost ceiling that exists only on paper provides no protection against surprise invoices.

Address these blockers before attempting to advance.
