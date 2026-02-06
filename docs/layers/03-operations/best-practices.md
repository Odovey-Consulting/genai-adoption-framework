# Operations Best Practices

> Layer 3 of the GenAI Adoption Framework

These practices represent tested approaches to operating a generative AI platform. Each practice is actionable, explains its reasoning, and describes the outcome teams should expect when it is followed consistently.

---

## 1. Export Metrics to Your Existing Monitoring Platform

**Guidance:** Route all generative AI telemetry (latency, token counts, error rates, cost estimates) into the monitoring and dashboarding system your organization already operates. Do not deploy a standalone monitoring stack for the AI platform.

**Rationale:** Operations teams already have muscle memory, alert routing rules, and escalation workflows built around their existing monitoring infrastructure. A separate system for AI metrics means separate on-call rotations, separate alert fatigue, and a separate place to look during incidents. Integration beats isolation. The cost of exporting metrics to an existing system is trivial compared to the cost of maintaining a parallel operational surface.

**Expected Outcome:** On-call engineers diagnose AI platform issues using the same tools they use for every other system. Correlation between AI platform metrics and downstream application metrics becomes possible without switching between dashboards.

---

## 2. Send Cost Alerts to Team Leads, Not On-Call Engineers

**Guidance:** Route budget threshold alerts to the team lead or budget owner for the consuming team. Do not send cost alerts to the platform on-call rotation unless spending indicates a technical malfunction such as a runaway loop.

**Rationale:** Cost overruns are almost always a business decision, not an infrastructure incident. The on-call engineer cannot decide whether Team A's spike in usage is a legitimate business need or waste. The team lead can. Sending cost alerts to on-call engineers trains them to ignore financial signals because they cannot act on them, and it delays the alert reaching someone who can.

**Expected Outcome:** Budget owners receive timely notification of spend trends and can make informed decisions about throttling, expanding budgets, or optimizing usage. On-call engineers focus on availability and performance incidents.

---

## 3. Review Blocked Requests Weekly

**Guidance:** Establish a weekly review of requests blocked by input and output guardrails. Categorize each blocked request as a true positive (correctly blocked), false positive (incorrectly blocked), or ambiguous (requires policy clarification). Track the ratio over time.

**Rationale:** Guardrails that are too aggressive block legitimate work and push developers toward shadow deployments. Guardrails that are too permissive fail to catch policy violations. The only way to calibrate guardrails is to review what they actually block. Without this feedback loop, guardrail configuration drifts toward one extreme or the other, and the platform team loses credibility with either security or development teams.

**Expected Outcome:** False positive rates decline over time as guardrail rules are tuned. Developers trust that blocked requests will be reviewed fairly. Security teams trust that bypass attempts are being caught and escalated. Guardrail configuration improves continuously rather than remaining static.

---

## 4. Make the Platform Discoverable Where Developers Already Work

**Guidance:** Publish platform documentation, onboarding guides, and support links in the locations developers already visit daily: internal code repositories, developer portals, messaging channels, and CLI tooling help commands. Do not build a standalone documentation site that requires a separate bookmark.

**Rationale:** Developers discover tools and services through their existing workflows. A standalone documentation site, no matter how well-organized, competes with every other internal tool for attention and loses. Documentation that appears in the code repository README, in the CLI help output, and in the team messaging channel's pinned messages gets read. Documentation on a separate site gets forgotten.

**Expected Outcome:** New developers find onboarding instructions without asking where they are. Platform adoption increases because the path to getting started is visible in contexts developers already frequent. Support ticket volume for "how do I get started" questions decreases.

---

## 5. Track Adoption Metrics to Measure Platform Health

**Guidance:** Define and track a small set of adoption metrics: number of active teams, weekly request volume, median time from onboarding request to first successful API call, and ratio of requests routed through the platform versus estimated total generative AI usage in the organization. Review these metrics monthly.

**Rationale:** Uptime and latency are necessary but insufficient indicators of platform health. A platform can have perfect availability and still be failing if only two of twenty teams are using it. Adoption metrics reveal whether the platform is achieving its purpose: centralizing generative AI usage to enable governance, observability, and cost control. Low or declining adoption is the strongest signal that friction, capability gaps, or policy rigidity is driving shadow usage.

**Expected Outcome:** Platform teams detect adoption stalls early and can investigate root causes. Leadership has a quantifiable measure of platform value beyond infrastructure metrics. Investment decisions about platform capability are informed by usage data rather than assumptions.

---

## 6. Build Incident Playbooks Before You Need Them

**Guidance:** Write incident response playbooks for generative AI-specific scenarios before the platform enters production. At minimum, cover: upstream provider outage, guardrail bypass detected, sensitive data found in model output, runaway cost event, and API key compromise. Review and update playbooks quarterly.

**Rationale:** Incident response under pressure is not the time to design a process. Generative AI incidents have characteristics that differ from traditional infrastructure incidents: they may involve data exposure through model outputs, policy violations that require legal review, or cost events that escalate in seconds rather than hours. Pre-written playbooks ensure that the first responder knows who to contact, what to check, and what authority they have to act, before the adrenaline starts.

**Expected Outcome:** Mean time to resolution for AI platform incidents decreases. Incident response is consistent regardless of which engineer is on call. Post-incident reviews identify process improvements rather than fundamental gaps in the response framework.

---

## 7. Automate Compliance Reporting from Gateway Logs

**Guidance:** Generate compliance evidence directly from gateway logs, access control records, and guardrail activation data using automated report generation. Define report templates that map to your compliance requirements and schedule automated generation at the required cadence.

**Rationale:** Manual compliance evidence collection is expensive, error-prone, and dreaded by every team involved. Gateway logs already contain the raw data needed for most compliance evidence: who accessed what, when, through which controls, and whether guardrails were active. Automating the transformation of this raw data into structured compliance reports eliminates manual labor, reduces human error, and makes compliance a continuous background process rather than a quarterly fire drill.

**Expected Outcome:** Compliance reporting requires minutes of review rather than days of manual evidence collection. Audit preparation time drops significantly. Compliance gaps are detected in near-real-time rather than during periodic audits, enabling faster remediation.
