# Workflow: Plan Phase

<required_reading>
**Read these files NOW:**
1. templates/phase-prompt.md
2. references/plan-format.md
3. Read `.planning/ROADMAP.md`
4. Read `.planning/BRIEF.md`
</required_reading>

<purpose>
Create an executable phase prompt (PLAN.md). This is where we get specific:
objective, context, tasks, verification, success criteria, and output specification.

**Key insight:** PLAN.md IS the prompt that Claude executes. Not a document that
gets transformed into a prompt.
</purpose>

<process>

<step name="identify_phase">
Check roadmap for phases:
```bash
cat .planning/ROADMAP.md
ls .planning/phases/
```

If multiple phases available, ask which one to plan.
If obvious (first incomplete phase), proceed.

Read any existing PLAN.md or FINDINGS.md in the phase directory.
</step>

<step name="check_research_needed">
For this phase, assess:
- Are there technology choices to make?
- Are there unknowns about the approach?
- Do we need to investigate APIs or libraries?

If yes: Route to workflows/research-phase.md first.
Research produces FINDINGS.md, then return here.

If no: Proceed with planning.
</step>

<step name="gather_phase_context">
For this specific phase, understand:
- What's the phase goal? (from roadmap)
- What exists already? (scan codebase if mid-project)
- What dependencies are met? (previous phases complete?)
- Any research findings? (FINDINGS.md)

```bash
# If mid-project, understand current state
ls -la src/ 2>/dev/null
cat package.json 2>/dev/null | head -20
```
</step>

<step name="break_into_tasks">
Decompose the phase into 5-10 tasks.

Each task must have:
- **Task name**: Clear, action-oriented
- **Files**: Which files created/modified
- **Action**: Specific implementation (including what to avoid and WHY)
- **Verify**: How to prove it worked
- **Done**: Acceptance criteria
</step>

<step name="confirm_breakdown">
Present the task breakdown inline:

"Here's the proposed breakdown for Phase [X]:

### Tasks
1. [Task name] - [brief description]
2. [Task name] - [brief description]
3. [Task name] - [brief description]
...

Does this breakdown look right? (yes / adjust / start over)"

Wait for confirmation before proceeding.

If "adjust": Ask what to change, revise, present again.
If "start over": Return to gather_phase_context step.
</step>

<step name="approach_ambiguity">
If multiple valid approaches exist for any task:

Use AskUserQuestion:
- header: "Approach"
- question: "For [task], there are multiple valid approaches:"
- options:
  - "[Approach A]" - [tradeoff description]
  - "[Approach B]" - [tradeoff description]
  - "Decide for me" - Use your best judgment

Only ask if genuinely ambiguous. Don't ask obvious choices.
</step>

<step name="decision_gate">
After breakdown confirmed:

Use AskUserQuestion:
- header: "Ready"
- question: "Ready to create the phase prompt, or would you like me to ask more questions?"
- options:
  - "Create phase prompt" - I have enough context
  - "Ask more questions" - There are details to clarify
  - "Let me add context" - I want to provide more information

Loop until "Create phase prompt" selected.
</step>

<step name="write_phase_prompt">
Use template from `templates/phase-prompt.md`.

Write to `.planning/phases/XX-name/PLAN.md`:

```markdown
---
phase: XX-name
type: execute
domain: [if domain expertise loaded]
---

<objective>
[Phase goal from roadmap]

Purpose: [Why this matters]
Output: [What will be created]
</objective>

<context>
@.planning/BRIEF.md
@.planning/ROADMAP.md
[If research done:]
@.planning/phases/XX-name/FINDINGS.md
[Relevant source files:]
@src/path/to/relevant.ts
</context>

<tasks>
[Tasks with Files/Action/Verify/Done]
</tasks>

<verification>
[Overall phase verification checks]
</verification>

<success_criteria>
[Measurable completion criteria]
</success_criteria>

<output>
After completion, create `.planning/phases/XX-name/SUMMARY.md`
[Include summary structure from template]
</output>
```
</step>

<step name="offer_next">
```
Phase prompt created: .planning/phases/XX-name/PLAN.md
[X] tasks defined.

What's next?
1. Execute phase
2. Review/adjust tasks
3. Done for now
```
</step>

</process>

<task_quality>
Good tasks:
- "Add User model to Prisma schema with email, passwordHash, createdAt"
- "Create POST /api/auth/login endpoint with bcrypt validation"
- "Add protected route middleware checking JWT in cookies"

Bad tasks:
- "Set up authentication" (too vague)
- "Make it secure" (not actionable)
- "Handle edge cases" (which ones?)

If you can't specify Files + Action + Verify + Done, the task is too vague.
</task_quality>

<anti_patterns>
- Don't add story points
- Don't estimate hours
- Don't assign to team members
- Don't add acceptance criteria committees
- Don't create sub-sub-sub tasks

Tasks are instructions for Claude, not Jira tickets.
</anti_patterns>

<success_criteria>
Phase prompt is complete when:
- [ ] PLAN.md exists with XML structure
- [ ] Objective, context, tasks, verification, success criteria, output all present
- [ ] @context references included
- [ ] 5-10 tasks defined
- [ ] Each task has: Files, Action, Verify, Done
- [ ] Tasks are specific enough for Claude to execute
- [ ] User knows next steps
</success_criteria>
