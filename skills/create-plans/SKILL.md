---
name: create-plans
description: Create hierarchical project plans optimized for solo agentic development. Use when planning projects, phases, or tasks that Claude will execute. Produces Claude-executable plans with verification criteria, not enterprise documentation. Handles briefs, roadmaps, phase plans, and context handoffs.
---

<essential_principles>

<principle name="solo_developer_plus_claude">
You are planning for ONE person (the user) and ONE implementer (Claude).
No teams. No stakeholders. No ceremonies. No coordination overhead.
The user is the visionary/product owner. Claude is the builder.
</principle>

<principle name="plans_are_prompts">
PLAN.md is not a document that gets transformed into a prompt.
PLAN.md IS the prompt. It contains:
- Objective (what and why)
- Context (@file references)
- Tasks (Files, Action, Verify, Done)
- Verification (overall checks)
- Success criteria (measurable)
- Output (SUMMARY.md specification)

When planning a phase, you are writing the prompt that will execute it.
</principle>

<principle name="ship_fast_iterate_fast">
No enterprise process. No approval gates. No multi-week timelines.
Plan → Execute → Ship → Learn → Repeat.
</principle>

<principle name="anti_enterprise_patterns">
NEVER include in plans:
- Team structures, roles, RACI matrices
- Stakeholder management, alignment meetings
- Sprint ceremonies, standups, retros
- Multi-week estimates, resource allocation
- Change management, governance processes
- Documentation for documentation's sake

If it sounds like corporate PM theater, delete it.
</principle>

<principle name="context_awareness">
Monitor token usage via system warnings.

**At 25% remaining**: Mention context getting full
**At 15% remaining**: Pause, offer handoff
**At 10% remaining**: Auto-create handoff, stop

Never start large operations below 15% without user confirmation.
</principle>

<principle name="user_gates">
Never charge ahead at critical decision points. Use gates:
- **AskUserQuestion**: Structured choices (2-4 options)
- **Inline questions**: Simple confirmations
- **Decision gate loop**: "Ready, or ask more questions?"

Mandatory gates:
- Before writing PLAN.md (confirm breakdown)
- After low-confidence research
- On verification failures
- After phase completion with issues
- Before starting next phase with previous issues

See: references/user-gates.md
</principle>

<principle name="git_versioning">
All planning artifacts are version controlled. Commit outcomes, not process.

- Check for repo on invocation, offer to initialize
- Commit only at: initialization, phase completion, handoff
- Intermediate artifacts (PLAN.md, RESEARCH.md, FINDINGS.md) NOT committed separately
- Git log becomes project history

See: references/git-integration.md
</principle>

</essential_principles>

<context_scan>
**Run on every invocation** to understand current state:

```bash
# Check git status
git rev-parse --git-dir 2>/dev/null || echo "NO_GIT_REPO"

# Check for planning structure
ls -la .planning/ 2>/dev/null
ls -la .planning/phases/ 2>/dev/null

# Find any continue-here files
find . -name ".continue-here.md" -type f 2>/dev/null

# Check for existing artifacts
[ -f .planning/BRIEF.md ] && echo "BRIEF: exists"
[ -f .planning/ROADMAP.md ] && echo "ROADMAP: exists"
```

**If NO_GIT_REPO detected:**
Inline question: "No git repo found. Initialize one? (Recommended for version control)"
If yes: `git init`

**Present findings before intake question.**
</context_scan>

<domain_expertise>
**Domain expertise can be loaded from `~/.claude/skills/build/` or local `build/` directory**

Before creating roadmap or phase plans, determine if domain expertise should be loaded.

<scan_domains>
```bash
# Check for domain skills in both locations
ls ~/.claude/skills/build/ 2>/dev/null
ls ./build/ 2>/dev/null
```

This reveals available domain expertise (e.g., macos-apps, iphone-apps, unity-games, next-js-apps).

**If no domain skills found:** Proceed without domain expertise (graceful degradation). The skill works fine without domain-specific context.
</scan_domains>

<inference_rules>
If user's request contains domain keywords, INFER the domain:

| Keywords | Domain Skill |
|----------|--------------|
| "macOS", "Mac app", "menu bar", "AppKit", "SwiftUI desktop" | build/macos-apps |
| "iPhone", "iOS", "iPad", "mobile app", "SwiftUI mobile" | build/iphone-apps |
| "Unity", "game", "C#", "3D game", "2D game" | build/unity-games |
| "MIDI", "audio app", "music app", "DAW", "sequencer" | build/swift-midi-apps |
| "Agent SDK", "Claude SDK", "agentic app" | build/with-agent-sdk |

If domain inferred, confirm:
```
Detected: [domain] project → build/[skill-name]
Load this expertise for planning? (Y / see other options / none)
```
</inference_rules>

<no_inference>
If no domain obvious from request, present options:

```
What type of project is this?

Available domain expertise:
1. macos-apps - Native macOS with Swift/SwiftUI
2. iphone-apps - Native iOS with Swift/SwiftUI
3. unity-games - Unity game development
4. swift-midi-apps - MIDI/audio apps
5. with-agent-sdk - Claude Agent SDK apps
[... any others found in build/]

N. None - proceed without domain expertise
C. Create domain skill first

Select:
```
</no_inference>

<load_domain>
When domain selected, READ all references from that skill:

```bash
# Try global location first, then local
if [ -d ~/.claude/skills/build/[domain] ]; then
  cat ~/.claude/skills/build/[domain]/references/*.md
elif [ -d ./build/[domain] ]; then
  cat ./build/[domain]/references/*.md
else
  echo "Domain skill not found in either location"
fi
```

This loads domain patterns, conventions, commands into context BEFORE planning.
Announce: "Loaded [domain] expertise from [location]. Planning with [framework] context."

**If domain skill not found:** Inform user and offer to proceed without domain expertise or create a basic domain template.
</load_domain>

<when_to_load>
Domain expertise should be loaded BEFORE:
- Creating roadmap (phases should be domain-appropriate)
- Planning phases (tasks must be domain-specific)

Domain expertise is NOT needed for:
- Creating brief (vision is domain-agnostic)
- Resuming from handoff (context already established)
- Transition between phases (just updating status)
</when_to_load>
</domain_expertise>

<intake>
Based on scan results, present context-aware options:

**If handoff found:**
```
Found handoff: .planning/phases/XX/.continue-here.md
[Summary of state from handoff]

1. Resume from handoff
2. Discard handoff, start fresh
3. Different action
```

**If planning structure exists:**
```
Project: [from BRIEF or directory]
Brief: [exists/missing]
Roadmap: [X phases defined]
Current: [phase status]

What would you like to do?
1. Plan next phase
2. Execute current phase
3. Create handoff (stopping for now)
4. View/update roadmap
5. Something else
```

**If no planning structure:**
```
No planning structure found.

What would you like to do?
1. Start new project (create brief)
2. Create roadmap from existing brief
3. Jump straight to phase planning
4. Get guidance on approach
```

**Wait for response before proceeding.**
</intake>

<routing>
| Response | Workflow |
|----------|----------|
| "brief", "new project", "start", 1 (no structure) | `workflows/create-brief.md` |
| "roadmap", "phases", 2 (no structure) | `workflows/create-roadmap.md` |
| "phase", "plan phase", "next phase", 1 (has structure) | `workflows/plan-phase.md` |
| "chunk", "next tasks", "what's next" | `workflows/plan-chunk.md` |
| "execute", "run", "do it", "build it", 2 (has structure) | `workflows/execute-phase.md` |
| "research", "investigate", "unknowns" | `workflows/research-phase.md` |
| "handoff", "pack up", "stopping", 3 (has structure) | `workflows/handoff.md` |
| "resume", "continue", 1 (has handoff) | `workflows/resume.md` |
| "transition", "complete", "done", "next" | `workflows/transition.md` |
| "guidance", "help", 4 | `workflows/get-guidance.md` |

**After reading the workflow, follow it exactly.**
</routing>

<hierarchy>
The planning hierarchy (each level builds on previous):

```
BRIEF.md          → Human vision (you read this)
    ↓
ROADMAP.md        → Phase structure (overview)
    ↓
RESEARCH.md       → Research prompt (optional, for unknowns)
    ↓
FINDINGS.md       → Research output (if research done)
    ↓
PLAN.md           → THE PROMPT (Claude executes this)
    ↓
SUMMARY.md        → Outcome (existence = phase complete)
```

**Rules:**
- Roadmap requires Brief (or prompts to create one)
- Phase plan requires Roadmap (knows phase scope)
- PLAN.md IS the execution prompt
- SUMMARY.md existence marks phase complete
- Each level can look UP for context
</hierarchy>

<output_structure>
All planning artifacts go in `.planning/`:

```
.planning/
├── BRIEF.md                    # Human vision
├── ROADMAP.md                  # Phase structure + tracking
└── phases/
    ├── 01-foundation/
    │   ├── PLAN.md             # THE PROMPT (execute this)
    │   ├── SUMMARY.md          # Outcome (exists = done)
    │   └── .continue-here.md   # Handoff (temporary)
    └── 02-auth/
        ├── RESEARCH.md         # Research prompt (if needed)
        ├── FINDINGS.md         # Research output
        ├── PLAN.md             # Execute prompt
        └── SUMMARY.md
```

Phase folders use `XX-name` format for ordering.
</output_structure>

<reference_index>
All in `references/`:

**Structure:** directory-structure.md, hierarchy-rules.md
**Formats:** handoff-format.md, plan-format.md
**Patterns:** context-scanning.md, context-management.md
</reference_index>

<templates_index>
All in `templates/`:

| Template | Purpose |
|----------|---------|
| brief.md | Project vision document |
| roadmap.md | Phase structure with tracking |
| phase-prompt.md | Executable phase prompt (PLAN.md) |
| research-prompt.md | Research prompt (RESEARCH.md) |
| summary.md | Phase outcome (SUMMARY.md) |
| continue-here.md | Context handoff format |
</templates_index>

<workflows_index>
All in `workflows/`:

| Workflow | Purpose |
|----------|---------|
| create-brief.md | Create project vision document |
| create-roadmap.md | Define phases from brief |
| plan-phase.md | Create executable phase prompt |
| execute-phase.md | Run phase prompt, create summary |
| research-phase.md | Create and run research prompt |
| plan-chunk.md | Plan immediate next tasks |
| transition.md | Mark phase complete, advance |
| handoff.md | Create context handoff for pausing |
| resume.md | Load handoff, restore context |
| get-guidance.md | Help decide planning approach |
</workflows_index>

<success_criteria>
Planning skill succeeds when:
- Context scan runs before intake
- Appropriate workflow selected based on state
- PLAN.md IS the executable prompt (not separate)
- Hierarchy is maintained (brief → roadmap → phase)
- Handoffs preserve full context for resumption
- Context limits are respected (auto-handoff at 10%)
</success_criteria>
