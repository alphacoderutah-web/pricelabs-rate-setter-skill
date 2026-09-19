---
id: internal-variant-scoped-trigger
title: "Narrow the internal variant's trigger so it never competes with the portable sibling"
category: decision
status: active
tags: [triggering, scope]
created: "2026-09-18T18:26:46"
updated: "2026-09-18T18:26:46"
---

<!-- compiled_truth -->
**Decided (commit 1967cf3).** Once the portable sibling skill existed, both skills could trigger on the same request to change a price. This skill's description was narrowed to facts that are true only here:

- this portfolio's own unit names;
- its dependency on the host project's guard, rate-setting script and mode file.

It states plainly that it does not work standalone, and redirects any other OwnerRez + PriceLabs portfolio to the portable skill. The README carries the same signposting.

**Why.** Ambiguity between two skills that both write live prices is the wrong kind of ambiguity. A misfire would route a price write through the wrong tooling, or aim it at the wrong portfolio.

**How it resolves.** The portable skill keeps its broad phrasing and stays the default. This one wins only when a request names a unit that exists here, or applies a recommendation from a host-project shadow run.

**Alternatives.** Leaving both descriptions broad was the state being fixed. No other alternative is recorded.

**Consequences.**

- The unit list in the description is load-bearing for triggering. Adding or retiring a unit in the portfolio means updating the description and rebuilding the package in the same change ([[skill-dir-plus-package]]).
- This brain deliberately does not copy those unit names; the skill's frontmatter is the single place they live.
- Fixes to the shared discipline have to reach both variants. How that happens is an open question on the roadmap root page.


## Timeline

- time: 2026-09-18T18:26:46
  kind: decision
  summary: "Created this page: Narrow the internal variant's trigger so it never competes with the portable sibling"
  source: commit 1967cf3
  affects: [internal-variant-scoped-trigger]

- time: 2026-09-18T18:26:46
  kind: decision
  summary: Captured the trigger-narrowing decision from commit 1967cf3
  source: "commit 1967cf3; README.md; SKILL.md description"
  affects: [internal-variant-scoped-trigger]
