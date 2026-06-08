# PRD: Student Gamification System

## Problem Statement

Students on the Cadence platform sign up, complete a few lessons, then drop off. There is no visible sense of accumulating progress beyond a per-lesson checkbox. Students who complete many lessons have nothing to show for their effort. Quizzes have no incentive attached to them. The result is low retention and disengagement.

## Solution

Introduce a private, per-student gamification layer built on top of the existing lesson and quiz completion infrastructure. Students earn points for completing lessons and passing quizzes. Points accumulate into levels with named ranks (Beginner → Master). A streak system rewards students who study every day. All stats are private — visible only to the individual student — with no leaderboards or competitive elements.

## User Stories

1. As a student, I want to earn points every time I complete a lesson, so that my effort accumulates into a visible number that grows over time.
2. As a student, I want to earn a bonus when I pass a quiz, so that I have a concrete reason to attempt quizzes rather than skipping them.
3. As a student, I want to earn the quiz bonus even if I failed on earlier attempts, so that I am encouraged to retry rather than give up.
4. As a student, I want my points to be credited for lessons and quizzes I already completed before gamification launched, so that my prior work is recognised and I don't start at zero.
5. As a student, I want to see my total points on my dashboard, so that I can gauge my overall effort at a glance.
6. As a student, I want to see my current level (e.g. "Practitioner") on my dashboard, so that I have a named milestone to aim for.
7. As a student, I want to see how many points I need to reach the next level, so that I can plan how much more study is required.
8. As a student, I want to see a brief confirmation when I complete a lesson showing the points I just earned, so that the reward feels immediate and satisfying.
9. As a student, I want to see a celebration modal when I level up, so that reaching a new rank feels like a meaningful milestone.
10. As a student, I want to maintain a daily streak by completing at least one lesson per calendar day, so that I am motivated to study consistently.
11. As a student, I want to see my current streak count on my dashboard, so that I know how many consecutive days I have studied.
12. As a student, I want to see my longest-ever streak on my dashboard, so that I retain a personal best even after a streak breaks.
13. As a student, I want my streak to reset to zero if I miss a day, so that the streak mechanic feels meaningful and worth protecting.
14. As a student, I want my longest streak to be preserved even after a reset, so that I still have something to show for a previous streak run.
15. As a student, I want a toast notification after completing a lesson that shows my points earned and current streak, so that I get instant positive feedback without interrupting the lesson flow.
16. As a student, I want a modal celebration when I reach a new level, so that the moment feels special and worth noticing.
17. As a student, I want my points and levels to be private (no leaderboards), so that I am not compared to or judged by other students.

## Implementation Decisions

### Schema Changes

- Add a new `user_stats` table with one row per user containing: `userId` (FK to users), `totalPoints` (integer), `currentStreak` (integer), `longestStreak` (integer), `lastActivityDate` (text, ISO date string, UTC).
- No other schema changes are required — existing `lessonProgress` and `quizAttempts` tables already capture the events needed.

### New Module: Gamification Service

A new `gamificationService` encapsulates all gamification logic behind a stable interface. It is the single source of truth for point values, level thresholds, and streak logic. Route handlers call it directly — no existing service is modified.

Key responsibilities:
- **Award lesson points**: When a lesson is marked complete, award 10 points to the user. Update streak if the activity date has changed. Return the delta and whether a level-up occurred.
- **Award quiz points**: When a quiz is submitted and passed for the first time by this user, award 25 points. Return the delta and whether a level-up occurred. If the user has previously passed this quiz, no points are awarded (idempotent).
- **Get user stats**: Return the current `totalPoints`, `currentStreak`, `longestStreak`, computed level name, points to next level, and `lastActivityDate` for a user.
- **Lazy backfill**: When stats are fetched for a user who has no `user_stats` row yet, backfill points from existing `lessonProgress` (10 pts per completed lesson) and `quizAttempts` (25 pts per quiz where the user has at least one passing attempt). Set `currentStreak = 0` and `longestStreak = 0` — no retroactive streak reconstruction. Insert the computed row, then return it.
- **Level resolution**: A pure function maps total points to a level number and name. Thresholds are hardcoded constants: Beginner (0 pts), Learner (100 pts), Practitioner (300 pts), Expert (750 pts), Master (1500 pts).
- **Streak update**: Compares today's UTC date to `lastActivityDate`. If they are the same, no streak change (idempotent). If yesterday, increment `currentStreak` and update `longestStreak` if exceeded. If more than one day ago, reset `currentStreak` to 1. Always update `lastActivityDate` to today.

### Modified Route Handlers

- **Lesson action handler** (`courses.$slug.lessons.$lessonId` action): After calling `markLessonComplete`, also call `awardLessonPoints`. Return the points earned, new total, new level, and whether a level-up occurred in the action response so the client can display the appropriate feedback.
- **Quiz submit action** (same route, quiz-submit intent): After calling `computeResult`, if the result is a pass, call `awardQuizPoints`. Return the same gamification delta fields.
- **Dashboard loader**: Call `getUserStats` for the current user and include the stats in the loader return value.

### UI Changes

- **Dashboard**: Add a stats widget displaying total points, current level name, current streak (days), and longest streak (days). Show progress toward the next level (e.g. "220 / 300 pts to Practitioner").
- **Lesson completion toast**: On successful `mark-complete` action response, display a toast (via the existing `sonner` toast library already imported in the lesson route) showing "+10 pts" and the current streak if ≥ 2 days.
- **Quiz pass toast**: Same toast pattern, showing "+25 pts".
- **Level-up modal**: If the action response indicates a level-up, display a modal (not a toast) celebrating the new level name. The modal replaces the toast for that action.

### Point Values (hardcoded constants)

- Lesson completion: **10 points**
- Quiz first pass: **25 points**

### Level Thresholds (hardcoded constants)

| Level | Name | Points Required |
|-------|------|----------------|
| 1 | Beginner | 0 |
| 2 | Learner | 100 |
| 3 | Practitioner | 300 |
| 4 | Expert | 750 |
| 5 | Master | 1500 |

### Streak Rules

- Timezone: UTC for all users.
- One lesson completed on a given UTC calendar date counts as activity for that date.
- Streak increments when today's date differs from `lastActivityDate` by exactly one day.
- Streak resets to 1 (not 0) when the gap is more than one day — the current action is the start of a new streak.
- `longestStreak` is updated whenever `currentStreak` exceeds it.

## Testing Decisions

**What makes a good test**: Tests should exercise the public interface of a module and assert on observable outputs (return values, database state). They should not assert on internal implementation details such as which internal helper functions were called. Tests should use a real in-memory SQLite test database (following the pattern established in `progressService.test.ts` using `createTestDb` and `seedBaseData`) rather than mocks, to avoid the class of divergence bugs that mocks enable.

**Modules to test**:

- **`gamificationService`** — this is the most important module to test. Cover:
  - `awardLessonPoints`: first completion awards 10 pts; calling again for the same lesson is idempotent (no double points); streak increments on consecutive days; streak resets after a gap; `longestStreak` is updated correctly.
  - `awardQuizPoints`: awards 25 pts on first pass; does not award if user has already passed; does not award if the attempt did not pass.
  - `getUserStats` / lazy backfill: user with existing completions and no `user_stats` row gets correct retroactive points; streak starts at 0 after backfill.
  - `getLevelForPoints`: pure function, test all boundary values (0, 99, 100, 299, 300, 749, 750, 1499, 1500+).

- **Route action integration**: Not required for v1; the service-level tests are sufficient.

**Prior art**: `progressService.test.ts` is the canonical example — use the same `vi.mock("~/db")` pattern, `createTestDb`, and `seedBaseData` helper.

## Out of Scope

- Leaderboards or any competitive/social comparison between students.
- Per-user timezone configuration for streak calculation.
- Admin UI for editing level thresholds or point values.
- Retroactive streak reconstruction from historical lesson timestamps.
- Badges or achievements beyond the five named levels.
- Notifications outside the app (email, push) for streaks or level-ups.
- Points for activities other than lesson completion and quiz passing (e.g. video watch time, forum posts).
- Streak freeze or grace-period mechanics.
- Grace period for missed streak days.

## Further Notes

- The `sonner` toast library is already imported in `courses.$slug.lessons.$lessonId.tsx` — no new dependency is needed for toast notifications.
- The `user_stats` row is created lazily on first stats read, not via a migration script that runs on all existing users. This keeps the migration simple and avoids a potentially slow one-time data operation at deploy time.
- Quiz passing threshold in `quizScoringService` uses `score > 0.7` (exclusive). The gamification service should rely on the `passed` boolean returned by `computeResult` rather than re-evaluating the score, to stay consistent with the existing passing logic.
- Level-up detection: compare the level derived from `totalPoints` before and after awarding points. If the level number increased, signal a level-up in the return value.
