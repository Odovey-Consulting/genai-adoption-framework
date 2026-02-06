# Workload Anti-Patterns

> Layer 4 of the GenAI Adoption Framework

Anti-patterns are recurring mistakes that appear reasonable on the surface but lead to predictable failures. Each anti-pattern below has been observed repeatedly across organizations adopting generative AI. Recognizing them early prevents costly rework, quality failures, and eroded trust.

---

## Anti-Pattern 1: "The Hammer"

**Description.** A team identifies a problem and immediately decides to solve it with a large language model, without evaluating whether generative AI is the right tool. The reasoning is "we have access to a powerful model, so let's use it" — treating the model as a universal hammer and every problem as a nail.

**Symptoms.**
- No problem-solution fit analysis in the workload brief (or no brief at all).
- The workload performs a task that could be handled by a database query, a regex, a rules engine, or a traditional classifier.
- Per-invocation costs are orders of magnitude higher than alternative approaches with equivalent accuracy.
- The team cannot articulate why generative AI is necessary beyond "it's more modern" or "it's what we have."

**Consequences.** Inflated costs for tasks that do not benefit from generative capabilities. Unnecessary exposure to model variability, hallucination risk, and latency. Wasted engineering effort building evaluation and adversarial test suites for a workload that should not exist in its current form.

**Correct Approach.** Complete a problem-solution fit analysis as part of the workload brief. Ask: Does this task require generation, or does it require lookup? Is output variability acceptable or harmful? Would a deterministic approach be more reliable and cheaper? Reserve generative AI for tasks where its strengths — flexible language understanding, open-ended generation, nuanced classification — genuinely outperform simpler alternatives.

---

## Anti-Pattern 2: "The Vibes Check"

**Description.** The team evaluates workload quality by reading a handful of outputs and deciding they "look good." There is no golden dataset, no scoring rubric, no systematic comparison. Quality judgments are subjective, inconsistent, and unreproducible.

**Symptoms.**
- No golden dataset exists for the workload.
- Evaluation consists of a team member running a few prompts and reviewing the outputs visually.
- Quality claims are expressed as "it seems to work well" or "the outputs are pretty good."
- Different team members reach different conclusions about the same outputs.
- There is no evaluation step in the CI pipeline.

**Consequences.** Regressions go undetected because there is no baseline to compare against. Prompt changes are deployed without evidence that they improve (or do not degrade) quality. Quality disputes cannot be resolved because there is no shared measurement framework. User trust erodes as inconsistent quality surfaces in production.

**Correct Approach.** Build a golden dataset of at least 30 representative inputs. Define a multi-dimensional scorecard with explicit scoring criteria. Run evaluations automatically in CI and compare against established baselines. Make deployment decisions based on aggregate scores, not individual impressions.

---

## Anti-Pattern 3: "The One-Shot"

**Description.** The team builds a workload, deploys it, and considers the work finished. There is no monitoring, no periodic re-evaluation, no process for incorporating feedback. The workload runs unchanged until something breaks visibly enough to generate a support ticket.

**Symptoms.**
- No monitoring pipeline captures production outputs for quality review.
- The workload has been running for months with the same prompt, against a model that has been updated multiple times.
- No one on the team can say what the workload's current quality level is.
- The golden dataset has not been updated since initial deployment.
- User feedback is collected but never acted upon.

**Consequences.** Silent quality degradation as models update and user behavior shifts. Stale prompts that do not account for new edge cases discovered in production. Growing gap between perceived and actual workload quality. When the degradation finally becomes visible, the cost of remediation is far higher than incremental maintenance would have been.

**Correct Approach.** Establish a monitoring cadence: sample 5-10% of production outputs weekly, score them against the evaluation scorecard, and compare to the deployment baseline. Re-evaluate the full golden dataset after every model upgrade. Expand the golden dataset with edge cases discovered in production. Treat workloads as living systems that require ongoing maintenance.

---

## Anti-Pattern 4: "The Untested"

**Description.** The team skips adversarial testing because the workload "works in the playground." Testing is limited to cooperative inputs — the kinds of prompts a well-intentioned user would submit. The possibility that users will submit malicious, degenerate, or unexpected inputs is acknowledged in theory but not tested in practice.

**Symptoms.**
- No adversarial test suite exists for the workload.
- The team has never tested for prompt injection, output format override, or information exfiltration.
- When asked about adversarial resilience, the team says "we'll add that later" or "our users wouldn't do that."
- Adversarial testing is a manual, ad-hoc activity performed once (if at all) rather than an automated, continuous process.

**Consequences.** The workload is vulnerable to well-documented attack patterns. Prompt injection allows users to override instructions and produce arbitrary outputs. Output format manipulation can inject malicious content into downstream systems. Information exfiltration can expose system prompts, internal configurations, or data from other sessions. These failures are not hypothetical — they are common and actively exploited.

**Correct Approach.** Build an adversarial test suite that covers prompt injection (direct and indirect), output format override, information exfiltration, and extreme input conditions. Run the suite in CI with blocking enforcement. Add new test cases as new attack patterns are published. Treat adversarial resilience as a deployment requirement, not an optional hardening step.

---

## Anti-Pattern 5: "The Unscoped"

**Description.** The team starts building a workload without a written brief, without success criteria, and without a cost ceiling. The scope emerges organically — driven by what seems interesting, what the model can do, and what stakeholders request during development. The result is a workload with no clear boundaries and no way to determine if it is successful.

**Symptoms.**
- No workload brief exists, or the brief was written retroactively after development began.
- The workload's purpose has shifted multiple times during development.
- Success criteria are vague: "it should be helpful" or "it should save time."
- There is no cost ceiling; per-invocation costs and monthly spend are unknown or uncontrolled.
- Scope creep is constant: "can we also make it do X?" is a weekly occurrence.

**Consequences.** The workload tries to do too many things and does none of them well. Evaluation is impossible because there is no clear definition of success. Costs grow unchecked because there is no ceiling to enforce. The team spends increasing effort on an expanding scope with diminishing returns. Eventually, the workload is either abandoned or deployed in a state that satisfies no one.

**Correct Approach.** Write a workload brief before any development begins. Define success criteria that are specific and measurable. Set a cost ceiling and enforce it through model selection and prompt optimization. Treat scope boundaries as commitments: new capabilities require new briefs, new evaluation cycles, and explicit approval. A well-scoped workload that does one thing reliably is vastly more valuable than an unbounded workload that does many things unreliably.

---

## Anti-Pattern 6: "The Golden Silence"

**Description.** The team has no golden dataset and no evaluation scorecard. Quality is assessed informally, if at all. The team may believe that their workload is "too creative" or "too open-ended" for systematic evaluation. In practice, every workload can be evaluated — the team simply has not invested in figuring out how.

**Symptoms.**
- No golden dataset exists. When asked, the team says "we're planning to build one" or "our outputs are too variable to evaluate systematically."
- No evaluation scorecard exists. Quality dimensions have not been defined, let alone weighted and scored.
- Prompt changes are deployed based on the author's judgment alone.
- There is no way to compare the quality of two prompt versions empirically.
- Regression detection is impossible because there is no baseline.

**Consequences.** The team is flying blind. They cannot demonstrate workload quality to stakeholders, they cannot detect regressions, and they cannot make evidence-based decisions about prompt changes or model migrations. Every deployment is a leap of faith. When quality problems surface in production, there is no framework for diagnosing the root cause or measuring the impact of a fix.

**Correct Approach.** Accept that every workload can be evaluated, even highly creative or open-ended ones. For creative workloads, evaluation dimensions shift from "correctness" to "format compliance," "tone consistency," "safety," and "relevance" — but they still exist and can still be scored. Build the golden dataset first, then design the scorecard to match. Start small: 30 inputs and three scoring dimensions are sufficient to begin. Expand as the workload matures. The investment is small; the return — in confidence, in regression detection, in evidence-based decision-making — is enormous.
