# Operations Capabilities

> Layer 3 of the GenAI Adoption Framework

This document defines the operational capabilities required to run a generative AI platform. Capabilities are organized by functional area. Each capability includes a maturity stage indicator to help teams prioritize what to build first and what to defer until the platform matures.

**Maturity stages:**
- **Foundation** — required before production traffic. Non-negotiable for launch.
- **Operational** — required within the first quarter of production operation. Necessary for sustainable operations at moderate scale.
- **Optimized** — required when the platform supports multiple business units, high request volumes, or regulated workloads. Enables optimization and continuous improvement.

---

## Observability

Observability is the foundation of all other operational capabilities. Without accurate, timely, and granular telemetry, cost governance is guesswork, security operations are reactive, and developer enablement is uninformed.

| Capability | Description | Maturity Stage |
|---|---|---|
| **Token throughput** | Track input and output token counts per request, per team, per model, and per environment. Token volume is the primary cost driver and must be the most granular metric available. | Foundation |
| **Cost per request** | Calculate and record the estimated cost of each request based on token counts and the pricing model of the upstream provider. Attribute costs to the requesting team or project. | Foundation |
| **Latency (time to first token)** | Measure the elapsed time from request submission to the first token of the response. Time to first token is the user-perceived latency and the most meaningful performance indicator for streaming workloads. | Foundation |
| **Error rates** | Track error rates by type: upstream provider errors, gateway errors, guardrail rejections, timeout errors, and client errors. Distinguish between transient and persistent failures. | Foundation |
| **Guardrail trigger rates** | Record the frequency and type of guardrail activations, including input blocks, output blocks, and modified responses. Segment by team, use case, and guardrail rule. | Operational |
| **Prompt length distributions** | Track the statistical distribution of prompt lengths (in tokens) across teams and use cases. Outliers in prompt length often indicate misuse, inefficient prompt design, or unexpected document injection. | Operational |
| **Cache hit rates** | Measure the proportion of requests served from semantic or exact-match caches. Cache hit rate directly impacts cost efficiency and latency. Low hit rates may indicate poor cache key design or highly variable prompts. | Operational |
| **Model response quality signals** | Capture downstream quality indicators such as user feedback scores, regeneration rates, and task completion rates. These signals are lagging but essential for detecting model drift or degradation. | Optimized |
| **Retrieval pipeline metrics** | For retrieval-augmented workloads, track retrieval latency, document relevance scores, chunk counts, and embedding lookup times. These metrics isolate performance issues in the retrieval layer from model inference issues. | Optimized |

---

## Alerting

Alerting translates observability data into operational action. Poorly configured alerting is worse than no alerting because it trains teams to ignore signals.

| Capability | Description | Maturity Stage |
|---|---|---|
| **Configurable thresholds** | Allow threshold configuration for all key metrics: error rate spikes, latency degradation, cost burn rate, and guardrail trigger rate anomalies. Thresholds must be adjustable per team or use case. | Foundation |
| **Escalation paths** | Define escalation sequences for unacknowledged alerts. First responder, secondary on-call, and management escalation should be documented and automated. | Foundation |
| **Team-specific routing** | Route alerts to the team responsible for the affected scope. A cost alert for Team A should not wake up Team B. Routing must align with the cost attribution and access control model. | Operational |
| **Anomaly-based alerting** | Supplement static thresholds with anomaly detection that identifies unusual patterns in token volume, cost, or error rates relative to historical baselines. | Optimized |
| **Alert correlation** | Correlate related alerts to reduce noise. A single upstream provider outage should produce one incident, not fifty alerts from fifty downstream consumers. | Optimized |

---

## Cost Governance

Cost governance ensures that generative AI spending is intentional, attributable, and controlled. It bridges finance, engineering, and business leadership.

| Capability | Description | Maturity Stage |
|---|---|---|
| **Budget alerts** | Notify budget owners when spending reaches configurable percentage thresholds of their allocated budget (commonly 50%, 75%, 90%, and 100%). | Foundation |
| **Overspend workflows** | Define automated actions when a budget is exceeded: alert-only, throttle requests, degrade to a less expensive model, or hard-stop all requests. The response should be configurable per team. | Operational |
| **Chargeback reporting** | Generate periodic reports attributing costs to teams, projects, or business units. Reports must reconcile with the upstream provider invoice within an acceptable margin of error. | Operational |
| **Capacity planning** | Forecast future spend based on historical usage trends, planned onboarding of new teams, and anticipated changes in model pricing. Capacity plans should project 30, 60, and 90 days forward. | Optimized |
| **Cost optimization recommendations** | Identify opportunities to reduce cost without degrading quality: cache tuning, prompt optimization candidates, model tier adjustments, and batch processing eligibility. | Optimized |

---

## Security Operations

Security operations protect the platform, its users, and the data flowing through it. These capabilities assume that the security architecture defined in Layer 2 is in place and focus on the ongoing operational work required to keep it effective.

| Capability | Description | Maturity Stage |
|---|---|---|
| **Guardrail tuning** | Regularly review guardrail effectiveness by analyzing blocked requests, false positive rates, and bypass attempts. Adjust rules, thresholds, and patterns based on findings. | Foundation |
| **Blocked request review** | Establish a recurring process to review requests blocked by guardrails. Identify false positives that degrade developer experience and true positives that indicate policy violations or adversarial behavior. | Foundation |
| **Incident playbooks** | Document step-by-step response procedures for generative AI-specific incidents: prompt injection detected, sensitive data in output, guardrail bypass, unexpected model behavior, and upstream provider breach notification. | Operational |
| **Adversarial test execution** | Conduct periodic adversarial testing (red teaming) against the platform's guardrails and security controls. Test with evolving attack techniques and document findings. | Operational |
| **Post-incident review** | After every security incident, conduct a blameless review that identifies root cause, contributing factors, detection timeline, and remediation actions. Feed findings into guardrail updates and playbook revisions. | Operational |
| **Threat intelligence integration** | Monitor external sources for emerging generative AI attack techniques, model vulnerabilities, and supply chain risks. Update adversarial test suites and guardrail configurations accordingly. | Optimized |

---

## Audit and Compliance

Audit and compliance capabilities ensure that the platform operates within regulatory, legal, and organizational policy boundaries. These capabilities produce the evidence required to satisfy internal audit, external regulators, and contractual obligations.

| Capability | Description | Maturity Stage |
|---|---|---|
| **Automated compliance reporting** | Generate compliance reports directly from gateway logs, access control records, and guardrail activation data. Eliminate manual evidence collection wherever possible. | Operational |
| **Access matrix generation** | Produce a current-state access matrix showing which teams, service accounts, and individuals have access to which models, data sources, and configuration surfaces. The matrix should be generated on demand and diffable against previous versions. | Operational |
| **Policy violation tracking** | Detect, record, and escalate policy violations: requests that bypass guardrails, access control exceptions, unapproved model usage, and data classification breaches. Track violations to resolution. | Operational |
| **Classification enforcement verification** | Verify that data classification labels are being enforced at runtime. Confirm that data classified as restricted is not being sent to models or environments that lack the appropriate controls. | Optimized |
| **Audit trail integrity** | Ensure that audit logs are immutable, tamper-evident, and retained for the required duration. Implement log integrity verification and alerting on any gaps or anomalies in the audit trail. | Optimized |

---

## Developer Enablement

Developer enablement is the operational capability that determines whether the platform is used or bypassed. Every friction point is a risk to governance coverage.

| Capability | Description | Maturity Stage |
|---|---|---|
| **Self-service API key provisioning** | Allow developers to generate scoped API keys through an automated workflow without requiring manual approval for standard access tiers. Keys should be provisioned in minutes, not days. | Foundation |
| **Internal documentation** | Maintain accurate, current documentation covering platform capabilities, API reference, usage policies, prompt engineering guidance, and troubleshooting steps. Documentation must live where developers already look. | Foundation |
| **Runbooks** | Provide operational runbooks for common tasks: onboarding a new team, rotating API keys, requesting a budget increase, escalating a guardrail false positive, and responding to a cost alert. | Operational |
| **Office hours and support channels** | Offer scheduled office hours for synchronous help and a persistent support channel for asynchronous questions. Staff these with platform engineers who can resolve issues directly. | Operational |
| **Feedback mechanisms** | Provide structured channels for developers to report guardrail false positives, request new model access, suggest documentation improvements, and flag platform issues. Close the loop by communicating resolution. | Operational |
| **Adoption metrics** | Track and publish platform adoption indicators: number of active teams, request volume trends, onboarding completion time, time to first successful request, and support ticket volume. Use these metrics to identify friction and measure enablement effectiveness. | Optimized |
