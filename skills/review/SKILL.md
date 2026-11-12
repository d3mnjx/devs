---
name: review
description: Review a plan or code with fresh-context reviewer personas (neutral, minimalist, skeptic, and security, performance, or UX when needed), sort the findings, apply the clear fixes, get a step back verdict, and show the user the skeptic's cons and the judgment calls. Use when the user wants a review, e.g. "review the plan" or "review this with security and skeptic".
---

You are the main session. You keep the context; the reviewers do not.
Your job is to run the reviewers, sort what they find, fix what is clear, and
bring the user only the decisions that need them.

## 1. Pick the target

- Find the feature folder in `.work/`. If more than one could match, ask.
- **Plan or code?** Use what the user says. Otherwise: if
  `.work/<feature>/base-commit` exists and commits follow it, review the
  **code**; else review the **plan**.
- Plan target: `.work/<feature>/plan.md`.
- Code target: `git diff <base-commit>...HEAD`, plus `plan.md` as the spec.
- **Commit range** (the user names commits, for example "the last 7
  commits"): `git diff <first>^..<last>`. The spec is the commit messages
  and any plan or issue they point to. The report goes to
  `.work/review-<first>..<last>/`.
- **An open round.** If the last round in the report has no `Status: done`
  line, an earlier session stopped before the user answered. Do not start a
  new round. Continue that one: at step 6 when its "Step back" section is
  there but empty, else at step 7 with the items that have no user answer.

## 2. Pick the reviewers

Persona files are in `personas/` next to this file.

- **Always:** `neutral.md`, `minimalist.md`.
- **On every plan; on code only when the user names it:** `skeptic.md`. It
  gives the cons of the whole thing. A bad plan is cheapest to stop before
  the build.
- **When the target touches the area, or the user names it:**
  - `security.md`: input from users, network, or files; auth; permissions;
    secrets; crypto; shell commands; new dependencies.
  - `performance.md`: data that can grow; loops over it; queries; network
    calls; hot paths; startup; large files.
  - `ux.md`: anything a person sees or types: UI, CLI, API shape, error
    messages, docs.
- The user can name any persona in plain words. A name with no file becomes a
  one-line brief from the user's words.
- **Small path** (`plan.md` has a `Path: small` line), code target:
  `neutral.md` only, plus any persona the user names. The user chose the
  short way for a small change.

<!-- Why a small path: in the Yap review-fixes run (2026-11-08) five nit
fixes, 49 lines in all, got a plan review and a code review with 8 agents,
after 6 more for the review that found the nits. The full set of reviewers
is for work that carries a design. -->

Tell the user the list in one line, with the reason for each conditional one
and the model the reviewers run on (see step 3).

## 3. Run the reviewers

Start one **fresh subagent per persona, in parallel**, with no chat history.
Fresh context is the point: a reviewer that saw our conversation shares our
blind spots. If your harness cannot start fresh subagents, stop and tell the
user. Do not review in this session instead.

**Model.** Run every reviewer on the model of this session, unless the user
names another model for the reviewers. Name the model when you start the
subagent, if your harness lets you; do not leave it to a default. A general
rule to send subagent work to a cheaper model does not cover reviewers:
their work is judgment, not mechanical work.

<!-- In a Yap plan review (2026-11-12) a Fable session ran all seven
reviewers on Opus. This skill named no model, so a workspace cost rule
("send subagent work to a cheaper model") filled the gap, and the user saw
it only after the round. There is no question to the user on purpose: the
answer is nearly always "the same model", and the line in step 2 shows the
model before the reviewers report. The report records the model because
runs on unknown models do not compare (see test-cases/README.md). -->

Give each reviewer this brief, and nothing from our conversation:

- The persona file text: its intro and the section for this target
  ("On a plan" or "On code"). If the persona file points to another file
  (for example `../../principles.md`), paste that file's text too. The
  reviewer cannot resolve paths relative to the persona file.
- The target: the `plan.md` path, or the diff command and `plan.md` path.
  The reviewer may read any file in the repo, except the skeptic on a
  plan: give it the `plan.md` path only.
- This output contract (the skeptic's file has its own; use that one for
  the skeptic):

  > Return findings only, most severe first, under 400 words. For each:
  > **title**, **severity** (Blocker / Major / Nit), **where** (file:line or
  > plan section), **problem** (a concrete case: this input or situation
  > leads to this wrong result), **fix** (the smallest one). No case, no
  > finding. If you find nothing in your area, say "All good" and what you
  > checked. That is a valid result. Never invent an issue to have
  > something to report. Change no files.

Add nothing else to a brief. Do not tell a reviewer what is settled, what the
user chose, or what not to question. No decision is protected from review,
the user's included.

<!-- In the Yap link-card run (2026-11-09) the main session added "decisions
were chosen by the user; do not propose cutting a whole decision" to the
briefs. That was false for a value the agent had picked, and it silenced the
one reviewer whose job was to ask if the thing should exist. -->

## 4. Sort the findings

Merge findings that describe the same problem into one entry, and keep all
reviewer names on it. Then, for each finding, **open the cited code or plan
section first**. Never label from the reviewer's text alone. The skeptic's
cons are not sorted here; see "The skeptic's cons" below. Give one label:

- **FIX**: the finding is right and the fix is clear. Apply it.
- **CONTEST**: the finding is wrong. Cite evidence anyone can check: a file
  and line, a test, a decision in `plan.md`. "It is on purpose" is not
  evidence, and a decision is not evidence against a finding that attacks
  that same decision.
- **ESCALATE**: the finding needs the user's knowledge or a judgment call
  (scope, product, trade-off, cost). Write it as a question with your
  recommended answer.
- **DECLINE**: the finding is right but too small to matter (a nit). Record
  it, change nothing.

Rules:
- Keep the reviewer's severity. If you disagree, CONTEST with reasons.
- FIX vs. ESCALATE: a FIX only corrects or removes. A fix that adds a
  mechanism, an option, or a dependency, that changes what the user gets,
  or that costs real time is ESCALATE, however clear it looks. For an
  addition, ask the user with "without it" as one of the options.
  <!-- In the Yap link-card run (2026-11-09) a plan-review FIX added a
  download gate with no question. The gate then caused three code-review
  findings, two of them Major. A reviewer's fix gets no review of its own
  (one round only), so the user is the only check on what it adds. -->
- A finding against a `(user)` decision is always ESCALATE. Never apply it,
  contest it, or decline it yourself: the user asked to be checked too.
  Give the user the reviewer's cost and cheaper option, not a softer
  version.
- When a finding says a mechanism costs too much, do not only make it
  cheaper. First answer whether the mechanism is needed at all.
- Plan target: apply FIX items to `plan.md`.
- Code target: apply FIX items to the code, run the project's checks, and
  commit them as "review fixes" with a pointer to the report.
- **Report only:** when the code is already on the default branch, or the
  user asks for a report, change nothing. FIX then means "clear and ready
  to apply". Say so at the top of the report.

### The skeptic's cons

Do not label, filter, merge, or reword the cons. You wrote or steered the
work, so you are a biased judge of them. Copy each con into the report as
written, and add one line of your own under it: "Agree: <what would
change>" or "Disagree: <evidence anyone can check>". Change nothing for a
con until the user says so.

## 5. Write the report

Write `.work/<feature>/plan-review.md` or `code-review.md`. If the file
exists, append a new `# Round N` section; do not delete the old round.

```markdown
# Round 1 (YYYY-MM-DD)

Reviewers: neutral, minimalist, skeptic, security (reason: parses uploaded files)
Model: <the model the reviewers and the step back agent ran on>

## Step back
Verdict: GO | CHANGE COURSE | STOP
Why: <the step back agent's text>
Instead: <only for CHANGE COURSE or STOP>
Remarks:
1. <the remark, as the step back agent wrote it>
   Answer: Agree: <what would change> | Disagree: <evidence>
   User: <the user's decision, filled in at step 7>

## Skeptic's cons
1. <the con, as the skeptic wrote it, with where and price>
   Answer: Agree: <what would change> | Disagree: <evidence>
   User: <the user's decision, filled in at step 7>

## Findings
### 1. <title>: <severity>, <LABEL>
Reviewers: <names>. Where: <location>
Finding: <one or two lines>
Resolution: <what was changed | the evidence | the question and the user's answer | why declined>
```

The round stays open until the user has answered at step 7. Write no
`Status:` line here; step 7 adds it. `start` and a later `review` read a
round without that line as not finished.

## 6. Step back

Start one more **fresh subagent** with `personas/step-back.md`. Give it the
`plan.md` path (or the commit range), the report path, and for code the
output of `git diff --stat` on the target. The rules from step 3 hold here
too: run it on the same model as the reviewers, and add nothing about what
is settled or what the user chose. Write its answer into the report's "Step
back" section.

Treat its remarks like the skeptic's cons: copy each one as written, add
your one-line "Agree" or "Disagree" answer under it, and change nothing for
a remark until the user says so.

<!-- In the Yap runs step back said GO six times of six, and five of those
texts held a point that no reviewer made. A GO moved the chain on, so the
point reached nobody: "fix 5... You can drop it" was built anyway, and the
remark about card pictures in the upload decode queue came back as a Major
finding in the code review. The verdict alone is too narrow a pipe. -->

Small path, code target: there is no step back. Leave the "Step back"
section out of the report.

## 7. Tell the user

Keep it short:

1. **Step back verdict** with its 1 or 2 lines of why.
2. **One count line:** "Fixed N, contested N, declined N, escalated N" and
   the report path.
3. **The skeptic's cons and the step back remarks**, all of them, as
   written, each with your one-line answer. Ask once for both lists: "Which
   of these should change the work?" Wait. Apply what the user picks, and
   record the answer in the report. A GO verdict does not answer a remark;
   only the user does. No cons and no remarks: no question.
4. **Escalated items, one at a time:** the question and your recommended
   answer, worded so "yes" accepts it. Wait for each answer. Apply it, and
   record it in the report.

When the cons, the remarks, and all escalations have answers, add
`Status: done` as the last line of the round. Then act on the verdict:

- Plan review GO: load the `implement` skill, unless the user asked for the
  review only.
- Code review GO, or a small path review with no verdict: done. Tell the
  user the branch is ready to merge. Never merge or push.
- CHANGE COURSE or STOP: stop and ask the user. Recommend `plan`,
  `implement`, or an end. The verdict is advice; the user decides.

One round only. Run a second round only when the user asks.
