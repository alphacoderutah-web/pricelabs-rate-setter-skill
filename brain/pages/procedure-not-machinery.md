---
id: procedure-not-machinery
title: "Ship only the procedure; the rate-setting machinery stays in the host project"
category: decision
status: active
tags: [architecture, scope, sharing]
created: "2026-09-18T18:26:46"
updated: "2026-09-18T18:26:46"
---

<!-- compiled_truth -->
**Decided.** This repository holds only the operating procedure. The rate-setting script, the guard, the writing module, the mode file, the cap configuration and the audit log stay in the separate host project, and the skill drives them by relative path. Cloning this repository gives you the procedure, not a working rate setter. The skill's description says outright that it does not work standalone.

**Why.**

- The skill is versioned apart from the host project so it can be shared, reviewed and reused without the host's internal analysis. The README describes that analysis as personnel assessments, financial detail and live pricing strategy.
- The README calls the procedure the part worth reusing: propose but never self-authorise, dry run first, treat a denial as information rather than an obstacle.

**Alternative, taken elsewhere.** A portable sibling skill (`ownerrez-rate-setter`) ships its own tooling and works with any OwnerRez + PriceLabs account. This repository stays the internal variant, and its trigger is narrowed so the two never compete ([[internal-variant-scoped-trigger]]).

**Consequences.**

- There is no executable code, configuration or test suite here. What the skill says about guard behaviour is documented, and cannot be verified from this repository alone.
- The verdict table in the skill mirrors messages in the host's guard and can drift silently if that wording changes ([[guard-verdicts]]).
- The same reasoning governs this brain: it records mechanisms only, and no rates, limit values, unit identities or names.

**Evidence.** README ("A note on dependencies"); commit b09e04a; the skill description.


## Timeline

- time: 2026-09-18T18:26:46
  kind: decision
  summary: "Created this page: Ship only the procedure; the rate-setting machinery stays in the host project"
  source: "README.md; commit b09e04a"
  affects: [procedure-not-machinery]

- time: 2026-09-18T18:26:46
  kind: decision
  summary: Captured the procedure/machinery split and why the skill is versioned apart
  source: "README.md; SKILL.md description; commit b09e04a"
  affects: [procedure-not-machinery]
