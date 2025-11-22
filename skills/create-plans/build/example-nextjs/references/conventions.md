# Next.js Project Conventions

This is an example domain skill showing how to provide framework-specific guidance to the create-plans skill.

## Project Structure

```
src/
├── app/                  # App Router pages
│   ├── api/             # API routes
│   ├── (auth)/          # Route groups
│   └── layout.tsx       # Root layout
├── components/           # React components
├── lib/                  # Utilities
└── types/               # TypeScript types
```

## Patterns

### API Routes
- Use Route Handlers in `app/api/`
- Export named functions: GET, POST, PUT, DELETE
- Return NextResponse with proper status codes

### Server Components
- Default to Server Components
- Use 'use client' only when needed (interactivity, hooks)
- Fetch data directly in components (no separate data layer)

### Database
- Use Prisma for type-safe database access
- Put schema in `prisma/schema.prisma`
- Run migrations with `npx prisma migrate dev`

### Authentication
- Use jose library for JWT (not jsonwebtoken - ESM issues)
- Store tokens in httpOnly cookies
- Protect routes with middleware in `middleware.ts`

## Common Commands

```bash
# Development
npm run dev

# Build
npm run build

# Type check
npx tsc --noEmit

# Database
npx prisma generate
npx prisma migrate dev
npx prisma studio
```

## Phase Breakdown Pattern

For Next.js projects, typical phases:

1. **Foundation** - Project setup, database, auth foundation
2. **Core Features** - Main application features
3. **Polish** - UI refinement, error handling, testing
4. **Deploy** - Production config, deployment

## Task Specificity

When writing PLAN.md tasks for Next.js:
- Specify exact file paths
- Include imports to use
- Mention App Router patterns (not Pages Router)
- Note Server vs Client Component distinction
- Include verification commands (npm run build, etc.)
