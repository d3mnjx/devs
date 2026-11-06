---
name: grill
description: Interview the user, one question at a time, to sharpen a rough idea before planning. Use when the user wants to brainstorm, be grilled, or probe an idea.
---

Load and follow the `grilling` skill. If your harness cannot load a skill by
name, read `../grilling/SKILL.md` (relative to this file) and follow it.

One change to `grilling`: ask **one question at a time**, not the whole
frontier. Pick the frontier question that unblocks the most other decisions.
Keep the format: a short question, the options, and your recommended answer,
worded so "yes" accepts it.

<!-- Why a wrapper and not an edit: grilling/SKILL.md stays a byte-for-byte
copy of upstream, so updates from mattpocock/skills are a plain file copy. -->

When the frontier is empty and the user confirms the shared understanding,
suggest the `plan` skill as the next step. Do not write files here.
