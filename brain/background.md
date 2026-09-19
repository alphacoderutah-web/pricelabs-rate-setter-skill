---
slug: background
title: Project background
role: project background
updated: "2026-09-18T18:26:31"
---

# Project background

## Why

Changing a live short-term-rental price is asymmetric: being slow costs little, while a booking taken at a wrong price cannot be recalled. This repository holds the operating procedure a Claude Code agent must follow to turn "we should change a price" into a price that has actually changed in PriceLabs, and nothing looser. Its governing rule is **you may propose; only a named human may authorise** ([[propose-never-authorise]]).

The failure mode it is written against is agent self-escalation: meeting a guard denial and reaching for an override flag, or recording an approver nobody named.

Throughout this brain, "the host project" means the separate pricing-agent project whose scripts and guard this skill drives. Its code is not in this repository.

## Goals

- Be the single sanctioned path to a price change for one specific portfolio: this is the internal variant ([[internal-variant-scoped-trigger]]).
- Force the fail-closed sequence every time: pin the unit, read live state, check the judgment, dry run, show the guard verdict, wait for a named human, execute, verify the read-back, prompt for a channel sync, and keep rollback available.
- Teach the agent to read a guard denial as information with a specific correct response, never as an obstacle ([[guard-verdicts]]).
- Stay shareable on its own, without the host project's internal analysis ([[procedure-not-machinery]]).

## Non-goals

- Shipping the machinery. The guard, the rate-setting script, the mode file and the audit log live in the host project; this repository alone is not a working rate setter.
- Serving any portfolio other than the one it is wired to. A separate portable sibling skill (`ownerrez-rate-setter`) covers other OwnerRez + PriceLabs accounts.
- Channel (Airbnb / VRBO) promotions. There is no API path; they are set by hand in an attended session.
- Availability, minimum-night stays and listing content. Listing content belongs to the companion `ownerrez-listing-builder` skill, which deliberately refuses to touch rates and hands pricing to this one.
- Deciding whether a unit is writable. Mode changes are a human decision; the agent may never advance its own mode.
- Setting pricing policy. The skill carries a judgment checklist, not a pricing strategy, and this brain deliberately records no rates, limits or figures.

## Target user

- **Primary:** a Claude Code agent working inside the host project. The skill's description triggers it when a request names one of that portfolio's units, or applies a recommendation from a host-project shadow run.
- **Secondary:** the named human who authorises each change, whose name becomes the accountable approver in the audit record, and who presses Sync Now in PriceLabs afterwards.

## Evidence and confidence

- Evidenced: everything above comes from `README.md`, `pricelabs-rate-setter/SKILL.md` and the three commit messages.
- Everything said about guard behaviour is as documented by the skill, not independently verified here, because the host project's code is not in this repository.

## Open questions

- No success criteria are stated anywhere in the repository (how anyone would measure that the procedure works). Unknown; needs the owner.
- The README frames this repository as a home for several skills (it names the other OwnerRez skills as candidates to move here), while the repository name is single-skill. Which framing is intended is not settled.
