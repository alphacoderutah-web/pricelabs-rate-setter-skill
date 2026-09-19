---
id: skill-dir-plus-package
title: "Publish the skill as a directory plus an installable .skill zip, kept in lockstep"
category: decision
status: active
tags: [packaging, distribution]
created: "2026-09-18T18:26:46"
updated: "2026-09-18T18:26:46"
---

<!-- compiled_truth -->
**Decided (commit 0bc663e).** The repository carries the skill two ways:

- `pricelabs-rate-setter/SKILL.md`, the source;
- `pricelabs-rate-setter.skill`, a zip containing only that SKILL.md, for installing.

This matches a layout already used by a sibling skill repository. A small `.gitignore` for editor and OS noise came in the same commit.

**Invariant: the package must match the source.** Commit 1967cf3 changed SKILL.md and rebuilt the package in the same commit, which is the practice to keep. On 2026-09-18 the packaged SKILL.md matched the source byte for byte once line endings were normalised. The working-tree copy was CRLF under that checkout's Git line-ending setting; the zip holds LF.

**Gaps (open).**

- The rebuild is manual. There is no build script, consistency check or CI in the repository.
- The zip's entry is stored as `pricelabs-rate-setter\SKILL.md`, with a backslash separator. The zip format expects forward slashes, so some extractors on non-Windows platforms may produce a file literally named with a backslash instead of a folder. Not tested; low confidence.

**Blast radius.** Any change to the description, including the unit list that drives triggering ([[internal-variant-scoped-trigger]]), reaches installs made from the zip only after the zip is rebuilt.


## Timeline

- time: 2026-09-18T18:26:46
  kind: decision
  summary: "Created this page: Publish the skill as a directory plus an installable .skill zip, kept in lockstep"
  source: commit 0bc663e
  affects: [skill-dir-plus-package]

- time: 2026-09-18T18:26:46
  kind: decision
  summary: "Captured the packaging decision and the package-matches-source invariant (checked 2026-09-18)"
  source: "commits 0bc663e and 1967cf3; offline comparison of the zip against SKILL.md"
  affects: [skill-dir-plus-package]
