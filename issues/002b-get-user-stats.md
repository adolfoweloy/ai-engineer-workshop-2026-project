## Parent PRD

`issues/prd.md`

## What to build

Implement `getUserStats` in `app/services/gamificationService.ts`, using TDD. This function returns a user's current gamification state and handles lazy backfill: when called for a user with no `user_stats` row, it computes retroactive points from existing `lessonProgress` and `quizAttempts`, inserts the row, then returns it.

See the **Get user stats** and **Lazy backfill** sections of the parent PRD for exact behaviour.

## Approach: test first

Add tests to `app/services/gamificationService.test.ts` before implementing. Tests must use a real in-memory SQLite test db (`createTestDb`, `seedBaseData`) — no mocks for DB queries.

Red → Green → Refactor for each scenario.

## Acceptance criteria

- [ ] Failing tests written first for all scenarios below
- [ ] `getUserStats(userId)` returns: `totalPoints`, `currentStreak`, `longestStreak`, `levelName`, `pointsToNextLevel`, `lastActivityDate`
- [ ] User with no `user_stats` row: backfills 10 pts per completed lesson and 25 pts per quiz with at least one passing attempt; inserts the row; returns computed values
- [ ] Backfilled row has `currentStreak = 0` and `longestStreak = 0`
- [ ] User with an existing `user_stats` row: returns it directly without recomputing
- [ ] `pointsToNextLevel` is 0 when the user is at the Master level
- [ ] All tests pass

## Blocked by

- `issues/002a-get-level-for-points.md`

## User stories addressed

- User story 4 (retroactive points for prior work)
- User story 5 (see total points)
- User story 6 (see current level name)
- User story 7 (see points to next level)
- User story 11 (see current streak)
- User story 12 (see longest streak)
