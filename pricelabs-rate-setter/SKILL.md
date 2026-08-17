---
name: pricelabs-rate-setter
description: FreeWyld / Saint Augustine Vacations INTERNAL rate setter. Sets or rolls back base and minimum prices for this portfolio's own units — the Cinnamon Beach homes (22, 224, 530, 537 Family Tides, 540, 546, 7 Palms), Vilano Beach (206 Dream Beach Haven, 505 Beachside Getaway, 604), St. George (266 and 309 Zion's, 370 Desert Haven, 553 Vida Sol) and 124 Jacksonville — by driving lib/guard.py and scripts/set_rates.py inside the FreeWyld Agent project. Use when a rate change names one of those units, or when applying a recommendation from a shadow run in this project. Requires that project's code and .env; it does not work standalone. For any OTHER OwnerRez + PriceLabs portfolio, use the portable `ownerrez-rate-setter` skill instead.
---

# PriceLabs rate setter (FreeWyld internal)

The only sanctioned path from "we should change a price" to a price actually changing — **for this portfolio**. Its companion skill `ownerrez-listing-builder` deliberately refuses to touch rates; this is where that work lands.

> **Which skill is this?** This one is wired to the FreeWyld Agent project: it drives that repo's `lib/guard.py`, `scripts/set_rates.py` and `analysis/AUTONOMY-MODE.json`, and knows this portfolio's specific units and history. If the portfolio in question is somebody else's, stop and use **`ownerrez-rate-setter`** instead — same discipline, ships its own tooling, works anywhere.

A booking taken at a wrong rate cannot be recalled. That asymmetry — cheap to be slow, expensive to be wrong — is why every part of this runs through `lib/guard.py`, and why the guard's refusals matter more than its approvals.

## The one rule

**You may propose. Only a named human may authorise.** Everything below is an elaboration of that. When the guard says no, your job is to report why and stop — not to find a flag that makes it say yes.

## Non-negotiables

1. **Dry run first, every time.** `scripts/set_rates.py` writes nothing without `--execute`. Run it bare, read the verdict, show the human, and only then consider executing. The dry run costs seconds and is the whole safety margin.
2. **Never add `--execute` on your own initiative.** It goes on the command line only when the human has asked for *that specific change* to be applied. "Have a look at pricing" is not authorisation to write.
3. **Never invent `--approved-by`.** That field records who is accountable. Putting a name there that the person did not give you is falsifying an audit record. If APPROVE_EXECUTE mode needs an approver and nobody has named themselves, stop and ask.
4. **Never reach for `--override-caps` to get around a denial.** The caps (±7%, two changes per unit per week, three-day cooldown) exist because the operator's real −19% moves are exactly what they are meant to catch. An override is a separate, explicit decision a human makes knowing they are exceeding a limit.
5. **A denial is not an error.** Don't retry, don't work around it, don't treat it as a bug in the tooling. Report the reason in plain language and let the human decide.
6. **Mode is per-unit and mostly SHADOW.** Globally the project is read-only. At time of writing only `124 Jacksonville` is APPROVE_EXECUTE. A unit you were not told about is almost certainly not writable, and the guard will say so.

## Order of operations

```
pin the unit → read live state → sanity-check the number → dry run
   → show the verdict → (human authorises) → execute → verify read-back → Sync Now
```

## Step 1 — Pin the unit and read what is actually live

Identity comes from the master map, never from a name that happens to match. `set_rates.py` resolves it for you and refuses unknown names, which is the behaviour you want.

```bash
cd "C:\Users\palmc\FreeWyld Agent"
python scripts/set_rates.py --unit "22 Cinnamon Beach" --base 440
```

Run with no `--execute` and it prints the unit, account, listing id, the **live** base/min/max, the effective mode, and a guard verdict per change. Read the live values before trusting any number you were given — a base quoted from a report may be hours or days stale.

If the unit is not in the master map, rebuild it rather than forcing a match:

```bash
python scripts/build_master_map.py
```

## Step 2 — Sanity-check the number before you propose it

The mechanics are safe; the judgment is where money is lost. This project has already made each of these mistakes, so check them explicitly:

- **Base is a global lever.** It moves every date, including next year's peak. A soft single month is fixed with a date-limited override, not a base cut. (537 Family Tides: a weak September drew a 19% base cut plus a promo that landed on peak July dates.)
- **Check fee load before cutting.** If the guest-facing price is high because of *fees*, cutting rent cannot fix it and permanently lowers the anchor. Run `python scripts/revenue_report.py` and compare rent against guest-facing.
- **Don't raise on a unit with nothing left to sell.** A base raise on a sold-out calendar is noise.
- **Check the channel side.** Airbnb and VRBO discounts stack on top of whatever PriceLabs pushes, and have already driven a realised rate below its own floor. `analysis/shadow/channel-promos.json` holds the last attended capture.

If a shadow run already recommended this change, its reasoning is in `analysis/shadow/latest-report.md` — cite it rather than re-deriving.

## Step 3 — Read the guard's verdict properly

Each denial means something specific and calls for a different response.

| Verdict | What it means | What to do |
|---|---|---|
| `mode is SHADOW` | This unit has no write authority. | Report it. Authority is a Phase 1 decision (roadmap item 4), not something to route around. |
| `KILL SWITCH ACTIVE` | Something tripped the halt, possibly the circuit breaker. | Find out *why* before anyone releases it. Read `KILL-SWITCH` at the project root and the recent audit log. |
| `change X exceeds cap 7%` | Size cap. | Propose a smaller change, or take the override question to the human explicitly. |
| `N change(s) already applied … in the last 7 days` | Frequency cap. | Wait, or escalate deliberately. Repeated same-week edits are usually chasing noise. |
| `cooldown: … within the 3-day cooldown` | A change needs time to show an effect. | Wait. Re-cutting after one day is how the oscillation pattern started. |
| `APPROVE_EXECUTE mode - human approval required` | Working as designed. | Get a real name from the human, then pass `--approved-by`. |
| `GUARD FAILED CLOSED: …` | Config is missing or unreadable. | Fix the config. Never bypass — failing closed is the guard working. |
| `field 'max' is not a permitted lever` | Setting a maximum price is prohibited by the method. | Cap individual dates with an override instead. |

## Step 4 — Execute, once a human has actually asked

```bash
python scripts/set_rates.py --unit "124 Jacksonville" --base 250 --execute \
    --reason "market ADR moved; shadow run 2026-08-12" --approved-by Cameron
```

`--reason` lands in the permanent audit record. Write it for someone reading the log in six months with no memory of this conversation: what moved, and what evidence.

In APPROVE_EXECUTE mode the tool prompts and requires the unit name typed back. That prompt is a human checkpoint — if you are running non-interactively you may pass `--yes`, but only when the human has already approved this exact change in conversation. Using `--yes` to skip a confirmation nobody gave defeats the point.

## Step 5 — Verify, then sync

A `200` is not proof the value stuck. The tool reads every applied change back and reports a mismatch; if it does, **stop and investigate before making further changes** rather than retrying.

Then tell the human to press **Sync Now** in PriceLabs. Without it, changes can take about a day to reach the channels — long enough for someone to conclude the change didn't work and cut again.

## Rolling back

Every applied change is reversible from the audit log.

```bash
python scripts/rollback.py --unit "22 Cinnamon Beach"            # plan only
python scripts/rollback.py --run-id set_rates-2026-08-12 --execute --approved-by Cameron
```

Rollback plans by default and still passes through the guard — it cannot be used as a side door. `--emergency` clears the cooldown and frequency caps *only*; it can never override the kill switch or the mode, because a halted system should not be writing at all.

## When something looks wrong mid-flight

Stopping is cheap. If the portfolio is moving in a way you cannot explain, halt writes and investigate:

```bash
python -c "import sys; sys.path.insert(0,'.'); from lib import guard; guard.engage_kill_switch('claude','<why>')"
```

Release is a human decision, and the reason should be understood first.

## What this skill does not do

- **Airbnb or VRBO promotions.** No API path exists; those are set by hand in an attended session.
- **Availability, minimum-night stays, or listing content.** Content belongs to `ownerrez-listing-builder`.
- **Deciding whether a unit should be writable.** Mode changes are the human's call, recorded in `analysis/AUTONOMY-MODE.json`. The agent may never advance its own mode.

## Reference

| Path | What it holds |
|---|---|
| `lib/guard.py` | The choke point. Mode, caps, kill switch, approvals, audit. |
| `lib/pricelabs_write.py` | The only module that may issue a mutating PriceLabs call. |
| `analysis/AUTONOMY-MODE.json` | Global and per-unit modes. |
| `analysis/PHASE1-DECISIONS.json` | The caps, and the objective: RevPAR vs market. |
| `analysis/audit/write-audit.jsonl` | Every decision, allowed and denied. |
| `AUTONOMY-PROTOCOL.md` | Standing prohibitions and authorisations. |
