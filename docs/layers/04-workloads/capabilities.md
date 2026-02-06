# Workload Capabilities

> Layer 4 of the GenAI Adoption Framework

This document defines the capabilities required to build, evaluate, harden, deploy, and operate generative AI workloads. Capabilities are organized by lifecycle phase. Each capability includes a maturity stage indicator showing when organizations typically develop it.

**Maturity Stages:**
- **Walk** — foundational capability, required before first production workload
- **Run** — intermediate capability, required for scaling beyond initial workloads
- **Fly** — advanced capability, required for enterprise-grade operations

---

## Phase 1: Scoping

Scoping translates a business need into a structured workload definition. It is the most frequently skipped phase and the most common root cause of workload failure.

### Workload Brief Template — Walk

A standardized document that captures the essential definition of a workload before any engineering begins. The brief includes:

- **Problem statement:** What specific task is the workload performing, and for whom?
- **Current state:** How is this task done today? What are the pain points?
- **Proposed approach:** What type of generative AI capability will address this? (summarization, classification, generation, extraction, transformation, conversation)
- **Data classification tier:** What is the sensitivity level of the data this workload will process? This determines which models, deployment patterns, and guardrails apply per Layer 3 (Data) policies.
- **Success criteria:** Measurable outcomes that define "good enough" for deployment.
- **Cost ceiling:** Maximum acceptable per-invocation cost and monthly budget.
- **Scope boundaries:** What the workload explicitly will not do.

### Problem-Solution Fit Validation — Walk

A structured assessment of whether generative AI is the appropriate tool for the stated problem. Many tasks are better served by deterministic logic, traditional machine learning, search and retrieval, or simple rule-based systems. This validation asks: Does the problem require generation, or does it require lookup? Is the variability of generative output acceptable, or is deterministic behavior required? Does the cost-quality trade-off favor a generative approach?

### Workload Type Classification — Walk

Categorization of the workload into a recognized type that drives downstream decisions about evaluation dimensions, adversarial test coverage, and monitoring strategy. Common types:

- **Summarization:** Condensing longer content into shorter form.
- **Classification:** Assigning categories or labels to inputs.
- **Generation:** Creating new content (drafts, code, responses).
- **Extraction:** Pulling structured data from unstructured inputs.
- **Transformation:** Converting content from one format or style to another.
- **Conversational:** Multi-turn interactive dialogue.

### Success Criteria Definition — Walk

Explicit, measurable criteria that the workload must meet before deployment approval. Criteria span accuracy, latency, cost, safety, and user experience. Each criterion has a minimum threshold (deployment gate) and a target threshold (desired performance).

### Cost Ceiling Setting — Walk

Establishing the maximum per-invocation and monthly cost for the workload. This ceiling drives model selection, prompt optimization (minimizing token usage), caching strategy, and volume controls. Cost ceilings are reviewed and adjusted as usage patterns become clear.

---

## Phase 2: Prompt Engineering

Prompt engineering is the disciplined practice of designing, structuring, and iterating on the instructions that define workload behavior.

### Structured Prompt Design — Walk

All prompts follow a consistent structure that separates concerns and maximizes clarity:

- **Persona:** Who the model is acting as (role, expertise, tone).
- **Task:** What the model must do, stated unambiguously.
- **Output format:** The exact structure of the expected output (plain text, structured markup, delimited fields, key-value pairs).
- **Constraints and rules:** Explicit boundaries — what the model must not do, what it must always do, how it should handle uncertainty.
- **Few-shot examples:** Representative input-output pairs that demonstrate expected behavior, edge case handling, and format compliance.

### Prompt Version Control — Walk

Prompts are stored in version control systems alongside or referenced by the application code that uses them. Every change produces a trackable diff. Prompt files are separate from application logic to enable independent review, testing, and rollback. Version identifiers (commit hashes, semantic versions, or monotonic counters) are attached to every evaluation result and production log entry.

### Prompt Review Process — Run

Prompt changes go through a defined review workflow before deployment. Reviewers assess:

- **Correctness:** Does the prompt produce accurate outputs for the golden dataset?
- **Safety:** Does the prompt introduce new attack surfaces or weaken existing guardrails?
- **Cost impact:** Does the change significantly increase token usage or require a more expensive model?
- **Clarity:** Is the prompt unambiguous? Could a different model interpret it differently?
- **Regression risk:** Does the change improve one dimension while degrading another?

---

## Phase 3: Evaluation

Evaluation provides the empirical foundation for deployment decisions, prompt comparisons, and regression detection.

### Golden Dataset Creation — Walk

A curated, versioned collection of at minimum 30 representative inputs paired with reference outputs or evaluation criteria. The dataset covers:

- **Happy path:** Typical inputs representing the most common usage patterns.
- **Edge cases:** Unusual but valid inputs that test boundary behavior.
- **Adversarial inputs:** Inputs designed to provoke failures (covered in more detail in Phase 4).
- **Distribution coverage:** Inputs representing the full range of expected real-world variety — different lengths, languages (if applicable), complexity levels, and domains.

Golden datasets are living artifacts. They grow as new edge cases are discovered in production and as usage patterns evolve.

### Multi-Dimensional Scoring — Walk

Each evaluation scores outputs across multiple dimensions relevant to the workload type. Common dimensions:

- **Accuracy / Correctness:** Does the output contain factually correct information?
- **Completeness:** Does the output address all aspects of the input?
- **Format compliance:** Does the output match the specified structure?
- **Tone / Style:** Does the output match the expected voice?
- **Safety:** Does the output avoid harmful, biased, or policy-violating content?
- **Conciseness:** Is the output appropriately sized — not truncated, not padded?

Each dimension is weighted by workload type. A classification workload weights accuracy heavily; a creative generation workload may weight tone and style more heavily.

### Automated CI-Based Evaluation — Run

Evaluation suites run automatically in continuous integration pipelines whenever prompts, application code, or model configurations change. Results are compared against established baselines. The pipeline produces a structured report showing per-dimension scores, aggregate scores, and delta from the previous run.

### Regression Detection — Run

Automated comparison of current evaluation scores against historical baselines. Regression detection identifies when a change (prompt edit, model update, data shift) causes scores to drop below acceptable thresholds. Regressions are surfaced as pipeline failures or alerts, not buried in logs.

### Threshold-Based Gating — Run

Deployment is blocked when evaluation scores fall below defined minimum thresholds. Gates are configured per dimension: a workload may tolerate a small drop in conciseness but not in accuracy. Gating thresholds are set during scoping and calibrated during initial evaluation cycles.

---

## Phase 4: Adversarial Testing

Adversarial testing validates workload resilience against intentional misuse, manipulation, and edge-case failure modes.

### Prompt Injection Testing — Walk

Systematic testing of the workload's resistance to inputs that attempt to override, modify, or append to the system prompt. Tests include direct injection ("ignore previous instructions"), indirect injection (malicious content embedded in retrieved documents or user-supplied data), and encoding-based evasion.

### Output Format Override Testing — Walk

Testing whether adversarial inputs can force the workload to produce output in a format different from what was specified — for example, producing executable code when plain text was expected, or including markup that downstream systems interpret as commands.

### Information Exfiltration Attempts — Run

Testing whether adversarial inputs can cause the workload to reveal system prompts, internal configuration, training data patterns, or information from other users' sessions. This is especially critical for workloads that process multi-tenant data or operate in shared environments.

### Extreme Input Testing — Walk

Testing workload behavior under degenerate input conditions: empty inputs, extremely long inputs, inputs in unexpected languages or character sets, inputs consisting entirely of special characters, and inputs designed to maximize token consumption and cost.

### CI-Based Adversarial Blocking — Run

Adversarial test suites run in continuous integration and produce blocking results. A workload that fails adversarial tests cannot be deployed. Adversarial test results are tracked over time to detect resilience regressions.

---

## Phase 5: Deployment

Deployment transitions a workload from development into production, integrating with platform-layer infrastructure.

### Service Packaging — Run

The workload is packaged as a deployable service with defined interfaces, health checks, and configuration management. Packaging includes the prompt (versioned), application logic, model configuration (provider, model identifier, parameters), and evaluation metadata.

### Gateway Integration — Run

The deployed workload routes through the organization's API gateway layer (defined in Layer 2: Platform). This ensures that rate limiting, authentication, logging, cost tracking, and policy enforcement are applied consistently.

### Observability Configuration — Walk

Every deployed workload emits structured logs, metrics, and traces. At minimum: request and response payloads (subject to data classification redaction rules), latency, token counts, model version, prompt version, and error conditions. Observability data feeds monitoring and evaluation pipelines.

### Classification Verification — Walk

Before deployment, automated checks verify that the workload's data handling matches its declared data classification tier. A workload declared as processing non-sensitive data must not have access to sensitive data sources. A workload processing sensitive data must route through appropriately configured infrastructure.

---

## Phase 6: Monitoring

Monitoring ensures that deployed workloads maintain quality, safety, and cost targets over time.

### Post-Deployment Output Sampling — Walk

A defined percentage of production outputs (typically 5-10%) are captured and scored against the evaluation scorecard on a regular cadence. Sampling provides ongoing empirical evidence of workload quality without the cost of evaluating every output.

### Quality Drift Detection — Run

Sampled scores are compared against the baseline evaluation scores established at deployment time. Statistically significant drops in any dimension trigger alerts. Drift detection catches silent degradation caused by model updates, data distribution shifts, or emerging adversarial patterns.

### Feedback Loop to Prompt Iteration — Run

Insights from monitoring — low-scoring outputs, new edge cases, user complaints, adversarial attempts detected in production — feed back into the prompt engineering and evaluation cycle. New golden dataset entries are created from production discoveries. Prompts are iterated, re-evaluated, and redeployed through the standard pipeline.
