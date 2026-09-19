---
slug: mindmap
title: Feature mindmap
role: feature mindmap
updated: "2026-09-18T18:26:31"
---

# Feature mindmap

## Feature mindmap

Branches of the skill as it stands (evidenced from `pricelabs-rate-setter/SKILL.md` and `README.md`).

```mermaid
mindmap
  root((pricelabs-rate-setter))
    Governing rule
      Propose, never authorise
      Named human approver
      No self-escalation
    Write procedure
      Pin the unit
      Read live state
      Judgment check
      Dry run
      Execute on explicit request
      Verify read-back
      Sync Now
    Guard verdicts
      Per-unit mode
      Size cap
      Frequency cap
      Cooldown
      Kill switch
      Fail closed
      Approval required
      Permitted levers only
    Recovery
      Rollback plan
      Rollback execute
      Emergency limits
      Halt writes
    Scope
      One portfolio only
      Portable sibling for others
      No channel promotions
      No listing content
      No mode changes
    Packaging
      Skill directory
      Installable zip
      Host project dependency
```

Where each branch lives in the brain:

- Governing rule: [[propose-never-authorise]]
- Guard verdicts: [[guard-verdicts]]
- Recovery: [[rollback-and-halt]]
- Scope: [[internal-variant-scoped-trigger]] and [[procedure-not-machinery]]
- Packaging: [[skill-dir-plus-package]]
- Write procedure: the flow root page
