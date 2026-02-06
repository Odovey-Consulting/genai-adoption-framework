# Compliance and Regulatory Mapping

Generative AI operates within a regulatory landscape that is evolving rapidly and unevenly across jurisdictions. Organizations cannot wait for regulatory clarity before deploying AI systems; they must build compliance into their adoption posture from the start. This document maps the GenAI Adoption Framework's five layers to major regulatory frameworks and standards, providing a crosswalk that compliance teams can use to trace controls back to specific obligations.

This document does not constitute legal advice. It provides structural mappings to help technical and compliance teams collaborate. Jurisdiction-specific interpretation requires qualified legal counsel.

## Regulatory Landscape Overview

Regulatory approaches to AI fall into three broad categories:

**Risk-based regulation.** Classifies AI systems by risk tier and imposes obligations proportional to the tier. Higher-risk systems face stricter requirements around transparency, human oversight, data governance, and conformity assessment. The EU AI Act is the most prominent example, but similar risk-tiered approaches are emerging in multiple jurisdictions.

**Standards-based frameworks.** Voluntary or semi-mandatory frameworks that define processes for AI risk management, trustworthiness, and governance without prescribing specific technical controls. These frameworks (NIST AI RMF, ISO 42001) provide structure that organizations can adopt proactively, often before regulation mandates it.

**Existing compliance regimes extended to AI.** Existing audit and compliance standards (SOC 2, ISO 27001) do not address AI explicitly but apply to the infrastructure, data handling, and operational practices that underpin AI systems. Organizations already subject to these regimes must demonstrate that their AI deployments do not create gaps in existing controls.

## Framework-to-Regulation Crosswalk

### EU AI Act Risk Tiers and GAF Layer Mapping

The EU AI Act classifies AI systems into risk tiers with corresponding obligations. The table below maps these obligations to the framework layers that implement them.

| AI Act Obligation | Risk Tier | Layer 1: Governance | Layer 2: Gateway | Layer 3: Operations | Layer 4: Workloads | Layer 5: Agent Security |
|---|---|---|---|---|---|---|
| Risk classification and registration | All tiers | Primary: maintain AI system inventory and risk classifications | -- | Contributes: tracks active deployments | -- | -- |
| Data governance requirements | High-risk | Defines data policies and classification standards | Enforces data flow controls and filtering | Logs data lineage and access patterns | Implements data quality checks in pipelines | Enforces data access boundaries for agents |
| Transparency and disclosure | High-risk, GPAI | Establishes disclosure policies and templates | Inserts disclosure markers in outputs | Records transparency metadata | Implements user-facing disclosure mechanisms | Ensures agent interactions are identifiable as AI |
| Human oversight mechanisms | High-risk | Defines oversight requirements by use case | Provides override and kill-switch capabilities | Monitors for conditions requiring human intervention | Implements human-in-the-loop workflows | Enforces human approval for high-impact agent actions |
| Accuracy, robustness, cybersecurity | High-risk | Sets accuracy benchmarks and testing cadence | Validates inputs and filters adversarial payloads | Tracks accuracy metrics and drift | Runs evaluation pipelines and adversarial tests | Tests agent boundary enforcement |
| Record-keeping and audit trails | High-risk | Defines retention policies and audit requirements | Logs all traffic with redaction for sensitive data | Aggregates operational records for audit | Maintains evaluation artifacts and test results | Logs agent decision chains and tool invocations |
| Conformity assessment | High-risk | Owns the assessment process and remediation | Provides gateway-level evidence | Provides operational evidence | Provides workload-level evidence | Provides agent security evidence |
| General-purpose AI model obligations | GPAI | Establishes model evaluation and approval processes | Enforces model routing and version controls | Monitors model performance across workloads | Conducts capability evaluations | Evaluates agent-specific model behaviors |

### NIST AI Risk Management Framework and GAF Layer Mapping

The NIST AI RMF defines four core functions: Govern, Map, Measure, and Manage. The table below maps these functions to framework layers.

| NIST AI RMF Function | Subcategory | Primary GAF Layer | Supporting GAF Layers |
|---|---|---|---|
| Govern | Policies and procedures | Layer 1: Governance | -- |
| Govern | Roles and responsibilities | Layer 1: Governance | All layers (each has defined owners) |
| Govern | Risk tolerance definition | Layer 1: Governance | Layer 3: Operations (enforces thresholds) |
| Map | Context and use case documentation | Layer 1: Governance | Layer 4: Workloads |
| Map | Stakeholder identification | Layer 1: Governance | -- |
| Map | Benefit and risk identification | Layer 1: Governance | Layer 4: Workloads |
| Measure | Performance metrics and monitoring | Layer 3: Operations | Layer 4: Workloads |
| Measure | Bias and fairness testing | Layer 4: Workloads | Layer 1: Governance |
| Measure | Robustness and security testing | Layer 5: Agent Security | Layer 4: Workloads |
| Manage | Risk response and mitigation | Layer 3: Operations | Layer 2: Gateway |
| Manage | Incident response | Layer 3: Operations | Layer 1: Governance |
| Manage | Continuous improvement | Layer 3: Operations | All layers |

### SOC 2 Trust Service Criteria and GAF Controls

SOC 2 audits evaluate controls across five trust service criteria. Generative AI deployments introduce new control requirements within each criterion.

| Trust Service Criterion | AI-Specific Control Need | GAF Layer | GAF Control |
|---|---|---|---|
| Security | Prompt injection prevention | Layer 5: Agent Security | Input validation, boundary enforcement, adversarial testing |
| Security | Model endpoint access control | Layer 2: Gateway | Authentication, authorization, routing policies |
| Security | Agent permission boundaries | Layer 5: Agent Security | Least-privilege tool access, scope constraints |
| Availability | Model provider failover | Layer 2: Gateway | Multi-provider routing, fallback configuration |
| Availability | Degradation handling | Layer 3: Operations | Circuit breakers, graceful degradation policies |
| Processing integrity | Output accuracy validation | Layer 4: Workloads | Evaluation pipelines, grounding checks, human review |
| Processing integrity | Input data quality | Layer 2: Gateway | Schema validation, content filtering |
| Confidentiality | Data leakage prevention | Layer 2: Gateway | PII filtering, data classification enforcement |
| Confidentiality | Log redaction | Layer 3: Operations | Sensitive data masking in operational logs |
| Privacy | Consent management for AI processing | Layer 1: Governance | Data processing policies, consent tracking requirements |
| Privacy | PII minimization in prompts | Layer 2: Gateway | Automated PII detection and redaction |

### ISO 42001 (AI Management System) and GAF Mapping

ISO 42001 specifies requirements for establishing, implementing, maintaining, and continually improving an AI management system. The table below maps its key clauses to the framework.

| ISO 42001 Clause | Requirement | Primary GAF Layer | Implementation Mechanism |
|---|---|---|---|
| 4. Context of the organization | Understand internal/external factors affecting AI | Layer 1: Governance | AI system inventory, stakeholder analysis, use case registry |
| 5. Leadership | Management commitment and AI policy | Layer 1: Governance | Executive sponsorship, acceptable use policy, resource allocation |
| 6. Planning | Risk assessment and objectives | Layer 1: Governance | Risk register, risk assessment process (see risk-management.md) |
| 7. Support | Resources, competence, awareness | Cross-cutting | Organizational readiness (see organizational-readiness.md) |
| 8. Operation | Operational planning and control | Layer 3: Operations | Monitoring, alerting, cost management, change management |
| 8. Operation | AI system impact assessment | Layer 4: Workloads | Evaluation pipelines, bias testing, impact analysis |
| 8. Operation | Data management for AI | Layer 2: Gateway | Data classification enforcement, data flow controls |
| 9. Performance evaluation | Monitoring, measurement, analysis | Layer 3: Operations | Metrics collection, dashboards, drift detection |
| 9. Performance evaluation | Internal audit | Layer 1: Governance | Audit evidence aggregation from all layers |
| 10. Improvement | Nonconformity and corrective action | Layer 3: Operations | Incident response, post-incident review, control updates |

## How Each Layer Contributes to Compliance Posture

**Layer 1: Governance** is the compliance anchor. It holds the AI system inventory, defines policies, establishes risk tolerances, owns the audit relationship, and aggregates evidence from all other layers. Without governance, compliance is reactive and fragmented.

**Layer 2: Gateway** provides the primary technical enforcement point for data-related compliance obligations. Data classification enforcement, PII filtering, content moderation, and access control at the model boundary all generate compliance evidence automatically when properly instrumented.

**Layer 3: Operations** generates the continuous evidence stream that auditors require. Monitoring data, cost records, performance metrics, incident logs, and change history demonstrate that controls are not just designed but operating effectively over time.

**Layer 4: Workloads** provides application-specific compliance evidence. Evaluation results, bias test outcomes, human review records, and output validation logs demonstrate that individual AI applications meet their specific risk-tier obligations.

**Layer 5: Agent Security** addresses the newest and least well-defined compliance frontier. Agent permission records, decision chain logs, tool invocation audits, and boundary enforcement evidence will become increasingly important as regulations explicitly address autonomous AI systems.

## Audit Evidence Generation

Each layer should produce evidence artifacts that map directly to compliance obligations.

| Layer | Evidence Artifacts |
|---|---|
| Layer 1: Governance | Policy documents, risk register, AI system inventory, approval records, review meeting minutes |
| Layer 2: Gateway | Traffic logs (redacted), data classification enforcement records, access control audits, routing decision logs |
| Layer 3: Operations | Uptime reports, cost reports, performance metrics history, incident records, change logs, drift detection reports |
| Layer 4: Workloads | Evaluation pipeline results, bias and fairness test reports, human review decision logs, output validation records |
| Layer 5: Agent Security | Permission boundary audit logs, agent action traces, delegation chain records, adversarial test results, tool access logs |

Automate evidence collection from the Foundation stage onward. Manual evidence gathering does not scale, introduces inconsistency, and delays audit cycles. Each layer's operational tooling should export compliance-relevant data in a structured, queryable format.
