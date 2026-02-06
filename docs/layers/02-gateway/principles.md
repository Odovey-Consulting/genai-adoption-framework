# Gateway Principles

The gateway layer is the single mandatory transit point for every interaction between an organization's workloads and external generative-AI model providers. These five principles define the non-negotiable properties that any gateway deployment must exhibit. Violating even one of them reopens the same risks the gateway was introduced to mitigate.

---

## Principle 1 — Single Point of Control

**Statement:** All model traffic — prompts, completions, embeddings, and fine-tuning data — must flow through the gateway; no workload may contact a provider directly.

**Rationale:**
A gateway can only enforce policy on traffic it can see. The moment a single workload bypasses it, the organization loses visibility into what data is leaving the network, what costs are being incurred, and whether content policies are being respected. Partial coverage is equivalent to no coverage, because adversarial or accidental leakage follows the path of least resistance.

**Implications:**
- Network egress rules must block direct connectivity from workload subnets to provider endpoints. The gateway subnet is the only path outbound.
- Service meshes, DNS policies, or firewall rules must be configured so that provider domains resolve only to the gateway's ingress address.
- Monitoring must alert on any attempted direct connection from a non-gateway host to a known provider IP range.

**Violations:**
- A developer hardcodes a provider URL in application configuration, bypassing the gateway entirely.
- A data-science notebook running on a personal laptop calls a model API using credentials stored locally, with no gateway in the path.
- An infrastructure team creates a "temporary" exception in firewall rules that is never removed, allowing a subnet to reach providers directly.

---

## Principle 2 — Provider Abstraction

**Statement:** Consuming teams interact with a provider-agnostic API surface; they must never need to know — or care — which upstream provider is fulfilling their request.

**Rationale:**
When workloads couple to a specific provider's API schema, authentication mechanism, or endpoint format, the organization loses the ability to switch providers, negotiate pricing, or implement failover without coordinated application changes. Abstraction insulates teams from provider-level changes and gives the platform team the freedom to re-route traffic for cost, compliance, or resilience reasons without filing change requests across dozens of teams.

**Implications:**
- The gateway exposes a single, canonical API contract. Workloads send requests to named model aliases (e.g., `text-general-large`), and the gateway resolves those aliases to a concrete provider and model version at runtime.
- Provider-specific fields, headers, and authentication tokens never appear in workload configurations, CI/CD pipelines, or application code.
- The gateway is responsible for translating between its canonical schema and each provider's native format.

**Violations:**
- Application code contains provider-specific SDK imports or endpoint URLs.
- Teams maintain separate code paths for different providers, switching between them with feature flags inside the application rather than at the gateway.
- Deployment manifests include environment variables that reference a provider name or model identifier that only one provider recognizes.

---

## Principle 3 — Enforcement at the Edge

**Statement:** Every policy — authentication, rate limiting, data-loss prevention, content filtering, and budget control — is evaluated at the gateway before the request leaves the organization's network boundary.

**Rationale:**
Once a prompt reaches an external provider, the organization has lost control of it. Sensitive data cannot be recalled, budget overruns cannot be reversed, and content-policy violations cannot be undone. Enforcement must therefore happen at the last point the organization controls: the gateway's egress path. Relying on provider-side controls means trusting a third party to enforce internal policy, which is both contractually fragile and operationally opaque.

**Implications:**
- Data classification scanning (PII, credentials, proprietary data) runs as part of the gateway's request pipeline, before the request is forwarded.
- Budget checks are synchronous. If a team has exhausted its allocation, the request is rejected immediately — not logged for reconciliation after the fact.
- Content-filtering rules are applied to both the outbound prompt and the inbound completion, ensuring that policy violations on either side are caught before they reach the workload.

**Violations:**
- DLP scanning is deferred to a batch process that reviews logs after requests have already been sent to a provider.
- Budget alerts fire via email after a team has already exceeded its spend limit by a significant margin.
- Content-safety checks exist only on the provider's side, with no organization-controlled validation.

---

## Principle 4 — Fail Closed

**Statement:** If the gateway cannot verify, classify, or authorize a request, the request must be rejected — never forwarded in an uncertain state.

**Rationale:**
Fail-open behavior in a security-critical component turns every outage, misconfiguration, or edge case into a potential data breach. A gateway that forwards unverified traffic during a DLP-service outage is indistinguishable from having no DLP at all. The temporary inconvenience of a rejected request is categorically less severe than the permanent consequence of leaked regulated data. Teams must internalize that availability of the AI service is subordinate to the integrity of the control plane.

**Implications:**
- If the DLP classification service is unreachable, the gateway returns a 503 to the caller rather than forwarding the unscanned prompt.
- If the authentication service cannot validate a token, the request is denied with a 401, even if the token was valid minutes earlier.
- Circuit breakers and health checks must distinguish between "provider unavailable" (trigger failover) and "control-plane component unavailable" (reject the request).

**Violations:**
- The gateway is configured to skip DLP checks when the scanning service is slow, in order to meet latency SLAs.
- An operator sets a gateway flag to "allow all" during an incident, and the flag is never reverted.
- Rate-limit state is stored in a cache that, when evicted, defaults to "unlimited" rather than "zero."

---

## Principle 5 — Credential Isolation

**Statement:** Provider API keys and authentication credentials are stored and managed exclusively within the gateway's secrets store; workloads never possess, see, or transmit provider credentials.

**Rationale:**
Credentials are the keys to external spend and data exposure. Every copy of a credential is an additional attack surface: a leaked environment variable, a committed secret, a logged header. By centralizing credentials in the gateway, the blast radius of a workload compromise is limited to the workload's own data — it cannot impersonate the organization against a provider. This also makes credential rotation a single-point operation rather than a coordinated rollout across every consuming team.

**Implications:**
- Workloads authenticate to the gateway using internally issued, scoped tokens. The gateway then attaches provider credentials to the outbound request.
- Provider credentials are injected into the gateway at runtime from a secrets manager. They never appear in source control, container images, or deployment manifests.
- Credential rotation is performed at the gateway layer alone. Consuming teams are unaware that a rotation has occurred.

**Violations:**
- Teams receive provider API keys via a shared credentials document or chat message and embed them in application configuration.
- Provider credentials are stored as environment variables in a container orchestration manifest checked into source control.
- A "service account" key for a provider is shared across multiple gateways with no audit trail of which gateway used it for which request.
