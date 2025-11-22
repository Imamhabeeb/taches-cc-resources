<overview>
Claude-executable plans have a specific format that enables Claude to implement without interpretation. This reference defines what makes a plan executable vs. vague.

**Key insight:** PLAN.md IS the executable prompt. It contains everything Claude needs to execute the phase, including objective, context references, tasks, verification, success criteria, and output specification.
</overview>

<core_principle>
A plan is Claude-executable when Claude can read the PLAN.md and immediately start implementing without asking clarifying questions.

If Claude has to guess, interpret, or make assumptions - the task is too vague.
</core_principle>

<prompt_structure>
Every PLAN.md follows this XML structure:

```markdown
---
phase: XX-name
type: execute
domain: [optional]
---

<objective>
[What and why]
Purpose: [...]
Output: [...]
</objective>

<context>
@.planning/BRIEF.md
@.planning/ROADMAP.md
@relevant/source/files.ts
</context>

<tasks>
### Task N: [Name]
**Files**: [paths]
**Action**: [what to do, what to avoid and WHY]
**Verify**: [command/check]
**Done**: [criteria]
</tasks>

<verification>
[Overall phase checks]
</verification>

<success_criteria>
[Measurable completion]
</success_criteria>

<output>
[SUMMARY.md specification]
</output>
```
</prompt_structure>

<task_anatomy>
Every task has four required fields:

<field name="files">
**What it is**: Exact file paths that will be created or modified.

**Good**: `src/app/api/auth/login/route.ts`, `prisma/schema.prisma`
**Bad**: "the auth files", "relevant components"

Be specific. If you don't know the file path, figure it out first.
</field>

<field name="action">
**What it is**: Specific implementation instructions, including what to avoid and WHY.

**Good**: "Create POST endpoint that accepts {email, password}, validates using bcrypt against User table, returns JWT in httpOnly cookie with 15-min expiry. Use jose library (not jsonwebtoken - CommonJS issues with Next.js Edge runtime)."

**Bad**: "Add authentication", "Make login work"

Include: technology choices, data structures, behavior details, pitfalls to avoid.
</field>

<field name="verify">
**What it is**: How to prove the task is complete.

**Good**:
- `npm test` passes
- `curl -X POST /api/auth/login` returns 200 with Set-Cookie header
- Build completes without errors

**Bad**: "It works", "Looks good", "User can log in"

Must be executable - a command, a test, an observable behavior.
</field>

<field name="done">
**What it is**: Acceptance criteria - the measurable state of completion.

**Good**: "Valid credentials return 200 + JWT cookie, invalid credentials return 401"

**Bad**: "Authentication is complete"

Should be testable without subjective judgment.
</field>
</task_anatomy>

<context_references>
Use @file references to load context for the prompt:

```markdown
<context>
@.planning/BRIEF.md           # Project vision
@.planning/ROADMAP.md         # Phase structure
@.planning/phases/02-auth/FINDINGS.md  # Research results
@src/lib/db.ts                # Existing database setup
@src/types/user.ts            # Existing type definitions
</context>
```

Reference files that Claude needs to understand before implementing.
</context_references>

<verification_section>
Overall phase verification (beyond individual task verification):

```markdown
<verification>
Before declaring phase complete:
- [ ] `npm run build` succeeds without errors
- [ ] `npm test` passes all tests
- [ ] No TypeScript errors
- [ ] Feature works end-to-end manually
</verification>
```
</verification_section>

<success_criteria_section>
Measurable criteria for phase completion:

```markdown
<success_criteria>
- All tasks completed
- All verification checks pass
- No errors or warnings introduced
- JWT auth flow works end-to-end
- Protected routes redirect unauthenticated users
</success_criteria>
```
</success_criteria_section>

<output_section>
Specify the SUMMARY.md structure:

```markdown
<output>
After completion, create `.planning/phases/XX-name/SUMMARY.md`:

# Phase X: Name Summary

**[Substantive one-liner]**

## Accomplishments
## Files Created/Modified
## Decisions Made
## Issues Encountered
## Next Phase Readiness
</output>
```
</output_section>

<specificity_levels>
<too_vague>
```markdown
### Task 1: Add authentication
**Action**: Implement auth
**Done**: Users can authenticate
```

Claude: "How? What type? What library? Where?"
</too_vague>

<just_right>
```markdown
### Task 1: Create login endpoint with JWT

**Files**: `src/app/api/auth/login/route.ts`
**Action**: POST endpoint accepting {email, password}. Query User by email, compare password with bcrypt. On match, create JWT with jose library, set as httpOnly cookie (15-min expiry). Return 200. On mismatch, return 401. Use jose instead of jsonwebtoken (CommonJS issues with Edge).
**Verify**: `curl -X POST localhost:3000/api/auth/login -H "Content-Type: application/json" -d '{"email":"test@test.com","password":"test123"}'` returns 200 with Set-Cookie header containing JWT.
**Done**: Valid credentials → 200 + cookie. Invalid → 401. Missing fields → 400.
```

Claude can implement this immediately.
</just_right>

<too_detailed>
Writing the actual code in the plan. Trust Claude to implement from clear instructions.
</too_detailed>
</specificity_levels>

<anti_patterns>
<vague_actions>
- "Set up the infrastructure"
- "Handle edge cases"
- "Make it production-ready"
- "Add proper error handling"

These require Claude to decide WHAT to do. Specify it.
</vague_actions>

<unverifiable_completion>
- "It works correctly"
- "User experience is good"
- "Code is clean"
- "Tests pass" (which tests? do they exist?)

These require subjective judgment. Make it objective.
</unverifiable_completion>

<missing_context>
- "Use the standard approach"
- "Follow best practices"
- "Like the other endpoints"

Claude doesn't know your standards. Be explicit.
</missing_context>
</anti_patterns>

<sizing_tasks>
Good task size: 15-60 minutes of Claude work.

**Too small**: "Add import statement for bcrypt"
**Just right**: "Create login endpoint with JWT validation"
**Too big**: "Implement full authentication system"

If a task takes multiple sessions, break it down.
If a task is trivial, combine with related tasks.
</sizing_tasks>
