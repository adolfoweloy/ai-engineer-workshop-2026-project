## Parent PRD

`issues/prd.md`

## What to build

Implement the `getLevelForPoints` pure function inside a new `app/services/gamificationService.ts` file, using TDD. This function maps a point total to a level number and name using the five hardcoded thresholds defined in the PRD. It has no database dependency.

See the **Level Thresholds** and **Level resolution** sections of the parent PRD.

## Approach: test first

Write all tests in `app/services/gamificationService.test.ts` before implementing the function. Follow the same test setup pattern as `progressService.test.ts` (use `vi.mock("~/db")`, `createTestDb`, `seedBaseData`). For this pure function the test db is not needed, but the file structure should be consistent with future slices.

Red → Green → Refactor for each boundary value.

## Acceptance criteria

- [ ] `gamificationService.test.ts` exists with failing tests for all boundary values before implementation starts
- [ ] Tests cover: 0 pts → Beginner, 99 pts → Beginner, 100 pts → Learner, 299 pts → Learner, 300 pts → Practitioner, 749 pts → Practitioner, 750 pts → Expert, 1499 pts → Expert, 1500 pts → Master, 2000 pts → Master
- [ ] `getLevelForPoints(points)` returns `{ level: number, name: string }` 
- [ ] Level thresholds are defined as named constants (not magic numbers)
- [ ] All tests pass

## Blocked by

- `issues/001-user-stats-schema.md`

## User stories addressed

- User story 6 (see current level name)
- User story 7 (see points needed for next level)
