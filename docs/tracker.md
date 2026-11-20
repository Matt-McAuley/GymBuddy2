# Tracker

The new tab for looking back: every workout, streaks, PRs, and graphs of each lift, plus the summary shown after a workout. Strava is the model for the kind of features, without the social side.

**Status:** behavior, layout (pass 1, grey boxes), and visual style (pass 2, Liquid Glass) settled, not built. See "Where we left off".

## What Matt is trying to do

- Check what he did last time (last Push: weights and reps)
- See progress: a lift going up over weeks and months
- Check and celebrate PRs
- Stay consistent: streaks, workouts this week, and a calendar

## Already decided elsewhere

From `docs/data.md`:

- **Tracker only reads history.** Nothing done there changes the plan ("What each page needs").
- **A calendar and a log of every workout,** including which day and scheme were done.
- **PRs are per exercise and rep count,** with filters by program ("Records and Tracker stats").
- **Each set saves planned and actual numbers.** The rep numbers are jump goals, so hitting them all is a rare PR moment, and falling short is normal (`CLAUDE.md` "Goals"). **Tracker never shows a set below its number as a miss or a failure.** No hit rates, and no ticks vs warnings.
- **Stats are calculated from the raw sets,** so new ones can be added later and still cover old workouts.

## Decided

### Streaks

- **A streak counts weeks with at least one workout.** Lifting isn't daily, so a day-by-day streak would break on rest days. Weeks use the program's week reset day (`docs/data.md` "Rotation").
- **Shown as the number only** ("7-week streak"), with no icon for now.

**Rejected:** weeks with every day done (too strict) and a weekly goal number (an extra setting to explain).

### Workout summary

- **Shown on Home in place of the exercise once the workout is finished.** This grows Home's existing "Push done" screen (`docs/home.md` "Day and rotation"). The same summary opens from a workout in Tracker's log, with every set listed below it.
- **Finishing happens on its own when every set is done.** If any sets are left, whether they were passed over or the workout was just cut short, a **"Finish workout"** button appears in Home's day list. It asks "Finish without DB Curl? 3 sets left", then shows the summary. This is the one exception to "no Finish button" in `docs/data.md`.
- **A workout finished with sets left shows "Resume workout" on its summary.** Tapping it reopens the same workout and hides the summary until the workout finishes again.
- **Shows:** time, a count of sets logged ("25 sets", with no "out of"), and PRs hit.
- **Each exercise row shows what was lifted, plus its jump if one was earned:** "BB Row · 195 × 5, 5, 5, 5, 7 · +2.5". Nothing is marked as hit or missed, since falling short of the rep numbers is normal. A tick means the exercise was done. An exercise left undone when finishing early shows "Not done".
- **"Start next workout"** moves on by hand (`docs/home.md` "Day and rotation").

**Rejected:** a tick vs "!" for each exercise, and "Set 4: 7 of 10" lines, which treated falling short as failing. Also "25 / 26 sets", and "compared to last time" (Bench +5 lb). Last time this day was done was usually a different scheme, so the numbers would show false drops. Also rejected: showing the summary only at rollover, and a "Summary" button that's always available.

### PRs

- **A small "PR" badge on the set on Home** right after it's logged. No pop-up.

### Layout

- **An "Overview | Log | Lifts" switch at the top,** like Programs | Exercises. Overview has the streak and calendar, Log lists every workout by month, and Lifts lists every exercise with search.

**Rejected:** one long scrolling page, which needed "All workouts" and "All lifts" pages behind it anyway.

### Calendar

- **A month grid showing every workout from every program,** with a dot in that day's own color. Weeks start on the reset day.
- **No legend.** Programs have different days and could share colors, so a legend can't be right for everyone. Tap a date and its workout shows under the calendar ("PPUL · Pull · 5x5").

**Rejected:** a program filter above the calendar, and a plain dot with no day colors.

### Lifts list

- **Each row shows the exercise and when its last PR was** ("PR 9 days ago"), so it's easy to see which lifts are still moving (screen L3).

**Rejected:** a mini graph with the change, heaviest ever (which means different things at different reps), estimated max (doesn't mean much for accessories), and "Best for N reps" (no single rep count to pick).

### A lift's page

- **Two graphs, stacked:** best weight for a rep count on top, estimated max under it.
- **The time range (3M · 6M · 1Y · All) changes both graphs** and lives in the Filter (see "Filter"). The shortest range is 3 months, so estimated max always has at least 3 monthly dots.
- **Everything uses what was actually lifted,** never the plan's goal. Plan 215 × 7 and get 6: it counts as 215 × 6.
- **Best weight for a rep count,** as a graph over time (heaviest Bench for 5).
- **The rep count is picked from a menu on the graph's title,** "Best for 5 reps ▾" (screen R1). It starts on the rep count done most, and the menu lists every rep count done, most done first, with how many times. It stays tidy with any number of rep counts, and it's a standard iPhone menu. **Rejected:** a table or chips for every rep count, swipe chips, and rep ranges (where a PR can hide inside a range).
- **Estimated one-rep max: one dot for the best of each month.** The formula gives different numbers at low and high reps, so a 531 day (260 × 2 ≈ 277) sits about 25 lb above a 5x5 day (215 × 5 ≈ 251), and a line through every workout zigzags by scheme. A month nearly always covers every scheme, so the monthly best is smooth, and it still drops if strength drops. **Rejected:** every workout (zigzags), best so far (a flat line forever after a drop), and best each rotation of schemes (replaced by monthly so it works the same in every program).
- **No hits anywhere in Tracker.** Rep numbers are also jump targets (`CLAUDE.md` "Goals"), so most workouts fall a little short by design until the jump is earned. A hit rate, a ring, or a dot row would be mostly empty, and it would read as failure. **Rejected:** a dot row, a set grid, a ring, a streak, tubes, and a "next jump" section (reps to go for each scheme, plus jumps over time), which didn't add enough.

**Rejected:** volume (weight × reps added up), lists of weight increases and of PRs as they happened, a "last few times" list, a switch between the two graphs, and 1-week or 1-month ranges (a lift done once a week has too few workouts, and estimated max would be one dot).

### Filter

- **A Filter button on every Tracker screen** opens the choices. They don't sit on the page.
- **Program applies to all of Tracker:** the calendar, streak, log, Lifts list, and a lift's page. Picking PPUL means only PPUL workouts count.
- **Time range (3M · 6M · 1Y · All) is on the Log and a lift's page.** Overview's filter has only the program, since the calendar always shows one month.
- **Starts on all programs and 3 months, every time.** The filter doesn't remember the last choice.

**Rejected:** a program picker stacked next to the time range on the page.

### Empty states

- **Nothing special.** With no history, the pages are just empty.

### Sharing

- **None.** Tracker is private, with nothing that leaves the phone.

### Visual style (pass 2)

Tracker uses Home's Liquid Glass look (`docs/home.md` "Visual style"): frosted glass cards, a soft glow behind them, capsule buttons, SF Pro with equal-width digits, and the rule that only main actions are solid.

- **Tracker is black, white, and grey, like Programs outside a day** (`docs/programs.md` "Visual style (pass 2)"). The glow is soft grey. Color always means a day.
- **Day color appears only as small dots:** on the calendar and on each Log row.
- **A lift's graphs have no color.** The line and dots are black (white in dark mode), with no key. **Rejected:** dots in the color of the day each lift was done on, with a "Push · Upper" key, which read worse than plain.
- **PRs have no color of their own.** "1 PR" is plain text, so color keeps one meaning.
- **A workout summary is in its day's color wherever it opens,** on Home or from the Log, so the same workout always looks the same.

**Rejected:** all grey, with day color only on the calendar. Also a gold PR color, which would make color mean two things.

- **Summary screens (on Home) in the day's color:** the day pill, ticks, and jumps (+2.5) are in the day color, and the PR count tile is solid day color, as on Home's "Push done". "Finish workout", "Resume workout", and "Start next workout" are glass. In the "Finish workout?" pop-up, Finish is solid day color and Not yet is glass.
- **The Filter is a floating glass panel** over a dimmed screen, with a solid Done, like Home's drawers.

Mockups: the "Tracker · color check" page (the comparison used to settle color) and the "Tracker · Liquid Glass" page (every screen in light and dark, see "Mockups").

## Still open

- The summary opened from the Log, with every set listed below it, isn't drawn yet
- The tab bar is a placeholder on every screen, until the overall layout is planned

## Mockups

**Canvas:** https://claude.ai/artifact/P5uocnirXmJ72uDcjekpkt (the same canvas as Home and Programs), page "Tracker · layout options". Grey boxes, so day colors are shades of grey. Only the picks are kept; the rejected options were removed.

- **B1–B2:** the "Overview | Log | Lifts" switch, on Overview and Log
- **L3:** the Lifts list, each row with its last PR
- **R1:** the rep count menu on the graph's title

**Page "Tracker · every feature":** pass 1 grey boxes with every decision above applied.

| # | Screen | Shows |
|---|---|---|
| 1 | Overview | Filter button, the switch, streak as a number only, calendar with no legend, Tue 17 tapped and its workout below |
| 2 | Log | Workouts by month, each with its program, time, and PRs |
| 3 | Lifts | Search, each exercise with "PR N days ago" |
| 4 | A lift | Bench: "All programs · 3 months" under the name, "Best for 5 reps ▾" graph, estimated max with a dot per month |
| 5 | Filter | Sheet over a lift: Program (All programs ticked), Time 3M · 6M · 1Y · All, Done |
| 6 | Home: summary | "Pull done": time, sets count, PRs, the new PR, each exercise with what was lifted and its jump, Start next workout |
| 7 | Home: Finish workout | Day list with DB Curl undone, Finish workout, and "Finish workout? DB Curl isn't done. 3 sets left." with Not yet / Finish |
| 8 | Home: finished early | The summary with DB Curl "Not done", Resume workout and Start next workout |

**Page "Tracker · color check":** Overview, then Log and a lift in two options: A (all grey) and B (day-color dots on Log rows and graph dots). B was picked for the Log, and A for the graphs.

**Page "Tracker · Liquid Glass":** pass 2, the same 8 screens restyled, light above dark. Screens 1–5 are grey with day-color dots on the calendar and Log. Screens 6–8 are on Pull, in blue.

The workouts and weights are placeholders.

## Where we left off

Pass 1 (layout) and pass 2 (visual style) are finished. Matt is happy with the 16 screens on the "Tracker · Liquid Glass" page of the canvas. **Next: plan the overall layout and Settings** in their own doc: the tab bar, where Settings lives, whether the timer shows on every page, and the Settings screen. Read the canvas back with the Artifact tool before editing it, since the local files were in a temporary folder.
