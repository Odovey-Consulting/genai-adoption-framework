# Agent Security Principles

> **Layer:** 05 — Agent Security
> **Purpose:** Define the non-negotiable principles that govern the identity, authorization, and auditability of autonomous agents operating within the enterprise.

These seven principles address the unique security challenges that emerge when generative-AI workloads evolve from stateless request-response patterns into autonomous agents that hold credentials, invoke tools, and act on behalf of humans. They are ordered intentionally: ownership establishes accountability, permission boundaries contain authority, traceability enables oversight, ephemeral credentials limit exposure, discovery reveals the true attack surface, parameter-level control closes the authorization gap, and mutual authentication secures the communication fabric. Every capability, best practice, and decision in this layer traces back to at least one of these principles.

---

## 1. Every Agent Has a Human Owner

**Statement:** Every autonomous agent operating in the enterprise must have a named individual who is accountable for the agent's behavior, access, compliance posture, and lifecycle.

**Rationale:**
Agents act with delegated authority, but authority without accountability is a governance vacuum. When an agent takes an action that causes harm—whether a data exposure, an unauthorized system modification, or a cost overrun—there must be one person who is answerable. Assigning ownership to a team alias, a shared role, or nobody at all guarantees that incidents have no clear responder and that agents drift from their intended purpose without anyone noticing.

**Implications:**
- The agent registry includes a mandatory "owner" field that maps to a specific individual, not a team name or distribution list.
- Ownership transfers are formal governance events that require acknowledgment by the receiving individual and review-board notification.
- Agents whose owners leave the organization or change roles are automatically flagged for ownership reassignment within a defined grace period; agents that exceed the grace period without a valid owner are suspended.

**Violations:**
- An agent operates in production for months after its creator leaves the company, with no one monitoring its behavior or reviewing its access.
- An incident involving agent-initiated actions cannot be investigated because no individual has context on the agent's design intent, authority scope, or tool integrations.
- Cost accumulates on an agent workload that no one actively manages because ownership was assigned to a department rather than a person.

---

## 2. Agent Permissions Never Exceed the Owner's Permissions

**Statement:** The set of tools, data, and systems an agent may access must be a strict subset of what its human owner is authorized to access.

**Rationale:**
An agent acts on behalf of its owner, and delegation cannot create authority that the delegator does not possess. If an agent can access systems its owner cannot, the agent has become a privilege-escalation vector—one that bypasses every access-control decision the organization has made about that human's authority. This principle ensures that the existing human authorization model remains the ceiling for agent permissions, regardless of the tools the agent is technically capable of invoking.

**Implications:**
- Agent authorization policies reference the owner's entitlements as an upper bound; any tool or data scope the owner lacks is denied to the agent by default.
- Changes to the owner's access (revocations, role changes, offboarding) automatically trigger a re-evaluation of the agent's permissions.
- Agents that require access beyond their owner's scope must be re-assigned to an owner who holds the necessary entitlements, with appropriate governance review.

**Violations:**
- An agent invokes administrative APIs that its owner has no authorization to call, effectively granting the owner elevated privileges through the agent.
- An owner's access is revoked during an investigation, but the agent continues operating with the previously granted permissions because the systems are not linked.
- Teams assign agent ownership to the person with the broadest access specifically to maximize the agent's permissions, undermining the intent of least privilege.

---

## 3. Every Agent Action Is Traceable

**Statement:** Every action an agent takes must be traceable through an unbroken delegation chain from the originating human event or system trigger, through every agent and tool invocation, to the final outcome.

**Rationale:**
Agents make multi-step decisions that span multiple tools, systems, and potentially other agents. Without end-to-end traceability, the organization cannot determine why an action was taken, who authorized it, or what sequence of decisions led to an outcome. This makes incident investigation, compliance auditing, and root-cause analysis impossible. The delegation chain is the accountability backbone of agent-based architectures.

**Implications:**
- Every agent invocation carries a correlation identifier that links it to the originating delegation event and propagates through all downstream tool calls and agent-to-agent handoffs.
- Audit logs capture not just the action taken but the full context: which agent, which tool, which parameters, which delegation chain, and which human owner is ultimately accountable.
- Delegation chains are immutable once written; they are stored in tamper-evident logging infrastructure that supports forensic investigation.

**Violations:**
- An agent modifies a production database, but the audit trail only shows the tool invocation with no link to the agent, the delegation chain, or the human who initiated the workflow.
- Agent-to-agent delegation breaks the correlation chain, making it impossible to determine which originating event led to a downstream action.
- Logging is implemented at the tool layer but not at the agent orchestration layer, creating gaps in the delegation chain.

---

## 4. No Standing Access

**Statement:** Agents must receive credentials just-in-time for each tool invocation and must not hold persistent secrets, long-lived tokens, or standing access to any system.

**Rationale:**
Long-lived credentials are the most common vector for unauthorized access in machine-identity scenarios. An agent with standing access to a database, an API, or a file system carries those credentials for the duration of its operation—often indefinitely. If the agent is compromised, if its execution environment is breached, or if the agent's purpose changes, those credentials remain valid and exploitable. Just-in-time credential issuance limits the window of exposure to the duration of a single operation and ensures that credentials are always scoped to the specific action being performed.

**Implications:**
- Agent execution environments do not store secrets in configuration files, environment variables, or embedded code; credentials are injected at invocation time by the platform's credential-management infrastructure.
- Issued credentials have a maximum time-to-live measured in minutes, not hours or days, and are automatically revoked upon task completion or timeout.
- Credential-issuance events are logged as part of the delegation chain, creating an auditable record of what credentials were granted, to which agent, for what purpose, and for how long.

**Violations:**
- An agent's configuration file contains hard-coded API keys that have not been rotated in months and grant access far beyond the agent's current purpose.
- Credentials issued to an agent remain valid after the agent's task completes, leaving an exploitable window if the execution environment is later compromised.
- Teams store shared secrets in the agent's runtime environment for convenience, bypassing the platform's credential-management controls entirely.

---

## 5. Discovery Before Control

**Statement:** You cannot secure agents you do not know about; therefore, continuous discovery of all agent-like activity in the environment must precede and complement registration-based controls.

**Rationale:**
Registration-based controls work only for agents that are voluntarily registered. In practice, teams build autonomous loops—scripts that call model endpoints in iterative patterns, workflows that chain tool invocations, automations that act on model outputs—without recognizing these as "agents" that fall under the agent security framework. If the only control mechanism is the registry, these shadow agents operate with no identity, no access controls, no audit trail, and no lifecycle management. Discovery closes the gap between what the registry knows and what actually exists.

**Implications:**
- The platform continuously monitors for agent-like behavioral patterns: iterative model calls from a single identity, multi-tool invocation chains, automated actions triggered by model outputs, and long-running sessions with tool-calling patterns.
- Discovered, unregistered agent-like activity triggers an investigation workflow that classifies the activity and either onboards it into the registry or terminates it.
- Discovery metrics (number of shadow agents found, time to remediation) are reported to the governance review board as a leading indicator of agent security posture.

**Violations:**
- The organization believes it has twelve registered agents, but network analysis reveals forty-seven autonomous loops making tool-calling patterns that are not associated with any registered identity.
- A team builds an automated research workflow that queries sensitive databases in a loop, and no one identifies it as an agent until a data-handling incident occurs.
- Shadow agent discovery is treated as a one-time exercise rather than a continuous capability, allowing new unregistered agents to accumulate between scans.

---

## 6. Authorization at the Tool AND Parameter Level

**Statement:** Controlling which tools an agent can invoke is necessary but not sufficient; authorization must also constrain the parameter values the agent may pass to those tools.

**Rationale:**
Tool-level access control answers the question "Can this agent call this tool?" but ignores the equally critical question "What can this agent do with this tool?" An agent authorized to query a database can query any table, any column, any row unless parameter-level policies constrain it. An agent authorized to send messages can send to any recipient unless the recipient parameter is scoped. Tool-level authorization alone creates a false sense of control: the agent appears restricted, but its effective permissions within each tool are unbounded.

**Implications:**
- Authorization policies define permitted parameter ranges, not just permitted tools: which tables, which columns, which recipient lists, which file paths, which API scopes.
- Parameter-level policies are enforced at the point of tool invocation, before the request reaches the downstream system, so that violations are caught by the agent security layer rather than relying on the downstream system's own access controls.
- Default-deny semantics apply to parameters as well as tools: if a parameter is not explicitly permitted by policy, the invocation is blocked.

**Violations:**
- An agent authorized to "read customer records" queries the entire customer database including payment information, because the authorization only checked tool access without scoping the query parameters.
- Parameter-level policies are defined but enforced only at the downstream system, which has a different and more permissive authorization model than the agent security layer intended.
- Teams define tool-level access controls and treat agent authorization as complete, without recognizing that the most sensitive decisions occur at the parameter level.

---

## 7. Agent-to-Agent Communication Requires Mutual Authentication

**Statement:** When one agent communicates with, delegates to, or receives instructions from another agent, both parties must authenticate their identities before any data or authority is exchanged.

**Rationale:**
Agent-to-agent communication introduces a trust boundary that does not exist in single-agent architectures. Without mutual authentication, an agent has no way to verify that the agent it is communicating with is who it claims to be. This opens the door to impersonation attacks, where a malicious or compromised agent poses as a trusted peer to extract data, escalate privileges, or inject malicious instructions into a delegation chain. Mutual authentication ensures that both sides of every agent-to-agent interaction are verified before trust is extended.

**Implications:**
- Every agent-to-agent communication channel requires both the initiating and receiving agents to present verifiable identity credentials before data or delegation is exchanged.
- The authentication mechanism supports credential revocation so that a compromised agent can be immediately excluded from the communication fabric without disrupting other agents.
- Delegation chain tracking records the authenticated identities of both agents at each handoff point, ensuring the chain of trust is verifiable end-to-end.

**Violations:**
- An agent receives a delegation request from another agent and executes it without verifying the caller's identity, because the communication protocol does not enforce authentication by default.
- A compromised agent impersonates a privileged agent and instructs a downstream agent to perform unauthorized actions; the downstream agent complies because it has no mechanism to verify the caller.
- Agent-to-agent communication relies on network-level trust (same network segment, same cluster) rather than identity-based authentication, making impersonation trivial for any actor with network access.
