# Operations Readiness Checklist

This checklist validates that the operations layer --- Layer 3 of the GenAI Adoption Framework --- is ready to support production workloads. Operations transforms a deployed gateway into a managed platform by providing observability, security operations, and developer enablement. Without this layer, the platform is running but not managed.

Complete every applicable item before onboarding workload teams to the platform. The Gateway Deployment checklist must be complete before beginning this checklist; operations depends on the telemetry and enforcement capabilities the gateway provides.

**Owner:** The platform team lead, in coordination with the security operations team and developer experience team.

**Prerequisite:** [Gateway Deployment Checklist](gateway-deployment.md) --- complete.

---

## Observability

- [ ] Operational dashboards are deployed and accessible to the platform team, covering: request volume, latency distribution, error rates, and model utilization.
- [ ] Key metrics are defined, tracked, and baselined: requests per second, p50/p95/p99 latency, error rate by type, token consumption, and cache hit ratio (if applicable).
- [ ] Alerts are configured for critical conditions: gateway unavailability, error rate spike above baseline, latency degradation beyond SLA, and log pipeline failure.
- [ ] Alert routing is configured: each alert type has a defined recipient (team or on-call rotation) and an expected response time.
- [ ] Historical trend data is retained for capacity planning and anomaly detection (recommended: minimum 90 days at full resolution).
- [ ] A runbook exists for each alert, documenting the expected investigation steps and escalation path.

## Cost Management

- [ ] Per-team cost dashboards are deployed and accessible to both platform operators and team leads.
- [ ] Cost dashboards display: current spend, spend rate, projected spend at current rate, and budget remaining.
- [ ] Budget alerts are active and tested: alerts fire when spend reaches defined thresholds (e.g., 50%, 70%, 90% of allocated budget).
- [ ] Chargeback or showback reporting has been tested end-to-end: a sample billing cycle has been processed and the output reviewed by finance stakeholders.
- [ ] Cost anomaly detection is configured: a sudden spike in spend (beyond a defined percentage above baseline) triggers an alert to the platform team and the affected team's lead.
- [ ] Model cost efficiency data is available: cost per request by model, enabling informed decisions about model selection.

## Security Operations

- [ ] Guardrails are configured and active for all content classifications defined in governance policy.
- [ ] A process is defined for reviewing blocked requests: who reviews them, at what cadence, and what actions can result from the review (policy adjustment, user notification, incident escalation).
- [ ] Incident response playbooks are drafted for GenAI-specific scenarios: data leakage through model interaction, prompt injection attack detected, unauthorized model access, cost runaway, and model provider outage.
- [ ] Playbooks have been reviewed by the security operations team and tabletop-tested (a walkthrough exercise simulating each scenario).
- [ ] Threat detection rules are configured to identify patterns of concern: repeated DLP triggers from a single identity, access attempts to unauthorized models, and anomalous request volumes.
- [ ] Security event correlation is configured: GenAI security events are integrated into the organization's existing security monitoring pipeline.

## Developer Enablement

- [ ] Self-service API key provisioning has been tested end-to-end: a developer can request, receive, and use a key without manual intervention from the platform team (beyond approval, if required by policy).
- [ ] Developer documentation is published and accessible, covering: how to request access, how to authenticate, which models are available, what rate limits apply, and how to read cost dashboards.
- [ ] Code examples and integration templates are available for the most common integration patterns (direct API call, SDK wrapper, and streaming, at minimum).
- [ ] A support channel is established for developer questions and issue reporting (chat channel, ticketing system, or equivalent), with a defined response time commitment.
- [ ] A sandbox or development environment is available where developers can experiment with approved models without affecting production budgets or metrics.
- [ ] Onboarding documentation walks a new team through the full process from access request to first successful model invocation.

## Compliance and Audit

- [ ] Audit reporting pipeline is configured: compliance-relevant events flow from gateway logs through the processing pipeline to an audit-ready reporting format.
- [ ] An access matrix has been generated documenting which teams have access to which models and at what classification tier.
- [ ] Audit reports can be generated on demand with a turnaround time that meets regulatory requirements (recommended: within 24 hours for any standard report).
- [ ] Data retention enforcement has been verified: logs older than the maximum retention period are automatically purged, and logs within the minimum retention period cannot be deleted.
- [ ] A compliance dashboard or report exists that shows current policy adherence rates (e.g., percentage of traffic routed through the gateway, percentage of workloads with assigned owners).

---

## Completion Criteria

All Observability and Security Operations items must be complete before any workload team is onboarded. Developer Enablement items must be complete before workload teams are expected to self-serve. Cost Management and Compliance items may mature incrementally but must be fully resolved before the organization enters the Operational maturity stage.

**Completed by:** ___________________________
**Date:** ___________________________
**Approved by:** ___________________________
