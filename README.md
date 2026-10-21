# Conveyor layout recognition skill

Cursor / Grok skill for reading conveyor AutoCAD layouts.

## Current version

**V1.11 (2026-10-21)**

V1.11 is the English edition of V1.10. The rules are unchanged.

Rule-source lock (do not mix in intermediate editions):

| Rule block | Edition | Point |
|------------|---------|-------|
| **Forward/reverse end** | **V1.0** | A single-head cyan/light-blue block arrow → arrow direction = forward. Double-head, or no arrow → use far-side in-position |
| **Task transfer** | **V1.8** | Cyan block arrow (single-head: arrow direction only / double-head: both directions) plus a receiving-side in-position interlock. The table records “task out” only |

Other: a transfer unit is `ss+yz+sj`. A thin yellow arrow is neither a transfer nor a forward/reverse call. **Drawing recognition must not consult the program.**

## Install

Copy `conveyor-layout-recognition/` into the agent’s workflows (or skills) directory.

## Links

- Body: [`conveyor-layout-recognition/SKILL.md`](./conveyor-layout-recognition/SKILL.md)
- Raw: https://github.com/zzll3/conveyor-layout-skill/raw/main/conveyor-layout-recognition/SKILL.md
