---
name: conveyor-layout-recognition
description: >-
  Use when the user provides a conveyor AutoCAD layout (block arrows, conveyor
  IDs, photosensors, and IO points), or connects a drawing through CAD-MCP, and
  needs conveyor type, direction, docking, task transfer, IO, and run logic.
  V1.11 is the English edition of V1.10: forward/reverse follows V1.0; task
  transfer follows V1.8; a transfer unit is ss+yz+sj; tables record task-out
  only; drawing recognition must not consult the PLC program (TIA Portal / Botu,
  FC, upstream or downstream machine). Also matches 输送线布局识图.
---
# Conveyor layout recognition

**Version: V1.11 (2026-10-21)**

V1.11 changes language only (Chinese → English). Rule locks are unchanged from V1.10.

Rule-source lock (do not mix in intermediate editions):

| Rule block | Edition | Point |
|------------|---------|-------|
| **Forward/reverse end** | **V1.0** | On this unit’s axis, a **single-head cyan/light-blue block arrow** → arrow direction = **forward**. Double-head, or no such arrow → use the far-side in-position sensor |
| **Task transfer** | **V1.8** | Cyan block arrow decides whether transfer is allowed (single-head: arrow direction only / double-head: both directions) plus an **in-position interlock on the receiving side**. The table records **task out** only |

Other: a transfer unit is `ss+yz+sj`. A thin yellow arrow is neither a transfer nor a forward/reverse call. **Drawing recognition must not consult the program at any point.**

Further edits require a version bump and user confirmation. Case-specific IDs belong only in examples or project notes.

**The body uses only “this section / next section / this unit”. Concrete IDs may appear only in examples.**

Output labels (English; previous Chinese label in parentheses): **task out** (任务传出), **task in** (任务传入).

---

## 0. Evidence boundary (hard rule)

### Do not consult the program

Drawing recognition (type / high-low / forward-reverse / docking / task out / IO ownership / run logic) **must not consult the program at any point**, including:

- TIA Portal (博图) / Openness / any FC, FB, DB, or OB
- Program sections or comments such as “upstream machine / downstream machine / task-transfer block / enable section / path direction”
- Inferring the drawing from program results, or rewriting a drawing conclusion with program evidence

When the drawing and the program disagree: **the drawing wins**. Differences may be listed separately as “differences versus the program”, but the program **must not** be used to overturn the drawing.  
If the user separately asks to “check the program / edit an FC”, that is **a different task** and must not enter this skill’s drawing-evidence chain.

| Allowed (drawing only) | **Forbidden** |
|------------------------|---------------|
| Outline, **cyan/light-blue block arrows** (including double-head), in-position photosensor text and end position, level, neighbor geometry, IO exported on the drawing | **Any program**; using a **thin yellow arrow** to decide transfer or forward/reverse |

---

## 1. Reading order

Upper level / lower level → type and outline → **§3 forward/reverse end (V1.0)** → **§4 task (V1.8)** → fill the table → IO / logic.  
Read CAD only for the whole pass. **Do not open or compare against the program.**

---

## 2. Type / high-low / pattern

Conveyor: `ss`. Transfer unit: **`ss+yz+sj`** (transfer units in this project all include sj; distinguish them from a pure lift). Lift: `ss+sj` (requires upper/lower comparison, and has no yz).  
Raised = high position `yz`. Lowered = low position `ss`. Pattern ①: high axis up-down / low axis left-right. Pattern ②: high axis left-right / low axis up-down. Judge each unit on its own. Docking lists only the direct neighbor.

---

## 3. Forward/reverse end (edition = V1.0)

**Priority:**

1. **First look at the cyan/light-blue block arrow on this unit’s axis**
   - **Single head**: arrow direction = **forward** on that axis; the tail side = reverse
   - **Double head** or **no cyan block arrow**: the cyan arrow cannot set forward/reverse; go to step 2
2. **Otherwise** use far-side in-position: far-side in-position = the direct run direction from this section to the next section. On a combined photosensor, take only the “in position” element
3. A thin yellow arrow **must not** set the forward/reverse end
4. **Do not** use program signal names, upstream/downstream machine, or FC comments to set forward/reverse

> Note: V1.6–V1.7 said “a cyan arrow does not set forward/reverse; look only at far-side in-position”. **That is repealed.** Forward/reverse always follows this section, i.e. V1.0.

---

## 4. Task transfer (edition = V1.8)

| Cyan arrow | Transfer |
|------------|----------|
| Single head | Arrow direction only |
| Double head | Both directions on that axis |

After A→B has a cyan arrow: receiving side B must have an in-position sensor for the matching run direction (entering along B’s forward → forward in-position; against B’s forward → reverse in-position). If that sensor is missing, there is no path.  
Forward itself is already decided by §3 (V1.0) and may be the same direction as a single-head cyan arrow.

**This unit writes outbound only**: only **`task out`**, `→ neighbor`, with multiple items separated by `;`. Do not add a “task in” column.  
A thin yellow arrow **is not** a task transfer.  
**Do not** fill task-out from an FC task block, an enable section, or a program column on an old table.

---

## 5. Deliverables

Master ID table (including pattern ①/②) + docking column + **task out** + IO + logic / to verify.  
Optional: a drawing-versus-program difference list (annotate only; do not overturn the drawing).

## 6. Quality gates (a hit here is wrong)

- Using the **program** to decide forward/reverse, docking, task, type, or high/low
- Using a thin yellow arrow to decide transfer or forward/reverse
- Treating a double-head cyan arrow as one-way and setting forward from it
- Overriding forward/reverse with far-side in-position when a single-head cyan arrow is present
- Treating a single head as bidirectional transfer
- Writing a path when a cyan arrow exists but the receiving side has no in-position sensor
- Writing a “task in” column on this unit’s row
- A transfer unit missing sj (written as only ss+yz)

## 7. Version comparison

| Edition | Forward/reverse end | Task transfer | Program |
|---------|---------------------|---------------|---------|
| about **V1.0** | Block arrow ≈ forward (this edition’s forward/reverse rule) | — | — |
| V1.6–V1.7 | ~~Cyan arrow does not set forward/reverse~~ (repealed) | Cyan block arrow + in-position interlock, early form; from V1.7 a transfer unit must include sj | Allowed program evidence “when the user asks separately” (repealed) |
| **V1.8** | Had written “single-head cyan arrow first” | **This edition’s task rule** | Program forbidden (for drawing recognition) |
| V1.9 | Locked to V1.0 | Locked to V1.8 | Program forbidden |
| **V1.10** | Locked to V1.0 | Locked to V1.8 | **Hard rule: drawing recognition must not consult the program** |
| **V1.11** | Same as V1.10 | Same as V1.10 | Same as V1.10. **Language change only: Chinese → English** |
