## Parent PRD

`issues/prd.md`

## What to build

Modify the `mark-complete` action handler in `app/routes/courses.$slug.lessons.$lessonId.tsx` to call `awardLessonPoints` after `markLessonComplete`, and return the gamification delta in the action response. The UI slices (toast, modal) are handled separately in `issues/006-toast-levelup-modal.md` — this slice is server-side only.

See the **Lesson action handler** section of the parent PRD.

## Acceptance criteria

- [ ] After `markLessonComplete` succeeds, `awardLessonPoints(userId, lessonId)` is called
- [ ] The `mark-complete` action response includes: `pointsEarned`, `newTotal`, `newLevel`, `levelUp`
- [ ] Existing `mark-complete` behaviour (marking the lesson complete) is unchanged
- [ ] TypeScript compiles without errors

## Blocked by

- `issues/002c-award-lesson-points.md`

## User stories addressed

- User story 1 (earn points for completing a lesson)
- User story 8 (see points earned immediately after completion)
