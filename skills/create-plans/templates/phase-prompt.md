# Phase Prompt Template

Copy and fill this structure for `.planning/phases/XX-name/PLAN.md`:

```markdown
---
phase: XX-name
type: execute
domain: [optional - if domain skill loaded]
---

<objective>
[What this phase accomplishes - from roadmap phase goal]

Purpose: [Why this matters for the project]
Output: [What artifacts will be created]
</objective>

<context>
@.planning/BRIEF.md
@.planning/ROADMAP.md
[If research exists:]
@.planning/phases/XX-name/FINDINGS.md
[Relevant source files:]
@src/path/to/relevant.ts
</context>

<tasks>

### Task 1: [Action-oriented name]

**Files**: `path/to/file.ext`, `another/file.ext`
**Action**: [Specific implementation - what to do, how to do it, what to avoid and WHY]
**Verify**: [Command or check to prove it worked]
**Done**: [Measurable acceptance criteria]

### Task 2: [Action-oriented name]

**Files**: `path/to/file.ext`
**Action**: [Specific implementation]
**Verify**: [Command or check]
**Done**: [Acceptance criteria]

[Continue for all tasks...]

</tasks>

<verification>
Before declaring phase complete:
- [ ] [Specific test command]
- [ ] [Build/type check passes]
- [ ] [Behavior verification]
</verification>

<success_criteria>
- All tasks completed
- All verification checks pass
- No errors or warnings introduced
- [Phase-specific criteria]
</success_criteria>

<output>
After completion, create `.planning/phases/XX-name/SUMMARY.md`:

# Phase [X]: [Name] Summary

**[Substantive one-liner - what shipped, not "phase complete"]**

## Accomplishments
- [Key outcome 1]
- [Key outcome 2]

## Files Created/Modified
- `path/to/file.ts` - Description
- `path/to/another.ts` - Description

## Decisions Made
[Key decisions and rationale, or "None"]

## Issues Encountered
[Problems and resolutions, or "None"]

## Next Phase Readiness
[What's ready for next phase, any blockers]
</output>
```

<key_elements>
From create-meta-prompts patterns:
- XML structure for Claude parsing
- @context references for file loading
- Action includes "what to avoid and WHY" (from intelligence-rules)
- Verification is specific and executable
- Success criteria is measurable
- Output specification includes SUMMARY.md structure
</key_elements>

<good_examples>
```markdown
---
phase: 01-foundation
type: execute
domain: next-js
---

<objective>
Set up Next.js project with authentication foundation.

Purpose: Establish the core structure and auth patterns all features depend on.
Output: Working Next.js app with JWT auth, protected routes, and user model.
</objective>

<context>
@.planning/BRIEF.md
@.planning/ROADMAP.md
@src/lib/db.ts
</context>

<tasks>

### Task 1: Add User model to database schema

**Files**: `prisma/schema.prisma`
**Action**: Add User model with fields: id (cuid), email (unique), passwordHash, createdAt, updatedAt. Add Session relation. Use @db.VarChar(255) for email to prevent index issues.
**Verify**: `npx prisma validate` passes, `npx prisma generate` succeeds
**Done**: Schema valid, types generated, no errors

### Task 2: Create login API endpoint

**Files**: `src/app/api/auth/login/route.ts`
**Action**: POST endpoint that accepts {email, password}, validates against User table using bcrypt, returns JWT in httpOnly cookie with 15-min expiry. Use jose library for JWT (not jsonwebtoken - it has CommonJS issues with Next.js).
**Verify**: `curl -X POST /api/auth/login -d '{"email":"test@test.com","password":"test"}' -H "Content-Type: application/json"` returns 200 with Set-Cookie header
**Done**: Valid credentials return 200 + cookie, invalid return 401, missing fields return 400

</tasks>

<verification>
Before declaring phase complete:
- [ ] `npm run build` succeeds without errors
- [ ] `npx prisma validate` passes
- [ ] Login endpoint responds correctly to valid/invalid credentials
- [ ] Protected route redirects unauthenticated users
</verification>

<success_criteria>
- All tasks completed
- All verification checks pass
- No TypeScript errors
- JWT auth flow works end-to-end
</success_criteria>

<output>
After completion, create `.planning/phases/01-foundation/SUMMARY.md`
</output>
```
</good_examples>

<bad_examples>
```markdown
# Phase 1: Foundation

## Tasks

### Task 1: Set up authentication
**Action**: Add auth to the app
**Done when**: Users can log in
```

This is useless. No XML structure, no @context, no verification, no specificity.
</bad_examples>
