# Workload Best Practices

> Layer 4 of the GenAI Adoption Framework

These practices represent hard-won lessons from organizations building generative AI workloads. They are ordered by lifecycle phase — scoping through monitoring — and each one addresses a specific failure mode observed in real-world deployments.

---

## Practice 1: Write a Workload Brief Before Writing a Prompt

The workload brief is the single most valuable artifact in the entire workload lifecycle, and it costs almost nothing to produce. A brief forces you to answer foundational questions: What problem are we solving? Who is the user? What does success look like? What is the cost ceiling?

Without a brief, teams default to "try it and see" — which produces workloads optimized for demos, not for production. The brief does not need to be long. A single page is sufficient. But it must exist, it must be reviewed by at least one person who did not write it, and it must be finalized before any prompt engineering begins.

The brief also serves as the authoritative reference when scope creep appears. When someone asks "can we also make it do X?" the answer is found in the brief: either X is in scope or it is not. If it is not, it requires a new brief, a new workload, and a new evaluation cycle.

**How to apply:** Create a brief template as a standard document in your workload repository. Require a completed brief as a prerequisite for opening a workload development branch or task. Include a brief review step in your workload approval workflow.

---

## Practice 2: Include the Data Classification Tier in the Brief

The data classification tier — defined in Layer 3 (Data) — determines which models may be used, which deployment patterns are permitted, what logging and redaction rules apply, and what guardrails must be active. This is not optional metadata; it is a constraint that shapes every subsequent decision.

Including the classification tier in the brief ensures that these constraints are visible from the start. Teams that discover their data classification requirements late in development face costly rework: switching models, adding redaction pipelines, changing deployment targets, or in the worst case, discovering that the workload cannot be built as designed because the data cannot be sent to the intended model.

**How to apply:** Make the data classification tier a required field in the workload brief template. Define a lookup process so that workload authors can determine the correct tier for their data. Block workload development if the tier is unresolved.

---

## Practice 3: Build the Golden Dataset Before the Evaluation Scorecard

Teams frequently design their evaluation scorecard first — deciding what dimensions to measure and what thresholds to set — and then attempt to build a golden dataset that fits. This is backwards. The golden dataset should represent the real-world input distribution, and the scorecard should be designed to measure what matters for those inputs.

Start by collecting representative inputs. Pull from production logs if they exist. Interview users about the range of requests they expect. Include the obvious happy-path cases, but spend proportionally more time on edge cases: unusual inputs, ambiguous requests, inputs at the boundary of the workload's scope. Aim for a minimum of 30 inputs for initial deployment; plan for 100 or more as the workload matures.

Once the golden dataset exists, design the scorecard to measure the dimensions that matter for those specific inputs and for the workload type. A summarization workload needs accuracy and completeness; a classification workload needs precision and recall; a generation workload may prioritize tone and format compliance.

**How to apply:** Create the golden dataset as the first artifact after the brief is approved. Store it in version control alongside the prompt. Reference it in the evaluation pipeline configuration. Review and expand it quarterly.

---

## Practice 4: Run Adversarial Tests in CI, Not Manually

Manual adversarial testing is better than no adversarial testing, but it is not sufficient. Manual tests are inconsistent — different people test different things at different times. They are not reproducible. They are not tracked. And they are the first thing dropped when deadlines tighten.

Adversarial tests belong in the continuous integration pipeline alongside functional evaluations. They run automatically on every prompt change, every code change, and every model configuration change. They produce structured, comparable results. They block deployment when they fail.

The adversarial test suite should include at minimum: direct prompt injection attempts, indirect injection via simulated retrieved content, output format override attempts, requests for system prompt disclosure, and extreme input conditions (empty, oversized, unexpected character sets). Each test case has a pass/fail criterion, not a subjective assessment.

**How to apply:** Create an adversarial test directory in your workload repository. Define test cases as structured data: input, expected behavior (rejection, safe response, format compliance), and pass/fail criteria. Wire the test suite into your CI pipeline with blocking enforcement. Add new test cases whenever a new attack pattern is identified.

---

## Practice 5: Sample 5-10% of Production Outputs Weekly for Quality Drift

Deployment is not the finish line. Models change. User behavior shifts. Data distributions evolve. A workload that scored well at deployment can degrade silently over weeks or months.

Sampling a percentage of production outputs and scoring them against the evaluation scorecard provides an early warning system for quality drift. A 5-10% sampling rate is typically sufficient to detect meaningful degradation without creating excessive evaluation cost. Scoring can be automated for objective dimensions (format compliance, length, presence of required fields) and periodic for subjective dimensions (tone, helpfulness, accuracy).

The key discipline is consistency: sample every week, score against the same scorecard, compare against the deployment baseline, and alert when scores drop below a defined threshold. Quality drift that is detected early is cheap to fix — a prompt adjustment, a few new golden dataset entries. Drift that goes undetected for months produces user distrust, reputational damage, and expensive remediation.

**How to apply:** Configure your monitoring pipeline to capture a random sample of production request-response pairs. Store samples in a review queue. Score samples against the evaluation scorecard on a weekly cadence. Define drift thresholds per dimension and configure alerts.

---

## Practice 6: Version Prompts with the Same Rigor as Application Code

Prompts define workload behavior. A one-word change to a prompt can alter accuracy, safety, cost, and tone. Yet many organizations treat prompts as informal text — edited casually, stored in configuration files without history, and deployed without review.

Version control for prompts means: every change is tracked, every change is attributable to an author, every change produces a reviewable diff, and every deployed version can be reproduced exactly. This is not aspirational — it is the minimum standard for any system that influences user-facing behavior.

Versioning also enables rollback. When a prompt change causes a regression, the team can revert to the previous version immediately and investigate at their own pace. Without versioning, rollback requires someone to remember (or guess) what the previous prompt said.

**How to apply:** Store prompts as dedicated files in version control, separate from application code where practical. Attach a version identifier (commit hash or semantic version) to every evaluation result and production log entry. Require pull request review for prompt changes. Include prompt version in deployment metadata.

---

## Practice 7: Re-Evaluate After Every Model Upgrade

When the model powering a workload is updated — whether through a provider-initiated upgrade, a deliberate migration, or a configuration change — the workload's behavior can change in unpredictable ways. A model update is functionally equivalent to rewriting the workload's execution engine. It requires re-evaluation.

Re-evaluation means running the full evaluation suite (golden dataset scoring and adversarial testing) against the new model version and comparing results to the previous baseline. If scores improve, update the baseline. If scores degrade, decide whether to accept the degradation, adjust the prompt to compensate, or pin to the previous model version.

Organizations that skip re-evaluation after model upgrades accumulate hidden quality debt. They discover regressions only when users complain or when a downstream system fails — by which point the root cause is obscured by time and intervening changes.

**How to apply:** Track model versions in your workload configuration. Configure alerts when a model provider announces a version update. Require full re-evaluation before adopting a new model version in production. Document model version history alongside prompt version history.
