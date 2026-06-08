## Parent PRD

`issues/prd.md`

## What to build

Modify the `submit-quiz` action handler in `app/routes/courses.$slug.lessons.$lessonId.tsx` to call `awardQuizPoints` when the result is a pass, and return the gamification delta in the action response. The UI slices (toast, modal) are handled separately in `issues/006-toast-levelup-modal.md` — this slice is server-side only.

See the **Quiz submit action** section of the parent PRD. Use the `passed` boolean from `computeResult` rather than re-evaluating the score.

## Acceptance criteria

- [ ] After `computeResult` returns a passing result, `awardQuizPoints(userId, quizId, passed)` is called
- [ ] The `submit-quiz` action response includes: `pointsEarned`, `newTotal`, `newLevel`, `levelUp`
- [ ] No points are awarded for a failing attempt — existing behaviour unchanged
- [ ] Existing quiz submit behaviour (recording the attempt) is unchanged
- [ ] TypeScript compiles without errors

## Blocked by

- `issues/002d-award-quiz-points.md`

## User stories addressed

- User story 2 (earn bonus for passing a quiz)
- User story 3 (earn bonus even after earlier failed attempts)
- User story 8 (see points earned immediately)
