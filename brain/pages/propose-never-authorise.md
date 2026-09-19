---
id: propose-never-authorise
title: "The agent may propose; only a named human may authorise a price change"
category: decision
status: active
tags: [governance, approval, guard]
created: "2026-09-18T18:26:46"
updated: "2026-09-18T18:26:46"
---

<!-- compiled_truth -->
**Decided.** The skill's governing rule: **you may propose; only a named human may authorise.** The agent's role ends at a proposal backed by a dry-run verdict. Every write needs a human who asked for *that specific change* and named themselves as the approver.

**What it prohibits.** The skill gives the reason for each:

- **`--execute` on the agent's own initiative.** A general request such as "have a look at pricing" is not authorisation to write.
- **An invented `--approved-by`.** The field records who is accountable. A name nobody gave falsifies an audit record. If an approver is required and none was named, stop and ask.
- **`--override-caps` to get past a denial.** An override is a separate, explicit decision a human makes knowing a limit is being exceeded.
- **`--yes` to skip the typed-back confirmation,** unless the human has already approved this exact change in the conversation.
- **Retrying or working around a denial,** or treating it as a tooling bug ([[guard-verdicts]]).
- **Advancing its own mode.** Which units are writable is a human decision, recorded in the host project's mode file.

**Why.** Asymmetry: being slow is cheap, and a booking taken at a wrong price cannot be recalled. The founding commit names the failure mode it guards against: agent self-escalation, meaning a denial met by reaching for a flag that turns it into a yes.

**What stays open to the agent.** Proposing, dry-running, explaining verdicts, planning a rollback, and engaging the kill switch with a reason. Stopping writes is the cheap direction, so it needs no approval. Releasing the kill switch does ([[rollback-and-halt]]).

**Alternatives.** None are recorded in the repository.

**Blast radius.** Rollback execution inherits the rule and needs a named approver as well. The rule also shapes the host's mode model: a unit is either read-only (SHADOW) or requires per-change human approval (APPROVE_EXECUTE). No mode is documented in which the agent writes unattended.

**Open question.** Rollback's `--emergency` flag clears the cooldown and frequency caps. The skill does not say whether the agent may choose it on its own. The stricter reading, recommended until the owner decides, treats it like `--override-caps`: a human decision.


## Timeline

- time: 2026-09-18T18:26:46
  kind: decision
  summary: "Created this page: The agent may propose; only a named human may authorise a price change"
  source: "pricelabs-rate-setter/SKILL.md; commit b09e04a"
  affects: [propose-never-authorise]

- time: 2026-09-18T18:26:46
  kind: decision
  summary: Captured the governing rule and its prohibitions from the skill and founding commit
  source: "pricelabs-rate-setter/SKILL.md; commit b09e04a"
  affects: [propose-never-authorise]
