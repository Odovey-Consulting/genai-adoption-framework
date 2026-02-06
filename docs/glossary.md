# Glossary

Terms are defined as used within the GenAI Adoption Framework (GAF). Definitions are concise, practical, and consistent with framework usage.

---

**Acceptable Use Policy (AUP)**
A governance artifact that defines what constitutes permitted and prohibited use of generative AI within an organization. The AUP covers topics such as approved use cases, prohibited data types, required human oversight, and consequences for violations.

**Agent**
A software entity that uses one or more generative AI models to autonomously plan and execute multi-step tasks, typically by invoking tools, retrieving data, and making decisions within a defined scope of authorization.

**Agent Identity**
A distinct, non-human identity assigned to an agent for the purposes of authentication, authorization, and audit. Agent identities are governed with the same rigor as service accounts, including scoped permissions, credential rotation, and activity logging.

**Air-gapped Environment**
A deployment configuration in which generative AI workloads operate with no connectivity to external networks or third-party services. All models, data, and tooling must be hosted internally. Used for workloads that process highly sensitive or regulated data.

**Audit Trail**
A chronological, tamper-resistant record of all significant actions taken by or through a generative AI system, including model invocations, tool calls, policy decisions, access grants, and configuration changes. Audit trails support compliance, incident investigation, and accountability.

**Classification Tier**
A label assigned to a workload or data set that reflects its sensitivity and risk profile. Classification tiers determine which governance controls, model access policies, and deployment environments apply. Tiers are defined in the governance layer and enforced across all other layers.

**Content Filtering**
The process of inspecting model inputs and outputs against defined rules to detect and block prohibited content such as personally identifiable information, toxic language, or policy-violating material. Content filtering may operate at the gateway layer, the application layer, or both.

**Cost Governance**
The set of policies, controls, and monitoring practices that ensure generative AI spending remains within approved budgets. Cost governance includes per-workload quotas, rate limits, alerting thresholds, and chargeback mechanisms.

**Data Leakage**
The unintended exposure of sensitive, confidential, or regulated information through a generative AI system. Data leakage can occur through model inputs (sending sensitive data to an external model), model outputs (the model revealing information from its training data or context), or logging pipelines that capture sensitive content.

**Data Loss Prevention (DLP)**
A category of controls that detect, classify, and prevent sensitive data from leaving authorized boundaries. In the framework, DLP applies to both the data sent to models and the data included in model outputs, and operates as a component of content filtering and gateway enforcement.

**Default Deny**
A security posture in which access to any resource (models, tools, data, infrastructure) is denied unless an explicit, governed grant exists. Default deny is both a framework principle and an operational configuration applied across all layers.

**Delegation Chain**
The traceable sequence of authorization from a human owner to an agent, and potentially from that agent to sub-agents, that establishes who is accountable for each action in a multi-step workflow. Every link in the chain must be explicitly authorized and logged.

**Developer Enablement**
The practice of providing developers with self-service tools, documentation, templates, and sandboxed environments that make it easy to build generative AI applications within governed boundaries. Developer enablement reduces friction and discourages the use of unsanctioned alternatives.

**Evaluation Scorecard**
A structured document that records the results of evaluating a model or agent against a defined set of quality, safety, and performance criteria using a golden dataset. Scorecards are versioned and stored as governance artifacts.

**Fail Closed**
A system behavior in which, when a control or service fails, the default response is to deny the request rather than allow it through. Fail closed is the expected behavior for security and policy enforcement components within the framework.

**Gateway**
An infrastructure component that sits between AI consumers (applications, agents, users) and AI model endpoints. The gateway enforces routing rules, access policies, rate limits, content filters, and logging. It is the primary enforcement point for the traffic management layer.

**Golden Dataset**
A curated, version-controlled set of test inputs and expected outputs used to evaluate model quality, detect regressions, and validate that guardrails function correctly. Golden datasets are maintained as governance artifacts and updated as requirements evolve.

**Governance**
The system of policies, processes, roles, and controls that an organization uses to manage the risks and maximize the value of its generative AI adoption. In the framework, governance is both a dedicated layer and a cross-cutting concern.

**Guardrail**
A control that constrains the behavior of a generative AI system to keep it within acceptable bounds. Guardrails may operate on inputs, outputs, or agent actions, and may be implemented as content filters, tool restrictions, output validators, or policy rules.

**Hallucination**
A model output that presents fabricated, incorrect, or unsubstantiated information as if it were factual. Hallucinations are a fundamental risk in generative AI systems and are managed through output validation, grounding techniques, and human review processes.

**Human-in-the-Loop**
A workflow design in which a human reviews, approves, or modifies an AI-generated output or action before it takes effect. Human-in-the-loop is required for high-risk decisions and is specified as a control in the governance layer based on workload classification.

**Just-in-Time Credentials**
Short-lived authentication credentials issued to an agent or service at the moment they are needed and automatically revoked after use or expiration. Just-in-time credentials reduce the risk associated with persistent, long-lived secrets.

**Lens (Framework)**
A specialized perspective applied to the framework that adds domain-specific guidance for a particular context, such as a regulated industry, a specific deployment model, or a particular risk profile. Lenses extend the base framework without modifying it.

**Maturity Stage**
A defined level of organizational readiness and sophistication in generative AI adoption. The framework defines progressive maturity stages, each prescribing which controls, processes, and capabilities are appropriate. Organizations advance through stages incrementally.

**Model Catalog**
A governed registry of all generative AI models approved for use within the organization. The catalog records each model's capabilities, classification tier, approved use cases, known limitations, and access policies.

**Model Routing**
The process by which the gateway directs a request to a specific model endpoint based on policy rules, workload classification, cost constraints, latency requirements, or availability. Model routing enables abstraction between consumers and providers.

**Mutual Authentication**
A security mechanism in which both parties in a communication (for example, an application and a gateway, or a gateway and a model endpoint) verify each other's identity before exchanging data. Mutual authentication prevents impersonation and man-in-the-middle attacks.

**Observability**
The ability to understand the internal state and behavior of a generative AI system by examining its external outputs, including logs, metrics, and traces. Observability is a framework principle and a capability that must be designed into every layer from the start.

**Over-privileged Identity**
A human, service, or agent identity that possesses more access rights than required for its current tasks. Over-privileged identities increase blast radius in the event of compromise or misconfiguration and violate the principle of least privilege.

**Parameter-Level Authorization**
A fine-grained access control mechanism that restricts not just which tools an agent may invoke, but which specific parameters it may supply. For example, an agent may be authorized to call a search tool but restricted from using certain filter values.

**Platform Team**
The organizational team responsible for building, operating, and maintaining the shared generative AI infrastructure that application teams consume. The platform team owns the gateway, model catalog, telemetry pipelines, and developer enablement tooling.

**Prompt Engineering**
The practice of designing, testing, and iterating on the text instructions (prompts) provided to generative AI models to achieve desired behavior, quality, and safety characteristics. Prompt templates are treated as versioned artifacts within the framework.

**Prompt Injection**
An attack technique in which malicious instructions are embedded in model inputs (directly or through retrieved content) in an attempt to override the model's intended behavior, bypass guardrails, or extract sensitive information.

**Provider Abstraction**
An architectural pattern in which applications interact with a common interface rather than directly with a specific model provider's API. Provider abstraction enables model routing, portability, and the ability to change providers without modifying application code.

**Rate Limiting**
A control that restricts the number of requests an identity or workload can make to model endpoints within a defined time window. Rate limiting protects against runaway costs, denial-of-service conditions, and abuse.

**Reference Architecture**
A documented, opinionated system design that illustrates how the framework's layers, components, and controls fit together in a deployable configuration. Reference architectures are starting points that organizations adapt to their specific context.

**Shadow AI**
The use of generative AI tools or services by individuals within an organization without the knowledge, approval, or governance of the platform or security teams. Shadow AI creates unmanaged risk, data leakage exposure, and compliance gaps.

**Shadow Agent**
An autonomous AI agent deployed by an individual or team without going through the organization's governed onboarding process. Shadow agents are a specific and high-risk form of shadow AI because they can take autonomous actions, invoke tools, and access data without oversight.

**Structured Telemetry**
Logs, metrics, and traces emitted in a consistent, machine-parseable schema with correlation identifiers that enable automated analysis, alerting, and auditing. Structured telemetry is distinct from unstructured log files and is required by the observability-by-design principle.

**Tool-Level Access Control**
A control that restricts which tools (APIs, functions, external services) an agent is authorized to invoke. Tool-level access is defined per agent identity and per workload, and is enforced at the orchestration or gateway layer.

**Workload**
A distinct generative AI use case that has been formally registered, classified, and authorized within the governance framework. A workload has a defined purpose, a human owner, a classification tier, and a set of approved resources.

**Workload Brief**
A structured governance artifact completed during onboarding that captures a workload's purpose, data requirements, risk profile, model needs, access requirements, and human owner. The workload brief is the primary input to the classification and approval process.
