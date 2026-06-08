## Parent PRD

`issues/prd.md`

## What to build

Implement `awardQuizPoints` in `app/services/gamificationService.ts`, using TDD. When a quiz is passed, this function awards 25 points if the user has not previously passed this quiz (idempotent on repeat passes). It does not award points for failed attempts.

See the **Award quiz points** section of the parent PRD. The function should rely on the `passed` boolean returned by `computeResult` (passed in as a parameter) rather than re-evaluating the score, to stay consistent with the existing passing logic.

## Approach: test first

Add tests to `app/services/gamificationService.test.ts` before implementing. Use the real in-memory SQLite test db — no mocks for DB queries.

Red → Green → Refactor for each scenario.

## Acceptance criteria

- [ ] Failing tests written first for all scenarios below
- [ ] First passing attempt awards 25 pts and returns `{ pointsEarned: 25, newTotal, newLevel, levelUp }`
- [ ] Second passing attempt for the same quiz returns `{ pointsEarned: 0 }` — no double award
- [ ] Failed attempt (`passed: false`) returns `{ pointsEarned: 0 }`
- [ ] `levelUp: true` returned when the award crosses a level threshold
- [ ] All tests pass

## Blocked by

- `issues/002b-get-user-stats.md`

## User stories addressed

- User story 2 (earn bonus for passing a quiz)
- User story 3 (earn bonus even after earlier failed attempts)

## Note

Can be worked in parallel with `issues/002c-award-lesson-points.md` once `issues/002b-get-user-stats.md` is complete.
