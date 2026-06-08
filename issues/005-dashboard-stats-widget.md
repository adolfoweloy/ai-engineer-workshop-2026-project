## Parent PRD

`issues/prd.md`

## What to build

Extend the dashboard to show a gamification stats widget. This requires one loader change (call `getUserStats`) and one new UI component rendering the returned data. There are no leaderboards or comparisons — all stats are private to the individual student.

See the **Dashboard loader** and **Dashboard UI** sections of the parent PRD.

## Acceptance criteria

- [ ] Dashboard loader calls `getUserStats(currentUserId)` and includes the result in the return value
- [ ] Stats widget displays total points (user story 5)
- [ ] Stats widget displays current level name, e.g. "Practitioner" (user story 6)
- [ ] Stats widget displays points needed to reach the next level, e.g. "220 / 300 pts to Practitioner" (user story 7)
- [ ] Stats widget displays current streak in days (user story 11)
- [ ] Stats widget displays longest-ever streak in days (user story 12)
- [ ] No leaderboard or comparison to other students is present anywhere on the page (user story 17)
- [ ] Widget renders correctly when the user has 0 points (new student, no prior activity)
- [ ] Widget renders correctly when the user is at the Master level (no "next level" target)

## Blocked by

- `issues/002b-get-user-stats.md`

## User stories addressed

- User story 5 (total points on dashboard)
- User story 6 (current level name on dashboard)
- User story 7 (points to next level on dashboard)
- User story 11 (current streak on dashboard)
- User story 12 (longest streak on dashboard)
- User story 17 (stats are private, no leaderboards)
