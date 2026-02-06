# Workload Principles

> Layer 4 of the GenAI Adoption Framework

A workload is a single, scoped unit of generative AI functionality: a summarizer, a classifier, a draft generator, a code reviewer. These six principles govern how workloads are conceived, built, evaluated, hardened, and maintained. They apply regardless of model provider, deployment pattern, or organizational size.

---

## Principle 1: Scope Before You Build

**Statement.** Every workload begins with a written brief that defines the problem, the user, the success criteria, the data classification tier, and the cost ceiling — before any prompt is drafted.

**Rationale.** The single most common failure mode in generative AI adoption is building the wrong thing. Teams jump from a compelling demo to production without establishing what "good" looks like, who the workload serves, or whether the problem warrants a generative approach at all. Scoping forces these questions to the surface before engineering effort is spent.

**Implications.**
- A workload brief template is a required artifact, not optional documentation.
- The brief must be reviewed and approved before prompt engineering begins.
- Briefs include explicit cost ceilings — the maximum acceptable per-invocation and monthly cost.
- Problem-solution fit is validated: is generative AI the right tool, or would deterministic logic, retrieval, or a simpler model suffice?

**Violations.** Starting prompt engineering without a written brief. Defining success criteria after deployment. Treating a playground experiment as a production-ready workload.

---

## Principle 2: Prompts Are Specifications

**Statement.** Prompts are not throwaway text. They are behavioral specifications that define what a workload does, and they deserve version control, peer review, and change management equal to application code.

**Rationale.** A prompt determines output structure, tone, accuracy boundaries, safety behavior, and cost. Changing a single word can alter all of these. Yet many organizations treat prompts as casual, unversioned strings embedded in application code or configuration. This creates drift, inconsistency, and an inability to reproduce or roll back behavior.

**Implications.**
- Prompts are stored in version control, separate from application logic where practical.
- Every prompt change goes through a review process that considers correctness, safety, and cost impact.
- Prompts follow a structured format: persona, task, output format, constraints, and few-shot examples.
- Prompt changes trigger re-evaluation against the golden dataset before deployment.

**Violations.** Editing prompts directly in production. Storing prompts only in application memory or environment variables without version history. Skipping review for "minor" prompt tweaks.

---

## Principle 3: Evaluation Is Systematic

**Statement.** Workload quality is measured through structured scorecards applied to golden datasets, not through subjective spot-checks or anecdotal impressions.

**Rationale.** "It looks good to me" is not an evaluation methodology. Generative outputs are inherently variable. Without systematic evaluation — consistent inputs, defined dimensions, scored rubrics — teams cannot detect regressions, compare prompt versions, or make defensible deployment decisions. Gut checks reward recency bias and miss edge cases.

**Implications.**
- Every workload has a golden dataset: a curated set of representative inputs with expected or reference outputs.
- Evaluation uses a multi-dimensional scorecard: accuracy, completeness, format compliance, safety, tone — weighted by workload type.
- Evaluation runs automatically in continuous integration pipelines, not manually in notebooks.
- Threshold-based gating prevents deployment when scores drop below defined minimums.

**Violations.** Deploying without running the evaluation suite. Using a single example as proof of quality. Evaluating only the happy path.

---

## Principle 4: Adversarial Testing Is Mandatory

**Statement.** If you have not systematically attempted to make a workload produce harmful, incorrect, or policy-violating outputs, it is not ready for deployment.

**Rationale.** Generative models are susceptible to prompt injection, jailbreaking, output format manipulation, and information exfiltration. These are not theoretical risks — they are well-documented, actively exploited attack patterns. A workload that has only been tested with cooperative inputs will fail under adversarial conditions.

**Implications.**
- Every workload undergoes a structured adversarial test suite before first deployment and after every significant change.
- Adversarial tests cover prompt injection, instruction override, output format manipulation, sensitive data extraction, and extreme or degenerate inputs.
- Adversarial test results are blocking: a failed test prevents deployment, not just logs a warning.
- Adversarial tests run in continuous integration alongside functional evaluations.

**Violations.** Claiming a workload is "safe" without adversarial testing. Running adversarial tests manually and inconsistently. Treating adversarial failures as low-priority issues to address later.

---

## Principle 5: Workloads Are Never Done

**Statement.** A deployed workload is a living system. Models update, user behavior shifts, data distributions change, and new attack vectors emerge. Continuous monitoring and periodic re-evaluation are operational requirements, not optional enhancements.

**Rationale.** Generative AI workloads degrade silently. A model provider updates their model and output quality shifts. Users discover prompt patterns that produce unintended behavior. The distribution of real-world inputs diverges from the golden dataset. Without active monitoring, these changes go undetected until they cause visible harm.

**Implications.**
- Production outputs are sampled and scored on a regular cadence — weekly at minimum.
- Quality drift detection compares ongoing scores to baseline evaluation results.
- Model upgrades trigger full re-evaluation and adversarial testing before the new model version is adopted.
- Feedback from users, reviewers, and monitoring feeds back into golden dataset expansion and prompt iteration.

**Violations.** Deploying a workload and declaring it "done." Skipping re-evaluation after a model version change. Ignoring quality drift because no one has complained.

---

## Principle 6: Quality Is Measured Against Golden Datasets

**Statement.** Workload quality claims are grounded in performance against curated, versioned golden datasets — not individual examples, cherry-picked outputs, or anecdotal evidence.

**Rationale.** Individual examples prove nothing about systematic quality. A workload can produce a brilliant response to one input and fail catastrophically on the next. Golden datasets provide the statistical grounding necessary to make meaningful quality claims, detect regressions, and compare alternatives.

**Implications.**
- Golden datasets are a first-class artifact: versioned, reviewed, and maintained alongside prompts and code.
- Minimum golden dataset size is 30 inputs for initial deployment; 100 or more for mature workloads.
- Golden datasets include edge cases, adversarial inputs, and inputs representing the full distribution of expected usage.
- Quality metrics are always reported as aggregate scores over the dataset, never as single-example demonstrations.

**Violations.** Claiming quality based on a demo. Using a golden dataset that does not represent real-world input distribution. Allowing golden datasets to become stale as usage patterns evolve.
