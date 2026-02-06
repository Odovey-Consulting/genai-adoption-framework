# Gateway Anti-Patterns

Anti-patterns are recurring design choices that appear reasonable on the surface but lead to predictable, damaging consequences. Recognizing them early is cheaper than remediating them after they have calcified into organizational habit. The following six anti-patterns are the most common failure modes observed in gateway deployments.

---

## 1. "The Bypass"

**Description:**
Workloads call model providers directly, circumventing the gateway entirely. This usually begins as a "temporary" shortcut — a developer testing a new model from a notebook, a team that finds the gateway's latency unacceptable, or a proof-of-concept that was never migrated to the governed path. Over time, the number of direct connections grows, and the gateway's view of organizational AI usage becomes incomplete.

**Symptoms:**
- Provider invoices show usage that does not appear in gateway logs.
- Security scans discover provider API keys embedded in application code, environment variables, or CI/CD pipelines.
- Network monitoring reveals DNS queries or TCP connections from workload subnets to provider endpoint domains.

**Consequences:**
- Data-loss prevention controls are bypassed. Sensitive data may be sent to providers without classification or redaction.
- Cost tracking is inaccurate. Budget enforcement is ineffective because the gateway does not see the spending.
- Incident response is blind. If a data-exposure event occurs through a direct connection, there are no gateway logs to reconstruct what was sent.

**Correct approach:**
Enforce the single-point-of-control principle at the network level. Block direct egress from workload subnets to provider IP ranges using firewall rules. Ensure that provider domains resolve only to the gateway's internal address via DNS policy. Run periodic audits that reconcile provider-reported usage against gateway-reported usage — any discrepancy is evidence of a bypass.

---

## 2. "The Leaky Bucket"

**Description:**
The gateway is deployed in a public subnet, on a host with a public IP address, or behind a load balancer with an internet-facing listener. The original intent may have been to simplify deployment or to allow external partners to reach the gateway, but the result is that the gateway is reachable from outside the organization's network boundary.

**Symptoms:**
- The gateway's ingress endpoint resolves to a public IP address.
- Security scans flag the gateway's port as externally accessible.
- Unexpected requests appear in gateway logs from IP addresses that do not belong to any known workload subnet.

**Consequences:**
- The gateway becomes a target for credential-stuffing, denial-of-service, and unauthorized model access.
- An attacker who compromises a valid API key can use the gateway from anywhere on the internet, turning the organization into an unwitting proxy for the attacker's model usage.
- Compliance frameworks that require AI services to be internal-only are violated.

**Correct approach:**
Deploy the gateway in a private subnet with no inbound route from the internet. Expose it only to internal workload subnets via private load balancers or service-mesh ingress. If external partner access is required, provision it through a separate, tightly scoped API gateway with additional authentication layers — never by exposing the AI gateway itself.

---

## 3. "The God Key"

**Description:**
A single API key (or a small set of shared keys) is distributed to all teams and workloads. The key is typically created during initial setup, shared via a wiki page or chat message, and never rotated. Every request to the gateway carries the same credential.

**Symptoms:**
- The gateway's authentication logs show a single key identifier across all teams and workloads.
- When asked "which team made this request?", the platform team cannot answer from gateway data alone.
- A key rotation requires coordinating with every team simultaneously, so rotations are deferred indefinitely.

**Consequences:**
- Attribution is impossible. Cost tracking, rate limiting, and access control cannot be applied per team because the gateway cannot distinguish one team from another.
- Blast radius is unlimited. If the shared key is leaked, every team's access must be revoked, and a new key must be distributed to everyone — an operational crisis.
- Compliance audits fail. Auditors expect to see per-team or per-service credentials with evidence of regular rotation.

**Correct approach:**
Issue scoped, per-team (or per-workload) API keys with defined permissions, rate limits, and budget ceilings. Implement automated key rotation with lifetimes no longer than 90 days. Provide self-service key management so that teams can rotate their own keys without filing a ticket. Supplement API keys with mTLS for transport-layer identity.

---

## 4. "The Black Hole"

**Description:**
The gateway is deployed and routing traffic, but logging is disabled, misconfigured, or writing to a destination that nobody monitors. This usually happens when logging is treated as a "phase two" deliverable, when log storage costs trigger a decision to reduce retention to near-zero, or when the logging pipeline silently fails and nobody notices.

**Symptoms:**
- The platform team cannot answer basic questions: "How many requests did team X send last week?" or "What was our total token consumption yesterday?"
- Incident investigations stall because there is no request-level data to examine.
- Cost reconciliation against provider invoices is impossible.

**Consequences:**
- The organization is operating a critical piece of infrastructure with no observability. Anomalies (cost spikes, data leakage, abuse) go undetected.
- Compliance requirements for audit trails and data-processing records are unmet.
- Model-quality evaluation and prompt-engineering feedback loops are broken because there is no record of what was sent or received.

**Correct approach:**
Treat logging as a foundation-stage capability that must be operational before the first production request flows. Configure metadata logging for all requests at a minimum. Implement log-pipeline health checks that alert when log volume drops below a baseline. Assign a log-storage budget and defend it in the same way the organization defends its security-tooling budget.

---

## 5. "The DIY Trap"

**Description:**
An engineering team builds a custom gateway from scratch — typically starting as a thin reverse proxy with some routing logic — rather than evaluating and adopting an existing gateway solution. The justification is usually that the organization's requirements are "unique" or that an off-the-shelf solution cannot match the team's preferred technology stack.

**Symptoms:**
- A small team (one to three engineers) is dedicating the majority of its capacity to maintaining the gateway rather than building value on top of it.
- Features like DLP integration, cost tracking, and budget enforcement are perpetually on the roadmap but never delivered.
- The gateway lacks documentation, has no upgrade path, and its operational knowledge is concentrated in one person.

**Consequences:**
- Opportunity cost is enormous. Engineering time spent reinventing routing, logging, and authentication is time not spent on the capabilities that differentiate the organization.
- The custom gateway accumulates technical debt faster than a supported solution because it has no community, no security-patch cadence, and no compatibility-testing matrix.
- When the original builder leaves the organization, the gateway becomes an unmaintainable artifact.

**Correct approach:**
Evaluate existing gateway solutions against the organization's requirements before committing to a build. Most requirements — routing, authentication, logging, rate limiting — are table stakes that existing solutions handle well. Reserve custom development for genuinely unique capabilities (e.g., organization-specific DLP rules, proprietary routing algorithms) and implement them as plugins or extensions to an existing gateway rather than as a standalone system.

---

## 6. "The Tight Coupling"

**Description:**
Workloads integrate directly against a specific provider's API schema, SDK, and authentication mechanism, with the gateway acting as a pass-through rather than an abstraction layer. The gateway forwards requests in the provider's native format without translation, and workloads include provider-specific libraries in their dependency trees.

**Symptoms:**
- Application code imports a provider-specific SDK rather than using the gateway's canonical API client.
- Request payloads contain provider-specific fields (e.g., provider-proprietary parameters, model identifiers that are only valid for one provider).
- Switching to a different provider requires code changes in every consuming application.

**Consequences:**
- Provider migration is a cross-team engineering project rather than a gateway configuration change. This gives the incumbent provider disproportionate negotiating leverage.
- Failover to an alternative provider is impossible without application-level logic that duplicates the gateway's routing responsibility.
- The gateway's value proposition — abstraction and centralized control — is undermined. It becomes an expensive, unnecessary hop that adds latency without adding governance.

**Correct approach:**
Define and enforce a provider-agnostic API contract at the gateway. Workloads send requests in the gateway's canonical format; the gateway translates to the provider's native format on the outbound path and normalizes the response on the inbound path. Publish a gateway client library (or an API specification that teams can generate clients from) and deprecate direct use of provider SDKs in production workloads.
