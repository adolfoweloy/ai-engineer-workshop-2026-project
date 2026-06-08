## Parent PRD

`issues/prd.md`

## What to build

Implement `awardLessonPoints` in `app/services/gamificationService.ts`, using TDD. When a lesson is marked complete, this function awards 10 points to the user, updates the streak, and returns a delta object indicating what changed. It calls `getUserStats` first to ensure the `user_stats` row exists (triggering lazy backfill if needed).

See the **Award lesson points** and **Streak update** sections of the parent PRD for exact behaviour. Note that streak resets to 1 (not 0) when the gap is more than one day.

## Approach: test first

Add tests to `app/services/gamificationService.test.ts` before implementing. Use the real in-memory SQLite test db — no mocks for DB queries. Simulate different `lastActivityDate` values directly in the db to test streak scenarios.

Red → Green → Refactor for each scenario.

## Acceptance criteria

- [ ] Failing tests written first for all scenarios below
- [ ] First completion of a lesson awards 10 pts and returns `{ pointsEarned: 10, newTotal, newLevel, levelUp }`
- [ ] Calling again for the same lesson is idempotent: returns `{ pointsEarned: 0 }` and does not increment points or streak
- [ ] Same-day call (activity date unchanged): streak not incremented
- [ ] Next-day call: `currentStreak` incremented by 1
- [ ] Gap of more than one day: `currentStreak` resets to 1
- [ ] `longestStreak` is updated whenever `currentStreak` exceeds it
- [ ] `levelUp: true` is returned when the new point total crosses a level threshold
- [ ] All tests pass

## Blocked by

- `issues/002b-get-user-stats.md`

## User stories addressed

- User story 1 (earn points for completing a lesson)
- User story 10 (maintain a daily streak)
- User story 13 (streak resets after a missed day)
- User story 14 (longest streak preserved after reset)
