# FreeWyld Claude Code skills

Versioned separately from the FreeWyld Agent project so skills can be shared, reviewed and
reused without carrying that repo's analysis — which contains personnel assessments,
financial detail and live pricing strategy.

## Skills

| Skill | What it does |
|---|---|
| `pricelabs-rate-setter` | The sanctioned path from "we should change a price" to a price actually changing. Pins the unit, forces a dry run, reads the guard's verdict, handles per-change human approval, verifies the read-back, and covers rollback. |

## Installing

Claude Code discovers skills in `.claude/skills/` relative to the project, or in
`~/.claude/skills/` for every project. Clone into whichever fits:

```bash
# per-project
git clone <this-repo> "C:/Users/palmc/FreeWyld Agent/.claude/skills"

# or available everywhere
git clone <this-repo> ~/.claude/skills
```

## A note on dependencies

`pricelabs-rate-setter` drives scripts that live in the **FreeWyld Agent** project
(`scripts/set_rates.py`, `lib/guard.py` and the rest). The skill is the operating procedure;
the project holds the machinery. Cloning this repo alone gives you the procedure, not a
working rate setter.

That separation is deliberate. The procedure — propose but never self-authorise, dry run
first, treat a denial as information rather than an obstacle — is the part worth reusing.

## Related

`ownerrez-listing-builder`, `ownerrez-photo-captions` and `ownerrez-rezzy-messaging`
currently live at user level in `~/.claude/skills/`. They are candidates to move here if you
want one place for all of them; `ownerrez-listing-builder` in particular is the deliberate
complement to `pricelabs-rate-setter`, since it refuses to touch rates and hands pricing off.
