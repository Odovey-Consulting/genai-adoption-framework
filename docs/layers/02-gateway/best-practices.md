# Gateway Best Practices

This document captures the operational practices that distinguish a well-run gateway deployment from one that merely exists. Each practice is presented with context, implementation guidance, and the risk it mitigates.

---

## 1. Deploy in a Private Subnet with No Direct Internet Access

The gateway should reside in a private network segment that has no inbound route from the public internet and no default outbound route. Outbound connectivity to model providers is established through a NAT gateway or, preferably, through private connectivity services that keep traffic off the public internet entirely.

**Implementation guidance:**
- Place the gateway's compute instances in a subnet whose route table contains no `0.0.0.0/0` route pointing to an internet gateway.
- Provision a NAT gateway or private-link/private-connect endpoint in a separate subnet dedicated to egress.
- Use network access-control lists and security-group rules to allow inbound traffic only from known workload subnets on the gateway's listening port.
- Validate the configuration with a connectivity test: attempt to reach an arbitrary public endpoint from the gateway host. The attempt should fail.

**Risk mitigated:** A gateway exposed to the public internet is an attack surface for credential theft, denial-of-service, and unauthorized model access. A public-facing gateway also inverts the trust model — instead of trusting the internal network, you must trust the entire internet.

---

## 2. Use mTLS Between Workloads and the Gateway

Transport-layer security between consuming workloads and the gateway should be mutual: the gateway verifies the client's certificate, and the client verifies the gateway's certificate. This establishes identity at the transport layer, independent of any application-layer API key.

**Implementation guidance:**
- Issue short-lived client certificates from an internal certificate authority, scoped to the workload's identity (team, service name, environment).
- Configure the gateway to require client-certificate verification on its ingress listener. Requests without a valid client certificate are rejected at the TLS handshake, before any application logic executes.
- Automate certificate rotation with a sidecar or init process in the workload's runtime environment. Certificate lifetimes of 24 hours or less are recommended.
- Log the client-certificate subject as part of the request metadata for attribution and audit.

**Risk mitigated:** API keys alone are bearer tokens — anyone who possesses them can use them. mTLS binds identity to a cryptographic credential that is validated at the transport layer, reducing the risk of key theft, replay attacks, and unauthorized lateral movement.

---

## 3. Use Named Model Aliases to Decouple Teams from Providers

Workloads should never reference a provider-specific model identifier. Instead, the gateway maintains a mapping from human-readable aliases (e.g., `text-general-large`, `code-assist-fast`, `embedding-standard`) to concrete provider-and-model pairs. The mapping is managed by the platform team and is transparent to consumers.

**Implementation guidance:**
- Define a naming convention for aliases that encodes capability and performance tier but never encodes a provider name or model family. For example: `{modality}-{use-case}-{size}`.
- Store the alias-to-provider mapping in the gateway's model registry. Changes to the mapping take effect at the gateway without any workload redeployment.
- Publish the alias catalog to an internal developer portal so that teams can discover available models and their characteristics.
- When a new provider or model version is onboarded, update the mapping and optionally use weighted routing to shift traffic gradually.

**Risk mitigated:** Without aliases, every provider change requires a coordinated update across every consuming application. Aliases reduce provider migration from a cross-team project to a single configuration change.

---

## 4. Implement Egress Control via Private Connectivity Where Available

Wherever a model provider supports private-network connectivity (private endpoints, dedicated interconnects, VPN tunnels), prefer it over public-internet egress. This keeps prompt and completion data off the public internet and reduces exposure to network-level interception.

**Implementation guidance:**
- Audit each model provider's connectivity options during onboarding. Prioritize providers that support private connectivity for regulated workloads.
- Establish private endpoints in the same region as the gateway to minimize latency.
- If private connectivity is not available, route traffic through a NAT gateway with static IP addresses. Register those IPs with the provider's allowlist if the provider supports IP-based access control.
- Monitor egress traffic volume and cost. Private connectivity often carries a fixed cost that becomes more economical at scale than per-gigabyte NAT charges.

**Risk mitigated:** Public-internet egress exposes traffic to network-level threats (interception, BGP hijacking) and makes the organization dependent on the provider's TLS implementation as the sole layer of transport security.

---

## 5. Log Metadata for All Requests; Log Prompt Content Only for Approved Tiers

Every request that transits the gateway must produce a metadata log entry: timestamp, team identifier, model alias, resolved provider, token counts, latency, cost, and response status. Prompt and completion content, however, should only be logged when the request's data-classification tier permits it.

**Implementation guidance:**
- Define a tiered logging policy aligned with the organization's data-classification scheme. For example: public and internal-only tiers log full content; confidential and restricted tiers log metadata only.
- Implement the tiered policy as a gateway pipeline stage that inspects the request's classification tag (set by the DLP stage or the team's access profile) and selectively redacts content fields before writing the log entry.
- Store content logs in a separate, access-controlled log sink with stricter retention and access policies than metadata logs.
- Ensure that log entries include a correlation identifier that links metadata logs to content logs (when content is logged) for forensic reconstruction.

**Risk mitigated:** Logging everything exposes the organization to data-breach risk within its own logging infrastructure — a compromised log sink becomes a trove of sensitive prompts. Logging nothing makes incident investigation, compliance auditing, and model-quality evaluation impossible. Tiered logging balances these tensions.

---

## 6. Set Retention Policies Aligned with Compliance Requirements

Log retention is not a storage decision — it is a compliance decision. Retention periods must be defined per data-classification tier and aligned with regulatory, contractual, and internal-policy requirements.

**Implementation guidance:**
- Work with legal and compliance teams to determine minimum and maximum retention periods for each data tier. Some regulations mandate minimum retention (e.g., seven years for financial records); others mandate maximum retention (e.g., data-minimization requirements under privacy regulations).
- Configure automated lifecycle rules on log storage to enforce deletion at the end of the retention period. Manual deletion processes are error-prone and unauditable.
- Ensure that retention policies apply to all copies of log data, including replicas, backups, and downstream analytics stores.
- Document retention decisions in the organization's records-management policy and reference them in the gateway's operational runbook.

**Risk mitigated:** Over-retention creates liability (more data to breach, more data subject to discovery). Under-retention creates compliance gaps and makes post-incident investigation impossible.

---

## 7. Use the Gateway's Cost Tracking as the Single Source of Billing Truth

The gateway sees every request and calculates its cost at the point of transit. This makes it the most accurate, most granular, and most timely source of cost data. Do not rely on provider invoices as the primary cost-tracking mechanism — they arrive late, lack internal attribution, and cannot be reconciled at the team level without the gateway's metadata.

**Implementation guidance:**
- Maintain a pricing table in the gateway's configuration that mirrors each provider's published per-token rates. Update it whenever a provider changes pricing.
- Tag every request's cost record with the team, workload, environment, and model alias. Export these records to the organization's cost-management or business-intelligence platform daily.
- Reconcile gateway-reported costs against provider invoices monthly. Discrepancies indicate either a pricing-table drift or traffic that bypassed the gateway (which is itself a critical finding).
- Use gateway cost data — not provider invoices — for internal chargebacks, budget reviews, and executive reporting.

**Risk mitigated:** Without gateway-based cost tracking, the organization operates with a billing lag measured in weeks, cannot attribute costs below the account level, and has no mechanism to detect billing anomalies in near-real-time.

---

## 8. Treat the Gateway Configuration as Infrastructure-as-Code

Every aspect of the gateway's configuration — model registry entries, routing rules, rate limits, budget ceilings, DLP policies, content-filtering rules, and logging tiers — must be defined in version-controlled configuration files, applied through a CI/CD pipeline, and promoted through environments (development, staging, production) using the same workflow as any other infrastructure change.

**Implementation guidance:**
- Store gateway configuration in a dedicated repository (or a directory within the platform team's infrastructure repository). Use a declarative format (YAML, JSON, or a domain-specific language supported by the gateway).
- Require pull-request review and approval for all configuration changes. Reviewers should include at least one platform engineer and, for policy changes, a representative from security or compliance.
- Apply changes through an automated pipeline that validates syntax, runs integration tests against a staging gateway, and promotes to production only on success.
- Maintain an audit trail: every configuration change is a commit with an author, timestamp, and description. The gateway's running configuration can be reconstructed for any point in time by checking out the corresponding commit.

**Risk mitigated:** Manual, ad-hoc configuration changes are the leading cause of gateway misconfigurations — a mistyped rate limit, a deleted routing rule, a logging policy set to the wrong tier. Infrastructure-as-code eliminates "it worked on my machine" and ensures that every environment is a known, reproducible state.
