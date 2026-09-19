---
id: guard-verdicts
title: "Guard verdicts: each denial is information with its own correct response"
category: concept
status: active
tags: [guard, denials]
created: "2026-09-18T18:26:46"
updated: "2026-09-18T18:26:47"
---

<!-- compiled_truth -->
**Definition.** When `set_rates.py` (dry run or execute) or `rollback.py` evaluates a change, the host project's guard returns one verdict per change. The skill maps each verdict to what it means and what the agent must do. The founding commit says the table was checked line by line against the guard's real messages, so that it could not describe behaviour that does not exist.

**Mapping.** Described as mechanisms only. Several literal messages embed limit values, which this brain does not record.

| verdict | meaning | correct response |
|---|---|---|
| mode is SHADOW | The unit has no write authority. | Report it. Write authority is a human governance decision, not something to route around. |
| KILL SWITCH ACTIVE | Writes are halted, possibly by the circuit breaker. | Find out why before anyone releases it: read the kill-switch file and the recent audit log. |
| size cap exceeded | The change is larger than the per-change limit. | Propose a smaller change, or take the override question to the human explicitly. |
| frequency cap reached | Too many changes to this unit within the rolling window. | Wait, or escalate deliberately. Repeated edits in quick succession usually chase noise. |
| cooldown | A recent change has not had time to show an effect. | Wait. Re-cutting too soon is how an oscillation pattern started. |
| APPROVE_EXECUTE: human approval required | Working as designed. | Get a real name from the human, then pass it as the approver. |
| GUARD FAILED CLOSED | Configuration is missing or unreadable. | Fix the configuration. Never bypass: failing closed is the guard working. |
| max is not a permitted lever | The method prohibits setting a maximum price. | Cap individual dates with a date override instead. |

**Why it is this way.** Each verdict calls for a different response, and none of them is "find a flag that makes it say yes" ([[propose-never-authorise]]). A denial is not an error: no retry, no workaround, no bug report against the tooling. Report the reason in plain language and let the human decide.

**Boundaries.**

- An unknown unit is not a guard verdict. Unit resolution refuses it earlier, and the response is to rebuild the master map, never to force a match.
- A read-back mismatch after execution is not a verdict either. It means stop and investigate before any further change ([[rollback-and-halt]]).

**Verification provenance (commit b09e04a).** Five paths were exercised against live systems: the SHADOW denial, unknown-unit refusal, the approver requirement in APPROVE_EXECUTE, the prohibited-lever message and the oversized-change message. The frequency cap, cooldown, kill switch and fail-closed paths were matched to the guard's text but are not listed as exercised live.

**Drift risk.** The messages are quoted from code that lives in the host project ([[procedure-not-machinery]]). If the guard's wording changes, this table goes stale without any signal in this repository.


## Timeline

- time: 2026-09-18T18:26:46
  kind: decision
  summary: "Created this page: Guard verdicts: each denial is information with its own correct response"
  source: "pricelabs-rate-setter/SKILL.md step 3; commit b09e04a"
  affects: [guard-verdicts]

- time: 2026-09-18T18:26:47
  kind: decision
  summary: "Captured the verdict-to-response mapping as mechanisms, with its verification provenance"
  source: "pricelabs-rate-setter/SKILL.md step 3; commit b09e04a"
  affects: [guard-verdicts]
