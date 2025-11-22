# Workflow: Execute Phase

<purpose>
Execute a phase prompt (PLAN.md) and create the outcome summary (SUMMARY.md).
</purpose>

<process>

<step name="identify_phase">
Find the current phase to execute:
- Check ROADMAP.md for "In progress" phase
- Or find phase with PLAN.md but no SUMMARY.md
- Confirm with user if ambiguous

```bash
cat .planning/ROADMAP.md
ls .planning/phases/*/PLAN.md 2>/dev/null
ls .planning/phases/*/SUMMARY.md 2>/dev/null
```
</step>

<step name="load_prompt">
Read the phase prompt:
```bash
cat .planning/phases/XX-name/PLAN.md
```

This IS the execution instructions. Follow it exactly.
</step>

<step name="previous_phase_check">
Before executing, check if previous phase had issues:

```bash
# Find previous phase summary
ls .planning/phases/*/SUMMARY.md 2>/dev/null | sort -r | head -2 | tail -1
```

If previous phase SUMMARY.md has "Issues Encountered" != "None" or "Next Phase Readiness" mentions blockers:

Use AskUserQuestion:
- header: "Previous Issues"
- question: "Previous phase had unresolved items: [summary]. How to proceed?"
- options:
  - "Proceed anyway" - Issues won't block this phase
  - "Address first" - Let's resolve before continuing
  - "Review previous" - Show me the full summary
</step>

<step name="execute">
Execute each task in the prompt:
1. Read the @context files listed in the prompt
2. For each task:
   - Work on the files specified
   - Perform the action described
   - Run the verification
   - Confirm done criteria met
3. Run overall verification checks from `<verification>` section
4. Confirm all success criteria from `<success_criteria>` section met
</step>

<step name="verification_failure_gate">
If any task verification fails:

STOP. Do not continue to next task.

Present inline:
"Verification failed for Task [X]: [task name]

Expected: [verification criteria]
Actual: [what happened]

How to proceed?
1. Retry - Try the task again
2. Skip - Mark as incomplete, continue
3. Stop - Pause execution, investigate"

Wait for user decision.

If user chose "Skip", note it in SUMMARY.md under "Issues Encountered".
</step>

<step name="create_summary">
Create SUMMARY.md as specified in the prompt's `<output>` section.
Use templates/summary.md for structure.

The one-liner must be SUBSTANTIVE:
- Good: "JWT auth with refresh rotation using jose library"
- Bad: "Authentication implemented"
</step>

<step name="issues_review_gate">
Before proceeding, check SUMMARY.md content:

If "Issues Encountered" is NOT "None":
  Present inline:
  "Phase complete, but issues were encountered:
  - [Issue 1]
  - [Issue 2]

  Please review before proceeding. Acknowledged?"

  Wait for acknowledgment.

If "Next Phase Readiness" mentions blockers or concerns:
  Present inline:
  "Note for next phase:
  [concerns from Next Phase Readiness]

  Acknowledged?"

  Wait for acknowledgment.
</step>

<step name="update_roadmap">
Mark phase complete in ROADMAP.md:
- Change status to "Complete"
- Add completion date
</step>

<step name="git_commit_phase">
Commit phase completion (PLAN.md + SUMMARY.md + code created):

```bash
git add .planning/phases/XX-name/
git add src/  # or relevant code directories
git commit -m "$(cat <<'EOF'
feat([domain]): [one-liner from SUMMARY.md]

- [Key accomplishment 1]
- [Key accomplishment 2]
- [Key accomplishment 3]
EOF
)"
```

Confirm: "Committed: feat([domain]): [what shipped]"
</step>

<step name="offer_next">
```
Phase XX-name complete.
Summary: .planning/phases/XX-name/SUMMARY.md

What's next?
1. Begin next phase
2. Review what was built
3. Done for now
```
</step>

</process>

<success_criteria>
- All tasks from PLAN.md completed
- All verifications pass
- SUMMARY.md created with substantive content
- ROADMAP.md updated
</success_criteria>
