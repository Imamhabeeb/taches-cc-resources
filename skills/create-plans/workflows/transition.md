# Workflow: Transition to Next Phase

<required_reading>
**Read these files NOW:**
1. `.planning/ROADMAP.md`
2. Current phase's `PLAN.md`
</required_reading>

<purpose>
Mark current phase complete and advance to next. This is the natural point
where progress tracking happens - implicit via forward motion.

"Planning next phase" = "current phase is done"
</purpose>

<process>

<step name="verify_completion">
Check current phase status:

```bash
cat .planning/phases/XX-current/PLAN.md
```

Ask: "Phase [X] tasks appear complete. Ready to mark it done and move to Phase [X+1]?"

If tasks incomplete, clarify:
- Continue current phase?
- Mark complete anyway (user judgment)?
- Review what's left?
</step>

<step name="cleanup_handoff">
Check for lingering handoff:

```bash
ls .planning/phases/XX-current/.continue-here.md 2>/dev/null
```

If found, delete it - phase is complete, handoff is stale.
</step>

<step name="update_roadmap">
Update `.planning/ROADMAP.md`:
- Mark current phase: `[x] Complete`
- Add completion date
- Keep next phase as `[ ] Not started`

```markdown
## Phases

- [x] Phase 1: Foundation (completed 2025-01-15)
- [ ] Phase 2: Authentication ← Next
- [ ] Phase 3: Core Features
```
</step>

<step name="archive_prompts">
If prompts were generated for the phase, they stay in place.
The `completed/` subfolder pattern from create-meta-prompts handles archival.
</step>

<step name="offer_next_phase">
```
Phase [X] marked complete.

Next: Phase [X+1] - [Name]

What would you like to do?
1. Plan Phase [X+1] in detail
2. Review roadmap
3. Take a break (done for now)
```
</step>

</process>

<implicit_tracking>
Progress tracking is IMPLICIT:

- "Plan phase 2" → Phase 1 must be done (or ask)
- "Plan phase 3" → Phases 1-2 must be done (or ask)
- Transition workflow makes it explicit in ROADMAP.md

No separate "update progress" step. Forward motion IS progress.
</implicit_tracking>

<partial_completion>
If user wants to move on but phase isn't fully complete:

```
Phase [X] has incomplete tasks:
- Task 5: [name]
- Task 6: [name]

Options:
1. Mark complete anyway (tasks weren't needed)
2. Defer tasks to later phase
3. Stay and finish current phase
```

Respect user judgment - they know if tasks matter.
</partial_completion>

<success_criteria>
Transition is complete when:
- [ ] Current phase verified complete (or user decided to move on)
- [ ] Any stale handoff deleted
- [ ] ROADMAP.md updated with completion
- [ ] User knows next steps
</success_criteria>
