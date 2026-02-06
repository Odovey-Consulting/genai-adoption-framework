# Gateway Deployment Checklist

This checklist validates that the gateway --- Layer 2 of the GenAI Adoption Framework --- is ready for production traffic. The gateway is the single enforcement point through which all model traffic flows. A misconfigured or incompletely deployed gateway undermines every layer above it.

Complete every applicable item before routing production traffic through the gateway. The Governance Readiness checklist must be complete before beginning this checklist; the gateway enforces the policies defined at the governance layer, and deploying a gateway without those policies is deploying infrastructure without purpose.

**Owner:** The platform team lead or infrastructure architect responsible for the GenAI platform.

**Prerequisite:** [Governance Readiness Checklist](governance-readiness.md) --- complete.

---

## Infrastructure

- [ ] Gateway is deployed within a private network segment with no direct inbound access from the public internet.
- [ ] Egress traffic from the gateway to external model providers is controlled through a defined allowlist of endpoints.
- [ ] High availability is configured with a minimum of two instances across separate failure domains (availability zones, regions, or equivalent).
- [ ] Health check endpoints are configured and monitored by the infrastructure layer.
- [ ] Failover behavior has been tested: when the primary instance fails, traffic routes to the secondary within the defined recovery time objective.
- [ ] Resource scaling thresholds have been defined and configured (auto-scaling or capacity planning documented).
- [ ] TLS termination is configured at the gateway with certificates managed through the organization's standard certificate lifecycle process.
- [ ] Network segmentation prevents lateral movement from the gateway to unrelated systems.

## Authentication and Credential Management

- [ ] Per-team or per-workload API keys have been issued; no shared keys exist across team boundaries.
- [ ] API key provisioning follows a documented process with approval, issuance, and revocation steps.
- [ ] Model provider credentials (API keys, service account tokens) are stored in a secrets management system, not in configuration files, environment variables, or source code.
- [ ] Provider credentials are rotated on a defined schedule and rotation has been tested end-to-end.
- [ ] Mutual TLS (mTLS) or equivalent mutual authentication is configured between the gateway and upstream consumers where the threat model requires it.
- [ ] Authentication failures result in a denied request with a structured error response; the gateway does not fall back to unauthenticated access.

## Policy Enforcement

- [ ] The model registry within the gateway is populated from the governance-approved model catalog; only approved models are routable.
- [ ] Content filters are configured for both request and response payloads, aligned with the data classification tiers defined in governance.
- [ ] Data loss prevention rules are active and configured to detect the sensitive data patterns specified in governance policy (e.g., personal identifiers, credentials, regulated data elements).
- [ ] Rate limits are configured per team, per workload, or per classification tier as defined in cost governance.
- [ ] Budget enforcement is active: requests that would exceed a team's cost ceiling are blocked or queued according to the escalation policy defined in governance.
- [ ] Default-deny is verified: requests for unapproved models, from unrecognized identities, or without valid authentication are rejected.
- [ ] Content filter bypass is not possible through prompt manipulation, header injection, or request formatting.

## Logging and Audit

- [ ] Request and response logging is enabled for all traffic passing through the gateway.
- [ ] Log entries include: timestamp, requesting identity, target model, request metadata, response metadata, policy decisions applied, and latency.
- [ ] Sensitive content in logs is handled according to the data retention policy (redacted, encrypted, or stored in a restricted-access log tier).
- [ ] Log retention periods are configured to match the requirements documented in the governance layer.
- [ ] Log pipeline has been verified end-to-end: a test request generates a log entry that arrives at the log aggregation system within the expected time window.
- [ ] Log integrity controls are in place (append-only storage, checksums, or equivalent tamper-resistance mechanisms).

## Testing and Validation

- [ ] End-to-end request flow has been verified: a request from a consumer application passes through authentication, policy enforcement, model routing, response filtering, and logging without error.
- [ ] Failover has been tested under load: primary instance is taken offline while traffic is flowing, and the secondary handles requests within acceptable latency.
- [ ] DLP rules have been validated with test payloads containing synthetic sensitive data; blocked requests return appropriate error responses.
- [ ] Rate limit behavior has been tested: exceeding the configured rate produces the expected throttling response.
- [ ] An unauthenticated request has been attempted and confirmed to be rejected.
- [ ] A request for a model not in the approved catalog has been attempted and confirmed to be rejected.

---

## Completion Criteria

All items in Infrastructure, Authentication, and Policy Enforcement must be complete before production traffic is routed through the gateway. Logging and Testing items must be complete before the gateway is declared production-ready. No exceptions.

**Completed by:** ___________________________
**Date:** ___________________________
**Approved by:** ___________________________
