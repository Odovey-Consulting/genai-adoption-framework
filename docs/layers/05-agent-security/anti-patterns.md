# Agent Security Anti-Patterns

> **Layer:** 05 — Agent Security
> **Purpose:** Document common failure modes in agent security so organizations can recognize and avoid them. Each anti-pattern is linked to the principle or best practice that corrects it.

Anti-patterns are recurring approaches that appear reasonable on the surface but produce poor outcomes at scale. In agent security, these failure modes are particularly dangerous because agents act autonomously—the time between a security gap and an exploitable incident is measured in seconds, not days.

---

## 1. The Zombie

**Description:** Agents are deployed with long-lived credentials—API keys, service-account tokens, static secrets—that are embedded in configuration files, environment variables, or code repositories and never expire. The credentials remain valid indefinitely, even after the agent's purpose has changed, its owner has left the organization, or the agent itself has been decommissioned in name but not in practice. The agent continues to operate with full access, sustained by credentials that nobody is managing.

**Symptoms:**
- Security scans of code repositories and configuration stores reveal static API keys and tokens associated with agent workloads, some with creation dates months or years in the past.
- Credential rotation reports show agent-associated secrets that have never been rotated since issuance.
- Decommissioned agents are removed from the registry but their credentials remain active in the systems they accessed, and usage logs show continued invocations from those credentials.

**Consequences:**
- A compromised credential grants an attacker persistent, long-lived access to every tool and system the agent was authorized to reach—potentially for the full remaining lifetime of the credential.
- Credential compromise goes undetected because nobody is monitoring for abnormal usage on a "decommissioned" agent's credentials.
- The blast radius of a single credential compromise spans every system the agent has ever been authorized to access, because long-lived credentials accumulate permissions over the agent's lifetime.

**Correct approach:** Implement [just-in-time credential injection](best-practices.md#practice-2-use-just-in-time-credential-injection--agents-never-hold-secrets) so that agents never hold persistent secrets. Apply the principle of [no standing access](principles.md#4-no-standing-access): credentials are issued at invocation time, scoped to the specific operation, and expire automatically.

---

## 2. The Orphan

**Description:** Agents operate in production with no identifiable human owner. Ownership was either never assigned at registration, was assigned to a team alias or shared role rather than an individual, or was assigned to a person who has since left the organization without transferring ownership. The agent continues to function autonomously, but nobody is accountable for its behavior, its access scope, or its ongoing compliance.

**Symptoms:**
- The agent registry contains entries where the owner field is blank, references a team name, or references an employee who is no longer with the organization.
- When an alert fires for unusual agent behavior, the security operations team cannot identify a responsible individual to investigate or approve remediation.
- Periodic access reviews skip agents without valid owners because there is no one to perform the attestation, and the agents continue in production unreviewed.

**Consequences:**
- Incident response is delayed or impossible because nobody has the context to assess whether the agent's behavior is expected or anomalous.
- The agent's permissions drift unchecked: tools are added, parameter scopes expand, and nobody evaluates whether the changes are appropriate.
- The organization cannot demonstrate accountability for the agent's actions to auditors or regulators, because no individual can attest to its purpose, scope, or compliance posture.

**Correct approach:** Apply the principle of [every agent has a human owner](principles.md#1-every-agent-has-a-human-owner). Enforce ownership binding in the registry, trigger automated workflows when ownership gaps are detected, and suspend agents that exceed the grace period without a valid owner.

---

## 3. The Skeleton Key

**Description:** Agents are granted access to all available tools with no scoping, or with tool-level scoping so broad that it is functionally unrestricted. This often happens when teams take the path of least resistance at registration time—requesting "all tools" or "all read access"—and the authorization system either does not enforce default-deny or applies permissions too generously. The agent can invoke any tool on the platform, and often with no parameter-level constraints.

**Symptoms:**
- Agent authorization policies contain wildcard grants or "all tools" designations rather than explicit, enumerated tool lists.
- Usage analysis reveals that agents are authorized for dozens of tools but routinely invoke only two or three; the remaining authorizations are unused but exploitable.
- Parameter-level policies do not exist or are not enforced; agents authorized to query a database can query any table, any column, any row.

**Consequences:**
- A compromised agent has access to every tool on the platform, making the blast radius of a single compromise equivalent to a platform-wide breach.
- The principle of least privilege is inoperative: the agent's effective permissions bear no relationship to its declared purpose.
- Over-privileged agents are invisible to access reviews because the reviewers have no baseline against which to evaluate whether the permissions are appropriate.

**Correct approach:** Implement [tool-level access control with default-deny semantics](best-practices.md#practice-3-implement-tool-level-access-control-before-parameter-level-policy) and apply the principle of [authorization at the tool AND parameter level](principles.md#6-authorization-at-the-tool-and-parameter-level). Every agent should have an explicit, enumerated tool list that matches its declared purpose, with parameter-level constraints on high-risk tools.

---

## 4. The Ghost

**Description:** Autonomous agent-like workloads operate outside the platform's agent registry and management framework entirely. These are scripts, automation workflows, CI/CD jobs, or custom applications that call model endpoints in iterative patterns, invoke tools based on model outputs, and take actions across systems—but they are not recognized or registered as agents. They have no agent identity, no ownership binding, no tool-level authorization, no audit trail, and no lifecycle management.

**Symptoms:**
- Network monitoring reveals iterative model-endpoint calls from IP addresses or service accounts not associated with any registered agent.
- Tool invocation logs show patterns consistent with agent-driven automation (multi-step chains, automated retries, conditional branching) but the calling identity does not appear in the agent registry.
- Teams describe their automation as "just a script" or "just a workflow" and are unaware that it falls under the agent security framework.

**Consequences:**
- The organization's actual agent population is significantly larger than the registry reflects, meaning the security posture is weaker than believed.
- Ghost agents operate with whatever permissions their underlying service account or API key provides, which is often far broader than what a properly registered agent would receive.
- When a ghost agent causes an incident—a data leak, an unauthorized modification, an unexpected cost spike—there is no audit trail, no owner, and no mechanism for rapid suspension.

**Correct approach:** Implement [shadow agent discovery](best-practices.md#practice-5-run-shadow-agent-discovery-before-assuming-you-know-all-your-agents) as a continuous capability. Apply the principle of [discovery before control](principles.md#5-discovery-before-control): find the agents first, then bring them under management.

---

## 5. The Assumed Secure

**Description:** The organization assumes that the communication protocols used for tool invocation enforce authentication and authorization by default, without explicitly verifying or configuring these controls. Teams deploy agents that connect to tool servers over standard protocols and assume the protocol layer handles identity verification, encryption, and access control. In reality, many tool communication protocols transmit requests without authenticating the caller, do not encrypt payloads by default, and rely on the application layer for authorization that may not be implemented.

**Symptoms:**
- Agent-to-tool communication flows over protocols with no authentication layer configured; any client on the network can invoke the tool server's endpoints.
- Security reviews of tool server configurations reveal default settings with authentication disabled or set to optional.
- Penetration testing demonstrates that unauthenticated requests to tool endpoints succeed, returning data or performing actions without verifying the caller.

**Consequences:**
- Any actor with network access to a tool server—including compromised workloads, lateral movers, or insider threats—can invoke tools as if they were an authorized agent.
- The organization's tool-level access-control policies are bypassed entirely because the enforcement point (authentication at the tool server) is not actually operational.
- Incident investigation reveals that unauthorized tool invocations were possible for the entire period the tool server was exposed, with no way to distinguish authorized from unauthorized access in the logs.

**Correct approach:** Apply the principle of [every agent action is traceable](principles.md#3-every-agent-action-is-traceable) and implement [agent authentication at the tool access layer](capabilities.md#5-agent-authentication-at-the-tool-access-layer). Verify that every tool server requires authenticated, authorized requests and that default configurations are reviewed before deployment.

---

## 6. The Trust Fall

**Description:** Agents communicate with other agents—delegating tasks, sharing data, passing instructions—without any mutual authentication mechanism. Trust is assumed based on proximity: agents on the same platform, in the same network segment, or in the same orchestration framework are treated as inherently trustworthy. No identity verification occurs at the agent-to-agent boundary, and no delegation chain tracking records the interaction.

**Symptoms:**
- Agent-to-agent communication logs (if they exist) show no authentication events; agents accept delegation requests and data from any caller that can reach them.
- Multi-agent workflows operate within a shared execution environment where agents invoke each other through function calls or message passing with no identity exchange.
- When asked how agent B verifies that a delegation from agent A is legitimate, the engineering team's answer references network segmentation or platform-level trust rather than cryptographic identity verification.

**Consequences:**
- A compromised agent can impersonate any other agent on the same network or platform, sending fraudulent delegation requests that are accepted without question.
- Delegation chains cannot be reconstructed because there is no authenticated record of which agent delegated to which other agent; the chain of accountability is broken.
- Malicious instructions can be injected into multi-agent workflows by any actor with access to the communication channel, because there is no mechanism to verify the source's identity.

**Correct approach:** Apply the principle of [agent-to-agent communication requires mutual authentication](principles.md#7-agent-to-agent-communication-requires-mutual-authentication). Implement [mutual authentication for agent-to-agent communication](best-practices.md#practice-7-require-mutual-authentication-for-all-agent-to-agent-communication) using identity-based credentials, not network-level trust.
