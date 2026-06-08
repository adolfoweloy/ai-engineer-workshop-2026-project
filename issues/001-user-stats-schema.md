## Parent PRD

`issues/prd.md`

## What to build

Add the `userStats` table to the Drizzle schema and generate the corresponding migration. This is the persistence layer for all gamification data — one row per user storing total points, streak counters, and last activity date.

See the **Schema Changes** section of the parent PRD for the exact column definitions.

## Acceptance criteria

- [ ] `userStats` table added to `app/db/schema.ts` with columns: `id`, `userId` (FK to users), `totalPoints` (integer, default 0), `currentStreak` (integer, default 0), `longestStreak` (integer, default 0), `lastActivityDate` (text, nullable)
- [ ] New Drizzle migration SQL file generated under `drizzle/` and committed
- [ ] TypeScript compiles without errors after the schema change

## Blocked by

None — can start immediately.

## User stories addressed

Foundation for all user stories — no user-facing behaviour is delivered by this slice alone.
