## Parent PRD

`issues/prd.md`

## What to build

Add in-lesson feedback UI that reacts to the gamification delta returned by the lesson and quiz action handlers. Two patterns are needed: a toast for normal point awards, and a modal that replaces the toast when a level-up occurs. The `sonner` toast library is already imported in the lesson route — no new dependency needed.

See the **Lesson completion toast**, **Quiz pass toast**, and **Level-up modal** sections of the parent PRD.

## Acceptance criteria

- [ ] After a successful `mark-complete` action, a toast displays "+10 pts" and the current streak if ≥ 2 days
- [ ] After a successful `submit-quiz` action on a pass, a toast displays "+25 pts" and the current streak if ≥ 2 days
- [ ] When either action returns `levelUp: true`, a modal is shown instead of a toast, celebrating the new level name
- [ ] The modal can be dismissed by the user
- [ ] No toast is shown when `pointsEarned` is 0 (repeat completion, failed quiz)
- [ ] Existing lesson completion and quiz submission UI behaviour is unchanged

## Blocked by

- `issues/003-lesson-action-points.md`
- `issues/004-quiz-action-points.md`

## User stories addressed

- User story 8 (brief confirmation showing points earned after lesson completion)
- User story 9 (celebration modal on level-up)
- User story 15 (toast showing points and streak after lesson completion)
- User story 16 (modal celebration when reaching a new level)
