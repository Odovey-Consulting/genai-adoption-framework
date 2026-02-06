# Tradeoffs

Frameworks create the illusion that all good things are simultaneously achievable. They are not. Generative AI adoption forces genuine tradeoffs where optimizing for one principle degrades another. Pretending these tensions do not exist leads to unstated preferences that drive inconsistent decisions. This document names the five most common tradeoffs, describes when to favor each side, and identifies the warning signs that you have over-indexed.

## 1. Security vs. Developer Velocity

**The tension.** Every security control adds friction to the development workflow. Gateway-level input validation adds latency. Prompt review processes add calendar time. Agent permission boundaries restrict what developers can build. Security teams that move slowly become bottlenecks. Development teams that move fast bypass controls.

**When to prioritize security.** When workloads handle sensitive data, interact with production systems, serve external customers, or involve agents with write access to consequential resources. When the blast radius of a security failure exceeds the cost of delayed delivery. When regulatory obligations mandate specific security controls regardless of velocity impact.

**When to prioritize velocity.** When workloads operate in sandbox environments with synthetic data. During early experimentation and prototyping phases where the output is evaluated by humans before any downstream impact. When the cost of delay (competitive pressure, time-sensitive business need) is concrete and the security risk is speculative.

**Signals you have over-indexed on security.** Developers route around the governed platform because it is too slow or too restrictive. Shadow AI usage increases despite policy prohibitions. Proof-of-concept timelines extend to months. Security review queues grow while workload teams idle. The platform team spends more time on access request tickets than on engineering.

**Signals you have over-indexed on velocity.** Security reviews are skipped for production workloads. Agent permissions are set to maximally permissive defaults because nobody has time to scope them properly. The security team learns about new AI deployments from monitoring alerts rather than from planning discussions. Post-incident reviews reveal controls that were known to be needed but never implemented.

## 2. Centralization vs. Team Autonomy

**The tension.** Centralized control through the gateway and platform team ensures consistency, security, and cost visibility. But centralization creates bottlenecks, slows down teams that know what they need, and forces one-size-fits-all solutions on workloads with different requirements. Full autonomy lets teams move fast but fragments the technology landscape, duplicates effort, and makes governance impossible.

**When to prioritize centralization.** When establishing the framework for the first time. When the organization lacks mature AI practices and needs guardrails to prevent costly mistakes. When compliance requirements demand uniform controls. When cost management requires visibility that only centralized routing can provide. When the number of workload teams is small enough that a central team can serve them without becoming a bottleneck.

**When to prioritize autonomy.** When workload teams have demonstrated competence through the framework's earlier maturity stages. When teams have specialized requirements that the central platform cannot efficiently support. When the platform team's approval queue becomes the primary constraint on delivery. When innovation requires experimentation with model capabilities, prompt patterns, or agent architectures that the central platform does not yet support.

**Signals you have over-indexed on centralization.** Workload teams describe the platform as an obstacle rather than an enabler. Feature requests to the platform team accumulate faster than they are resolved. Teams with legitimate specialized needs cannot get exceptions processed. Innovation happens outside the framework because the framework cannot accommodate it.

**Signals you have over-indexed on autonomy.** Multiple teams independently solve the same infrastructure problems. Cost attribution is impossible because there is no central routing. Security posture varies wildly across workloads. Compliance audits require team-by-team evidence collection. New hires must learn a different AI integration pattern for every team they interact with.

## 3. Cost vs. Capability

**The tension.** More capable models cost more per token and often require larger context windows that further increase cost. Cheaper models produce lower-quality outputs that require more post-processing, more human review, or more retry attempts. The cost difference between model tiers can be an order of magnitude or more. At scale, model selection is a significant budget line item.

**When to prioritize capability.** When output quality directly affects customer experience or business outcomes. When errors are expensive to detect and correct (medical, legal, financial contexts). When the workload involves complex reasoning that cheaper models fail at, causing downstream costs that exceed the model cost savings. When the volume is low enough that per-token cost is irrelevant relative to development cost.

**When to prioritize cost.** When the workload involves high volume, lower-stakes tasks (summarization of internal documents, draft generation for human editors, data extraction from structured sources). When cheaper models achieve acceptable quality as validated by evaluation pipelines. When budget constraints are real and the alternative to cost management is shutting down workloads. When the task can be decomposed so that expensive models handle only the components that require their capabilities.

**Signals you have over-indexed on capability.** Every workload uses the most expensive model available regardless of task complexity. Cost per workload is unknown or unmonitored. Monthly model costs surprise finance teams. No evaluation data exists to justify the quality difference between model tiers.

**Signals you have over-indexed on cost.** Output quality complaints from users increase. Human review rates climb because cheaper model outputs require more correction. Workload teams spend engineering time on elaborate prompt optimization to compensate for model limitations, and that engineering time costs more than the model cost savings. Customer-facing quality degrades measurably.

## 4. Speed-to-Market vs. Governance Maturity

**The tension.** The framework defines three maturity stages: crawl, walk, run. Full governance maturity takes time, often twelve to eighteen months for complex organizations. Competitive pressure demands AI capabilities now. Waiting for full maturity before deploying means competitors ship first. Shipping without governance means accumulating risk debt that compounds.

**When to prioritize speed.** When the competitive landscape demands AI capabilities and delay creates existential risk. When the initial workloads are low-risk (internal tooling, developer productivity) and can be deployed with minimal governance. When crawl-stage governance (acceptable use policy, basic gateway, usage logging) can be stood up in weeks, not months, and is sufficient for initial workloads.

**When to prioritize governance maturity.** When initial workloads involve customer-facing AI, sensitive data, or regulated activities. When the organization has experienced an AI-related incident and needs to restore trust. When regulatory timelines create hard deadlines for compliance. When the volume of planned workloads is high enough that governance debt will become unmanageable within months.

**Signals you have over-indexed on speed.** Production workloads exist with no governance review, no monitoring, and no assigned owner. The AI system inventory is incomplete or does not exist. The number of AI workloads exceeds the organization's ability to enumerate them. Risk assessment has never been performed for deployed workloads.

**Signals you have over-indexed on governance maturity.** No AI workloads have reached production despite months of framework development. Governance processes require weeks of review for low-risk internal workloads. Policy documents outnumber running applications. Teams perceive the framework as bureaucracy designed to prevent AI adoption rather than enable it.

## 5. Comprehensive Logging vs. Privacy

**The tension.** Effective operations, security monitoring, and compliance auditing require detailed logging of prompts, responses, model behavior, and user interactions. Comprehensive logs are also a privacy liability. Prompt-response pairs may contain personal data, proprietary information, or sensitive context. Retaining this data creates exposure in the event of a breach, may violate data retention obligations, and can conflict with user expectations about how their inputs are handled.

**When to prioritize comprehensive logging.** When workloads are in early deployment and behavior characterization requires full visibility. When security monitoring demands complete request-response pairs to detect prompt injection or data exfiltration. When regulatory obligations require demonstrable audit trails. When debugging non-deterministic model behavior requires the full context of what the model received and produced.

**When to prioritize privacy.** When prompts routinely contain personal data or sensitive information. When data retention regulations impose strict limits on how long interaction data can be stored. When users have a reasonable expectation that their inputs are not retained. When the volume of logged data creates a breach liability that exceeds the operational value of the logs.

**Signals you have over-indexed on logging.** Full prompt-response pairs are stored indefinitely with no redaction. Log storage costs grow without bounds. Privacy impact assessments have not been conducted for AI operational data. A data breach of log storage would expose personal data at scale. Teams treat logs as a data asset and query them for analytics purposes without considering the privacy implications.

**Signals you have over-indexed on privacy.** Incident investigation is hampered by insufficient log data. Security teams cannot reconstruct attack sequences. Model behavior drift goes undetected because operational data is too aggressively redacted. Compliance audits fail because evidence of control operation has been purged. Debugging production issues requires reproducing problems from scratch because no historical context exists.

## Using Tradeoff Analysis

These tradeoffs are not problems to be solved permanently. They are tensions to be managed continuously. The right balance shifts as the organization's maturity, risk profile, regulatory environment, and competitive landscape evolve. Revisit tradeoff decisions at each maturity stage transition and whenever the external context changes materially.

Document your tradeoff decisions explicitly. When leadership chooses to prioritize speed over governance maturity for a specific initiative, record that decision, the reasoning, and the conditions under which it should be revisited. Implicit tradeoff decisions made under time pressure and never documented become invisible risk.
