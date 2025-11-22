# create-plans

**Hierarchical project planning optimized for solo developer + Claude**

Create executable plans that Claude can run, not enterprise documentation that sits unused.

## Philosophy

**You are the visionary. Claude is the builder.**

No teams. No stakeholders. No ceremonies. No coordination overhead.

Plans are written AS prompts (PLAN.md IS the execution prompt), not documentation that gets transformed into prompts later.

## Quick Start

```
Skill("create-plans")
```

The skill will:
1. Scan for existing planning structure
2. Check for git repo (offers to initialize)
3. Present context-aware options
4. Guide you through the appropriate workflow

## Planning Hierarchy

```
BRIEF.md          → Human vision (what and why)
    ↓
ROADMAP.md        → Phase structure (high-level plan)
    ↓
RESEARCH.md       → Research prompt (for unknowns - optional)
    ↓
FINDINGS.md       → Research output (if research done)
    ↓
PLAN.md           → THE PROMPT (Claude executes this)
    ↓
SUMMARY.md        → Outcome (existence = phase complete)
```

## Directory Structure

All planning artifacts go in `.planning/`:

```
.planning/
├── BRIEF.md                    # Project vision
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

## Workflows

### Starting a New Project

1. Invoke skill
2. Choose "Start new project"
3. Answer questions about vision/goals
4. Skill creates BRIEF.md
5. Optionally create ROADMAP.md with phases
6. Plan first phase

### Planning a Phase

1. Skill reads BRIEF + ROADMAP
2. Loads domain expertise if applicable (see Domain Skills below)
3. If phase has unknowns → create RESEARCH.md first
4. Creates PLAN.md (the executable prompt)
5. You review or execute

### Executing a Phase

1. Skill reads PLAN.md
2. Executes each task with verification
3. Creates SUMMARY.md when complete
4. Git commits phase completion
5. Offers to plan next phase

### Pausing Work (Handoff)

1. Choose "Create handoff"
2. Skill creates `.continue-here.md` with full context
3. When resuming, skill loads handoff and continues

## Domain Skills (Optional)

Domain expertise provides framework-specific patterns, conventions, and commands.

The skill checks two locations:
- `~/.claude/skills/build/[domain]` (global)
- `./build/[domain]` (local to repo)

Example domains:
- `macos-apps` - Swift/SwiftUI macOS development
- `iphone-apps` - Swift/SwiftUI iOS development
- `example-nextjs` - Next.js web apps (included as example)

When planning with domain expertise, the skill loads reference files before creating PLAN.md, ensuring tasks are framework-appropriate.

### Creating a Domain Skill

See `build/example-nextjs/` for pattern. Create:

```
build/[your-domain]/
└── references/
    └── conventions.md     # Framework patterns, structure, commands
```

## Quality Controls

Research prompts include systematic verification to prevent gaps:

- **Verification checklists** - Enumerate ALL options before researching
- **Blind spots review** - "What might I have missed?"
- **Critical claims audit** - Verify "X is not possible" with sources
- **Quality reports** - Distinguish verified facts from assumptions
- **Streaming writes** - Write incrementally to prevent token limit failures

See `references/research-pitfalls.md` for known mistakes and prevention.

## Key Principles

### Solo Developer + Claude
Planning for ONE person (you) and ONE implementer (Claude). No team coordination, stakeholder management, or enterprise processes.

### Plans Are Prompts
PLAN.md IS the execution prompt. It contains objective, context (@file references), tasks (Files/Action/Verify/Done), and verification steps.

### Ship Fast, Iterate Fast
Plan → Execute → Ship → Learn → Repeat. No multi-week timelines, approval gates, or sprint ceremonies.

### Context Awareness
Monitors token usage:
- **25% remaining**: Mentions context getting full
- **15% remaining**: Pauses, offers handoff
- **10% remaining**: Auto-creates handoff, stops

Never starts large operations below 15% without confirmation.

### User Gates
Pauses at critical decision points:
- Before writing PLAN.md (confirm breakdown)
- After low-confidence research
- On verification failures
- When previous phase had issues

See `references/user-gates.md` for full gate patterns.

### Git Versioning
All planning artifacts are version controlled. Commits outcomes, not process:
- Initialization commit (BRIEF + ROADMAP)
- Phase completion commits (PLAN + SUMMARY + code)
- Handoff commits (when pausing work)

Git log becomes project history.

## Anti-Patterns

This skill NEVER includes:
- Team structures, roles, RACI matrices
- Stakeholder management, alignment meetings
- Sprint ceremonies, standups, retros
- Multi-week estimates, resource allocation
- Change management, governance processes
- Documentation for documentation's sake

If it sounds like corporate PM theater, it doesn't belong.

## Files Reference

### Structure
- `references/directory-structure.md` - Planning directory layout
- `references/hierarchy-rules.md` - How levels build on each other

### Formats
- `references/plan-format.md` - PLAN.md structure
- `references/handoff-format.md` - Context handoff structure

### Patterns
- `references/context-scanning.md` - How skill understands current state
- `references/context-management.md` - Token usage monitoring
- `references/user-gates.md` - When to pause and ask
- `references/git-integration.md` - Version control patterns
- `references/research-pitfalls.md` - Known research mistakes

### Templates
- `templates/brief.md` - Project vision document
- `templates/roadmap.md` - Phase structure
- `templates/phase-prompt.md` - Executable phase prompt (PLAN.md)
- `templates/research-prompt.md` - Research prompt (RESEARCH.md)
- `templates/summary.md` - Phase outcome (SUMMARY.md)
- `templates/continue-here.md` - Context handoff

### Workflows
- `workflows/create-brief.md` - Create project vision
- `workflows/create-roadmap.md` - Define phases from brief
- `workflows/plan-phase.md` - Create executable phase prompt
- `workflows/execute-phase.md` - Run phase, create summary
- `workflows/research-phase.md` - Create and run research
- `workflows/plan-chunk.md` - Plan immediate next tasks
- `workflows/transition.md` - Mark phase complete, advance
- `workflows/handoff.md` - Create context handoff for pausing
- `workflows/resume.md` - Load handoff, restore context
- `workflows/get-guidance.md` - Help decide planning approach

## Example Domain Skill

See `build/example-nextjs/` for a minimal domain skill showing:
- Framework-specific patterns
- Project structure conventions
- Common commands
- Phase breakdown strategies
- Task specificity guidelines

Use this as a template for creating your own domain skills.

## Success Criteria

Planning skill succeeds when:
- Context scan runs before intake
- Appropriate workflow selected based on state
- PLAN.md IS the executable prompt (not separate doc)
- Hierarchy is maintained (brief → roadmap → phase)
- Handoffs preserve full context for resumption
- Context limits respected (auto-handoff at 10%)
- Quality controls prevent research gaps
- Streaming writes prevent token limit failures
