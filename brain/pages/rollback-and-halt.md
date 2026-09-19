---
id: rollback-and-halt
title: "Rollback and the kill switch: reversal stays inside the guard, halting is always cheap"
category: concept
status: active
tags: [recovery, guard, kill-switch]
created: "2026-09-18T18:26:47"
updated: "2026-09-18T18:26:47"
---

<!-- compiled_truth -->
**Definition.** The two recovery mechanisms the skill documents. Reversal happens inside the guard, and stopping is always cheap.

**Rollback.**

- Every applied change can be reversed from the host project's audit log.
- `scripts/rollback.py` plans by default, by unit or by run id. It executes only with `--execute` and a named `--approved-by` ([[propose-never-authorise]]).
- Rollback passes through the guard like any other write, so it cannot be used as a side door.
- `--emergency` clears only the cooldown and frequency caps. It can never override the kill switch or the mode, because a halted system should not be writing at all. Inferred consequence: a unit without write authority cannot be rolled back through this path either.

**Halt (kill switch).**

- The agent may engage the kill switch through the guard, giving a reason, whenever the portfolio is moving in a way nobody can explain. A circuit breaker can also trip it.
- While it is active, every write is refused ([[guard-verdicts]]), including rollback.
- Releasing it is a human decision, and the cause should be understood first.

**After a write.** A success response is not proof the value stuck. The rate-setting script reads back every applied change. On a mismatch, stop and investigate before making further changes; do not retry.

**Why.** Stopping costs little, and an unexplained write is exactly what the guard exists to prevent. Emergency rollback is scoped to the limits that only slow things down (cooldown and frequency), never to the ones that decide whether writing is allowed at all (mode and kill switch).

**Open questions.**

- May the agent choose `--emergency` without explicit human instruction? The skill is silent. The stricter reading is no: treat it like `--override-caps`.
- What trips the circuit breaker is not described in this repository.


## Timeline

- time: 2026-09-18T18:26:47
  kind: decision
  summary: "Created this page: Rollback and the kill switch: reversal stays inside the guard, halting is always cheap"
  source: pricelabs-rate-setter/SKILL.md rollback and mid-flight sections
  affects: [rollback-and-halt]

- time: 2026-09-18T18:26:47
  kind: decision
  summary: "Captured rollback, emergency limits, kill switch and read-back handling"
  source: "pricelabs-rate-setter/SKILL.md steps 5, rollback and mid-flight sections"
  affects: [rollback-and-halt]
