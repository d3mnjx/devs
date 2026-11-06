# devs

A Claude Code plugin of skills for a dev process with roles. See README.md.

- Keep the skills short and in plain, harness-neutral words. Do not name
  Claude Code tools (Agent, Skill, Bash) in skill text; say "start a fresh
  subagent" or "load the skill".
- `skills/brainstorm/grilling.md` is a byte-for-byte copy of upstream
  `grilling/SKILL.md`. Do not edit it. Put changes in
  `skills/brainstorm/SKILL.md`.
- `.work/v1/plan.md` holds the decisions behind v1 and the rejected options.
  Read it before you change the design.
- Validate after changes: `claude plugin validate .`
