# Round 1 (2026-11-10)

Target: commit 0c3bf1d on branch `step-back-lessons` (`git diff 0c3bf1d^..0c3bf1d`).
Spec: the commit message and `.work/v1/plan.md`.

Reviewers: neutral, minimalist, ux (reason: the change is skill text, questions, and docs that a person reads and answers). All on Opus.

## Step back
Verdict: GO
Why: Each of the four changes answers a failure seen in the Yap runs, and each stays small. A FIX that cannot add a mechanism, remarks that reach the user, and a status line are cheap rules that close real gaps. The small path cuts the cost of nit work, and it runs only after the user says yes.
Remarks: None.

## Findings

### 1. A round marked done after CHANGE COURSE lets `start` go on to implement: Major, FIX
Reviewers: neutral. Where: `skills/start/SKILL.md:32`, `skills/review/SKILL.md` step 7, `skills/implement/SKILL.md:10`
Finding: `Status: done` is written before the verdict is acted on. `start` then loads `implement`, and `implement` asked the user only for a stop verdict, so a CHANGE COURSE plan went through.
Resolution: `implement` now asks on a CHANGE COURSE or STOP verdict that the user did not overrule. This also covers `implement` run alone. The hole was there before this commit (the old `start` row had the same gap).

### 2. Reports from before this change look like open rounds: Major, ESCALATE
Reviewers: ux (Major), neutral (Nit). Where: `skills/start/SKILL.md:31-35`, `skills/review/SKILL.md:22-25`
Finding: No report written before 0c3bf1d has a `Status: done` line. `start` on an old finished folder (for example Yap `.work/link-card-cache/`) reopens its last review round.
Resolution: Question to the user: leave it as it is? Recommended: yes. All old folders hold finished work that nobody restarts, and a rule for the old format is a new rule that guards three folders. Cheaper option if the user wants a guard: one README line, "add `Status: done` to an old report".
User: Leave it as it is (2026-11-10). No change.

### 3. The small path offer came after "load its skill": Nit, FIX
Reviewers: ux, neutral. Where: `skills/start/SKILL.md:43-55`
Finding: A model that reads in order loads `plan` before it offers the small path, so `Path: small` is never written.
Resolution: The small path section now comes before the "Tell the user the first stage... then load its skill" line.

### 4. A resumed small path round could start a step back: Nit, FIX
Reviewers: ux, neutral. Where: `skills/review/SKILL.md:24`
Finding: A small path report has no "Step back" section, and step 1 sent a round with an "empty" section to step 6.
Resolution: Step 1 now says "there but empty".

### 5. The small path's "no step back" rule was written twice: Nit, FIX
Reviewers: minimalist. Where: `skills/review/SKILL.md` step 2 and step 6
Finding: Two places to keep the same.
Resolution: The clause in step 2 is deleted. Step 6 holds the rule.

### 6. Two FIX vs. ESCALATE bullets overlap: Nit, FIX
Reviewers: minimalist. Where: `skills/review/SKILL.md:111-117`
Finding: A sorter reads two rules for one decision.
Resolution: Merged into one bullet. The reviewer's reason (the new rule is a special case of the old one) is not right: in the Yap run the gate passed the old rule as "only makes the work correct". The merge is still right, because the old sentence "a fix that only makes the work correct is FIX" contradicted the new rule.

### 7. "a new part" goes beyond the plan: Nit, FIX
Reviewers: neutral. Where: `skills/review/SKILL.md:113-114`
Finding: "a new part" has no limit, so a missing test or input check could become a stop for the user.
Resolution: Removed. The rule now names "a mechanism, an option, or a dependency", as the plan line does. "That runs by itself" also went, so a gate or a queue counts as a mechanism.

### 8. Two "why" comments tell the same Yap story: Nit, FIX
Reviewers: minimalist. Where: `skills/review/personas/step-back.md:36-38`, `skills/review/SKILL.md` step 6
Finding: The step-back comment is a shorter copy of the review comment.
Resolution: The step-back comment is now one line that points to the note in `../SKILL.md`, step 6. A pointer stays, so the rule line keeps a guard where a cleanup would meet it.

### 9. The README does not say how to take the small path without `start`: Nit, DECLINE
Reviewers: ux. Where: `README.md:31-34`
Finding: `plan` accepts the small path when the user chose it, and the README names only `start`.
Resolution: Declined. The README already says each stage takes plain words, and the agreed design is that `start` offers the path.

## All good
- ux: README against skill text for FIX, remarks, and status; the step 7 question; the step back output format; the `start` stop list.
- minimalist: the `Status: done` line, the `start` table rows, `Path: small`, the "Remarks" fields, the README additions, and the plan entries stay.
- neutral: all four plan items are in the diff; AGENTS.md rules hold; `claude plugin validate .` passes.

Status: done
