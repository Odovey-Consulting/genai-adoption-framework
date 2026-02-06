# Organizational Readiness

Technology adoption fails more often from organizational misalignment than from technical deficiency. The GenAI Adoption Framework's five layers require specific team structures, skills, and leadership commitment. This document defines who owns what, what they need to know, how to introduce the framework, and how team composition evolves across maturity stages.

## Team Structures

Four organizational functions own the framework's implementation. Their boundaries align with the layer architecture to minimize ambiguity about ownership.

### Platform Team

**Owns:** Layer 1 (Governance infrastructure), Layer 2 (Gateway), Layer 3 (Operations).

The platform team builds and operates the shared infrastructure that all AI workloads depend on. This includes the gateway that routes and controls model access, the operational tooling that provides observability and cost management, and the technical implementation of governance policies. The platform team does not build AI applications; it builds the platform that makes AI applications safe, observable, and governable.

At Foundation maturity, the platform team may be two to three engineers with part-time allocation. At Optimized maturity, this is a dedicated team of six to twelve engineers with specialized roles in gateway engineering, observability, and infrastructure automation.

### AI Center of Excellence (CoE)

**Owns:** Cross-cutting concerns, standards, training, and framework evolution.

The CoE is not a delivery team. It does not build workloads or operate infrastructure. It defines standards, curates best practices, conducts training, facilitates knowledge sharing across workload teams, and maintains the framework itself. The CoE serves as the connective tissue between the platform team, workload teams, and security team.

At Foundation maturity, the CoE may be a single senior practitioner who sets initial standards and conducts knowledge-sharing sessions. At Optimized maturity, it includes dedicated roles for AI ethics review, evaluation methodology, prompt engineering standards, and training program management.

### Workload Teams

**Own:** Layer 4 (Workloads).

Workload teams are the application developers who build AI-powered features and products. Each workload team owns their application's prompt engineering, evaluation pipelines, output validation, human-in-the-loop workflows, and retrieval-augmented generation configurations. Workload teams consume the platform that Layers 1 through 3 provide and conform to the standards that the CoE defines.

The number of workload teams scales with the number of AI applications. At Foundation maturity, one or two pilot teams build the first workloads. At Optimized maturity, dozens of workload teams operate independently within the guardrails that the platform provides.

### Security Team

**Owns:** Layer 5 (Agent Security). **Contributes to:** Layer 3 (Operations security controls).

The security team extends existing security practices to cover generative AI-specific threats. This includes prompt injection defense, agent permission boundary enforcement, adversarial testing, credential management for model integrations, and security monitoring for AI-specific attack patterns. The security team works closely with the platform team on Layer 3 operational security and with the CoE on security training.

At Foundation maturity, AI security is a part-time responsibility within the existing security team. At Optimized maturity, dedicated AI security engineers focus full-time on agent security, adversarial testing, and AI-specific threat modeling.

## Skills Assessment Matrix

Each team requires a distinct skill set. The table below identifies core skills, proficiency levels, and which maturity stages demand them.

| Skill | Platform Team | CoE | Workload Teams | Security Team | Required From |
|---|---|---|---|---|---|
| API gateway design and operation | Expert | Awareness | Awareness | Intermediate | Foundation |
| Model API integration patterns | Expert | Expert | Expert | Intermediate | Foundation |
| Prompt engineering | Intermediate | Expert | Expert | Intermediate | Foundation |
| Evaluation and benchmarking | Intermediate | Expert | Expert | Intermediate | Operational |
| Retrieval-augmented generation | Intermediate | Expert | Expert | Awareness | Operational |
| Observability and monitoring | Expert | Intermediate | Intermediate | Intermediate | Foundation |
| Cost modeling and optimization | Expert | Intermediate | Awareness | Awareness | Operational |
| Data classification and handling | Intermediate | Expert | Intermediate | Expert | Foundation |
| AI-specific threat modeling | Awareness | Intermediate | Awareness | Expert | Foundation |
| Prompt injection defense | Intermediate | Intermediate | Intermediate | Expert | Operational |
| Agent framework architecture | Expert | Expert | Expert | Expert | Operational |
| Agent permission design | Intermediate | Expert | Intermediate | Expert | Operational |
| Adversarial testing | Awareness | Intermediate | Intermediate | Expert | Operational |
| Fine-tuning and model customization | Intermediate | Expert | Intermediate | Awareness | Optimized |
| AI ethics and bias assessment | Awareness | Expert | Intermediate | Awareness | Operational |
| Regulatory and compliance mapping | Awareness | Expert | Awareness | Intermediate | Foundation |
| Change management | Awareness | Expert | Awareness | Awareness | Foundation |

Proficiency levels: **Expert** means the team can design, implement, and troubleshoot independently. **Intermediate** means the team can implement with guidance and participate in design decisions. **Awareness** means the team understands the concepts sufficiently to collaborate with experts and identify when the skill is needed.

## Training Paths

### Platform Engineers

1. **Foundation.** Model API integration patterns, authentication and authorization for model endpoints, basic prompt structure and token economics.
2. **Gateway specialization.** Request routing, load balancing across model providers, data classification enforcement at the gateway, PII detection and redaction pipeline design.
3. **Operations specialization.** AI-specific observability (latency distributions, token usage, cost attribution), drift detection, alerting design for probabilistic systems, log redaction automation.
4. **Advanced.** Multi-provider abstraction design, capacity planning for consumption-based services, automated failover, gateway policy-as-code.

### Workload Developers

1. **Foundation.** Prompt engineering fundamentals, model capability and limitation awareness, retrieval-augmented generation patterns, output parsing and validation.
2. **Quality engineering.** Evaluation pipeline design, benchmark creation, regression testing for non-deterministic systems, human evaluation methodology.
3. **Production readiness.** Human-in-the-loop workflow design, graceful degradation patterns, cost-aware prompt design, output caching strategies.
4. **Advanced.** Agent design patterns, tool integration, multi-step reasoning chains, fine-tuning workflows, complex retrieval architectures.

### Security Engineers

1. **Foundation.** AI threat landscape overview, prompt injection taxonomy, common attack vectors against generative AI systems.
2. **Defense engineering.** Input validation for model endpoints, agent permission boundary design, credential management for AI integrations, adversarial prompt testing methodology.
3. **Agent security.** Agent delegation chain analysis, privilege escalation vectors in tool-using agents, behavioral monitoring for autonomous systems, shadow agent detection.
4. **Advanced.** Red team methodology for AI systems, automated adversarial testing pipelines, security architecture review for complex agent systems.

## Change Management

Introducing the GenAI Adoption Framework is an organizational change, not a technology deployment. The following approach reduces resistance and accelerates adoption.

**Start with the problem, not the framework.** Teams that are already experiencing shadow AI, cost surprises, or compliance anxiety are receptive to structure. Teams that have not yet felt pain will resist process overhead. Begin adoption with the teams that need it most.

**Implement layers incrementally.** Do not attempt to deploy all five layers simultaneously. Start with Layer 1 (minimal governance: an AI system inventory and acceptable use policy) and Layer 2 (a basic gateway for routing and logging). Add layers as workloads and maturity demand them.

**Make the platform team's value visible early.** The gateway should provide tangible benefits to workload teams within the first weeks: simplified model access, centralized credential management, usage visibility. If the platform is perceived as overhead rather than enablement, adoption stalls.

**Empower the CoE to teach, not police.** The CoE's early credibility comes from helping teams build better workloads, not from rejecting their proposals. Enforcement comes later, after trust is established and standards are proven.

**Create feedback loops.** Workload teams should have a clear channel to propose changes to platform capabilities, standards, and governance policies. One-way mandates breed shadow AI.

## Executive Sponsorship

Executive leadership must provide certain things directly and can delegate others.

**Leadership must provide directly:**

- A clear mandate that production AI workloads must use governed channels (eliminating the legitimacy of shadow AI).
- Budget allocation for platform team and CoE headcount, distinct from workload team budgets.
- Organizational authority for the CoE to set cross-team standards.
- Visible engagement with responsible AI outcomes, not just productivity metrics.
- Decision-making when cross-cutting tradeoffs (see [tradeoffs.md](tradeoffs.md)) reach an impasse between teams.

**Leadership can delegate:**

- Technical architecture decisions within the framework's structure.
- Model selection and evaluation criteria.
- Workload prioritization within individual teams.
- Operational runbook design and incident response procedures.
- Training curriculum specifics.

## Maturity-Based Team Scaling

Team size and structure should match the organization's maturity stage. Premature scaling creates overhead. Delayed scaling creates bottlenecks.

| Function | Foundation | Operational | Optimized |
|---|---|---|---|
| Platform Team | 2-3 engineers (part-time OK) | 4-6 dedicated engineers | 8-12 engineers with specialized roles |
| CoE | 1 senior practitioner (part-time OK) | 2-3 dedicated practitioners | 4-6 practitioners with specialized roles |
| Workload Teams | 1-2 pilot teams | 3-8 teams with varying experience | 10+ teams operating independently |
| Security Team (AI focus) | 1 engineer (part-time, within existing security team) | 1-2 dedicated AI security engineers | 3-5 AI security engineers with red team capability |
| Executive Sponsor | Named sponsor with quarterly engagement | Named sponsor with monthly engagement | Named sponsor with ongoing engagement plus AI steering committee |

The transition from one maturity stage to the next is not primarily a technology upgrade. It is an organizational scaling event. The platform, standards, and security infrastructure must scale ahead of workload team growth. If workload teams outpace platform capability, they route around the framework and shadow AI grows.
