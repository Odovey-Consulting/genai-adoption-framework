# Risk Management

Generative AI introduces risk categories that traditional software risk frameworks do not adequately cover. Models behave probabilistically, not deterministically. Outputs cannot be fully predicted from inputs. Autonomous agents extend the blast radius of failures beyond what conventional access control models anticipate. This document establishes a risk taxonomy specific to generative AI, maps each risk to the framework layers responsible for mitigation, and provides a risk assessment template.

## Risk Taxonomy

### Model Risks

**Hallucination.** Models generate plausible but factually incorrect outputs. This risk exists in every generative AI deployment and cannot be eliminated through model selection alone. Severity scales with the degree to which downstream consumers treat model output as authoritative.

**Bias.** Models reproduce and amplify biases present in training data. Bias manifests in content generation, classification, summarization, and recommendation. It affects both customer-facing outputs and internal decision support.

**Capability degradation.** Model behavior changes across versions, fine-tuning iterations, or provider-side updates. A model that passed evaluation last quarter may fail the same evaluation today. Degradation is often silent: outputs remain syntactically valid but semantically wrong.

**Training data contamination.** Models trained or fine-tuned on data that includes proprietary information, copyrighted material, or poisoned samples. Contamination may originate from upstream providers or from internal fine-tuning pipelines that ingest unvetted data.

### Data Risks

**Data leakage.** Sensitive data transmitted to model providers through prompts, context windows, or fine-tuning pipelines. Leakage occurs when developers embed production data in prompts during debugging, when retrieval pipelines surface documents above the user's clearance, or when logging captures full prompt-response pairs.

**Classification violation.** Data processed at an inappropriate classification level. A model endpoint approved for internal data receives prompts containing restricted data. A retrieval pipeline indexes documents across classification boundaries without enforcing access controls at query time.

**PII exposure.** Personally identifiable information surfaces in model outputs, logs, or cached responses. PII may originate from training data, retrieval context, or user-provided prompts. Exposure creates regulatory liability and erodes user trust.

**Exfiltration via prompts.** Adversarial or accidental prompt construction that causes models to reveal information from their context window, system prompts, or retrieval-augmented sources. This vector is distinct from traditional data exfiltration because it exploits the model's instruction-following behavior rather than a software vulnerability.

### Security Risks

**Prompt injection.** Malicious instructions embedded in user inputs, retrieved documents, or tool outputs that override system-level instructions. Prompt injection is the most broadly applicable attack vector against generative AI systems.

**Adversarial manipulation.** Crafted inputs designed to elicit specific undesirable behaviors: bypassing safety filters, extracting system prompts, causing the model to execute unintended actions, or producing outputs that appear benign to automated checks but carry malicious payloads.

**Credential theft.** Agents or integrations configured with credentials that can be exfiltrated through prompt manipulation, verbose error messages, or insecure tool interfaces.

**Shadow AI.** Unauthorized use of generative AI services by employees outside governed channels. Shadow AI bypasses every layer of the framework simultaneously: no governance review, no gateway routing, no operational monitoring, no workload standards, no security controls.

### Operational Risks

**Vendor lock-in.** Deep dependency on a single model provider's APIs, prompt formats, fine-tuning infrastructure, or embedding spaces. Lock-in increases switching costs and reduces negotiating leverage. It also creates single points of failure.

**Cost overrun.** Consumption-based pricing models combined with unpredictable usage patterns create budgeting challenges. Agentic workloads that make recursive calls, workloads with large context windows, and retrieval-heavy pipelines are particularly prone to cost surprises.

**Model deprecation.** Providers retire model versions, sometimes with limited notice. Workloads pinned to specific model versions must migrate. Workloads that depend on undocumented model behaviors may break silently on migration.

**Availability.** Model provider outages disrupt all dependent workloads simultaneously. Rate limits, capacity constraints, and regional availability gaps affect reliability in ways that differ from traditional infrastructure failures.

### Agent Risks

**Autonomy drift.** Agents incrementally expand their operational scope beyond intended boundaries. An agent authorized to draft emails begins sending them. An agent authorized to query databases begins modifying records. Drift occurs when guardrails are defined imprecisely or when the agent interprets ambiguous instructions broadly.

**Privilege escalation.** Agents acquire access to resources beyond their intended scope through tool chaining, credential inheritance, or exploitation of overly permissive integrations. Unlike traditional privilege escalation, agent-based escalation may follow a valid logical path that no individual step would flag as unauthorized.

**Delegation chain breaks.** Multi-agent systems where one agent delegates tasks to another lose accountability when the chain is not fully traced. A supervising agent delegates a subtask, the subordinate agent fails or behaves unexpectedly, and the supervising agent cannot detect or recover from the failure.

**Shadow agents.** Agents deployed outside governed pipelines, often by teams experimenting with agent frameworks before formal adoption. Shadow agents combine the risks of shadow AI with the amplified blast radius of autonomous execution.

## Risk Assessment Matrix

Assess each identified risk using three dimensions: likelihood of occurrence, impact if realized, and which framework layer is the primary mitigation point.

| Risk | Likelihood | Impact | Primary Layer | Secondary Layer |
|---|---|---|---|---|
| Hallucination | High | Medium-High | Layer 4: Workloads | Layer 1: Governance |
| Bias | Medium | High | Layer 4: Workloads | Layer 1: Governance |
| Capability degradation | Medium | Medium | Layer 3: Operations | Layer 4: Workloads |
| Training data contamination | Low | High | Layer 1: Governance | Layer 4: Workloads |
| Data leakage | Medium | High | Layer 2: Gateway | Layer 3: Operations |
| Classification violation | Medium | High | Layer 2: Gateway | Layer 1: Governance |
| PII exposure | Medium | High | Layer 2: Gateway | Layer 3: Operations |
| Exfiltration via prompts | Medium | High | Layer 5: Agent Security | Layer 2: Gateway |
| Prompt injection | High | High | Layer 5: Agent Security | Layer 2: Gateway |
| Adversarial manipulation | Medium | Medium-High | Layer 5: Agent Security | Layer 4: Workloads |
| Credential theft | Low-Medium | Critical | Layer 5: Agent Security | Layer 2: Gateway |
| Shadow AI | High | Medium | Layer 1: Governance | Layer 2: Gateway |
| Vendor lock-in | Medium | Medium | Layer 1: Governance | Layer 2: Gateway |
| Cost overrun | Medium-High | Medium | Layer 3: Operations | Layer 2: Gateway |
| Model deprecation | Medium | Medium | Layer 3: Operations | Layer 4: Workloads |
| Availability | Medium | Medium-High | Layer 3: Operations | Layer 2: Gateway |
| Autonomy drift | Medium | High | Layer 5: Agent Security | Layer 1: Governance |
| Privilege escalation | Low-Medium | Critical | Layer 5: Agent Security | Layer 2: Gateway |
| Delegation chain breaks | Medium | Medium-High | Layer 5: Agent Security | Layer 3: Operations |
| Shadow agents | Medium | High | Layer 1: Governance | Layer 5: Agent Security |

## Risk Mitigation Mapping

Each framework layer contributes specific mitigation capabilities. The table below maps mitigation mechanisms to layers.

**Layer 1: Governance.** Defines acceptable use policies, data classification requirements, model approval processes, and risk tolerance thresholds. Governance does not prevent risks directly but establishes the rules that other layers enforce. Governance owns the risk register and the escalation process.

**Layer 2: Gateway.** Enforces data flow controls at the boundary between internal systems and model endpoints. The gateway is the primary control point for data leakage, classification violations, and PII filtering. It also provides the abstraction layer that reduces vendor lock-in.

**Layer 3: Operations.** Provides observability into model behavior, cost tracking, performance monitoring, and drift detection. Operations detects risks that other layers prevent. It generates the audit trail that governance and compliance require.

**Layer 4: Workloads.** Implements application-level controls including output validation, grounding verification, bias testing, evaluation pipelines, and human-in-the-loop workflows. Workload-level controls are the last line of defense before outputs reach consumers.

**Layer 5: Agent Security.** Contains risks specific to autonomous and semi-autonomous agents. Manages permission boundaries, tool access controls, delegation chain integrity, and behavioral guardrails. Agent Security addresses the highest-severity risks in the taxonomy because agents amplify the impact of every other risk category.

## Using the Risk Assessment

1. Inventory all generative AI workloads, including those in development, staging, and production.
2. For each workload, walk the risk taxonomy and score each applicable risk using the likelihood-impact dimensions.
3. Map each scored risk to the responsible framework layer.
4. Verify that the responsible layer has implemented the corresponding mitigation control at or above the organization's current maturity stage.
5. For risks scored as high-likelihood or critical-impact, verify that both the primary and secondary layers have active mitigations.
6. Record residual risks, those that remain after all implemented mitigations, in the governance risk register.
7. Review the risk register at a cadence appropriate to your maturity stage: quarterly at crawl, monthly at walk, continuously at run.
