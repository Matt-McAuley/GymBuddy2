# Home

The screen Matt uses mid-workout: it shows today's day and scheme, the current exercise and set, the rest timer, and a Done button that logs each set to history.

**Status:** behavior and layout settled in planning (pass 1), not built. The visual style (pass 2) is next. See "Where we left off".

## What Matt is trying to do

- Log a set in one tap, without stopping to type numbers that match the plan
- Have the rest timer start on its own, and see it on the lock screen and Dynamic Island
- Run supersets in whatever order the gym allows
- Fix mistakes quickly, both straight away and a few sets later
- See the whole day when needed, to jump around, reorder, or regroup exercises

## What was clunky in 1.0

From `GYMBUDDY_1.md` and the screenshots in `../GymBuddy/assets/images/readme/`:

- **Nothing was logged.** The set arrows only moved a counter (`components/Home/Counter.tsx`).
- **The timer was manual** and lost its place on restart (`components/Home/Timer.tsx:53`).
- **Superset logic was copied into three files,** with a likely off-by-one bug for uneven set counts (`components/Home/exerciseDisplays/SuperSet.tsx:13`).
- **Superset rest used the longer of the two exercises' rests** (`components/Home/Timer.tsx:19`), which needed `Dips - SS` style copies to get the right rest.
- **The look was clunky:** heavy borders, hard-coded sizes, and no dark mode.

## Mockups

**Canvas:** https://claude.ai/artifact/P5uocnirXmJ72uDcjekpkt (private to Matt; a Claude Design canvas)

- **Page "Home · every feature":** the agreed layout, one screen per feature. This is the reference for pass 2.
- **Page "Pass 1 options":** the first three layout options (A, B, C), kept for history.

All screens are grey boxes on purpose: they decide where things go, not how they look. "Cable Fly" in screens 6 and 9 is filler, not from Matt's program.

| # | Screen | Shows |
|---|---|---|
| 1 | Lifting | Day pill, sets drawer collapsed as chips, big exercise name, set number with timer icon and time, big tappable weight and reps, Done, "Up next" bar |
| 2 | Resting | Done turned into the timer (pause icon, −15 / +15), set Undo bar, first-time TipKit tip |
| 3 | Sets drawer open | Drawer from the top covering the exercise, done / now / upcoming sets, area around it dimmed |
| 4 | Scroll wheel | Weight (0.5 lb) and reps wheels, "This set / Rest of today", Save |
| 5 | Day and scheme picker | Drawer from the top: Day grid with done ticks, Scheme switch, rotation note, Switch |
| 6 | Day list pulled up | Collapsed rows, one row held open to edit its sets, superset with Split, Select for pairing, help line |
| 7 | Superset mid-round | Normal lifting screen plus a switch between the superset's exercises |
| 8 | Jump popup | "Go up next time?", 215 → 217.5 (tappable), matching places, See all, Not yet / Jump |
| 9 | Day done | "Push done", sets / time / PRs, every exercise with its +jump, "Start Pull now" |

**Editing the canvas in a new session:** the local files were in a temporary scratchpad folder and are gone. Read the canvas back with the Artifact tool (`action: "read"` on the URL above, then each `project/*.dc.html` file) before changing it.

## Decided

### Lifting screen

- **The current exercise fills the screen,** with a large name and large weight and reps numbers (pass 1 option B).
- **The top row** holds the "PUSH · 5x5" day pill on the left and "Exercise 1 of 7" on the right.
- **The set's rest shows as just a timer icon and the time,** next to "Set 2 of 5". No "rest" label, to keep it minimal.
- **An "Up next" bar at the bottom** names the next exercise and is the handle for pulling up the day list.

**Rejected: +/- steppers on the main screen (option A)** as too busy, and **set rows as the main view (option C)**, which became the top sets drawer instead.

### Logging sets

- **One big Done button logs the current set.** It saves the planned weight and reps unless they were changed.
- **Numbers are changed before tapping Done.** The screen always shows exactly what will be saved.
- **Tap a number to change it.** A scroll wheel opens, starting on the current number. Weight moves in 0.5 lb steps (covering machine and free weights) and reps in steps of 1. No +/- buttons.
- **A changed weight applies to "This set" or "Rest of today"** (every remaining set at that weight in this workout).
- **Home never edits the program,** except by accepting a jump in the jump popup. Other program changes are made in Programs.
- **An Undo bar appears above the Done button for a few seconds after Done,** for quick mistakes.
- **Sets go in order.** The current set is always the first one not done. Upcoming sets can have today's numbers changed ahead of time, but you can't jump to them.

**Open:** the kg wheel step. 0.5 kg is proposed, with 0.25 kg as the alternative.

### Sets drawer (top)

- **Shows the current exercise's sets,** done and coming up. Collapsed by default as a row of chips (done, current, upcoming).
- **Expanded, it covers the exercise view.** Tapping the dimmed area outside closes it.
- **Tap a done set to edit it** (for example, logged 5 reps but did 4), or **an upcoming set to change today's numbers** (for example, drop set 5 to 205). Both open the scroll wheel.

### Rest timer

- **Done becomes the timer.** After Done, the Done button turns into the countdown, and turns back into Done when rest ends.
- **Tap to pause or play. Swipe across to skip. Hold to restart.**
- **Skip and restart show an Undo** in the same bar as set Undo ("Rest skipped · Undo"), with a buzz. No confirm popup, since one would get annoying.
- **Side buttons take time off and add it.** The amount is a setting (5, 10, 15, 30, or 60 seconds, default 15).
- **When rest ends,** a notification fires and the Live Activity shows that rest is over.
- **The timer stores its end time,** not a running count, so it survives restarts and backgrounding (from `GYMBUDDY_1.md` lessons).

### Hints

- **First-time hints use TipKit,** Apple's built-in tip tool.
- **The timer tip** ("Tap to pause · Swipe to skip · Hold to restart") points at the timer on the first rest ever. It comes back on later rests if ignored, up to 3 rests in total, and is gone for good once dismissed or once any timer gesture is used.
- **Tips sync through iCloud,** so a new phone doesn't repeat them. A "Show tips again" switch in Settings resets them.
- **One tip covers all three gestures,** rather than one tip per gesture.

### Day list (bottom)

- **Pulls up from the "Up next" bar** to jump between, reorder, split, or pair exercises, and to edit sets.
- **Tap an exercise to jump to it.** After it, Home continues with the next exercise in the list, not the one that was passed over. A passed-over exercise stays undone unless it is dragged into place. There is no separate Skip action.
- **Hold an exercise to open its sets** as chips. Tapping a chip edits that set with the scroll wheel. Rows show no chips otherwise, so a tap always means "jump".
- **Drag ≡ to reorder.**
- **The help line reads:** "Tap to go · Hold to edit sets · Drag ≡ to reorder", then "Changes only apply to today's workout".

**Rejected: tap to expand a row, with a "Go to" button inside** (2 taps to jump), and **separate tap targets for the name and the chips** (too easy to mis-tap mid-workout).

### Jump popup

- **Appears after an exercise's last set** when every set hit its number (`docs/data.md` "Jump rules").
- **Shows the old and new weight** (215 → 217.5). The new weight can be tapped to change the amount.
- **Shows how many matching places will update,** with "See all" for the full list.
- **"Not yet" or "Jump".** Jump is the only way Home changes the program.

### Supersets

- **A superset is worked in rounds.** Round N includes every exercise in the superset that has at least N sets. Uneven set counts need no special case: with Squat at 4 sets and Curl at 3, round 4 is Squat alone.
- **A superset uses the normal lifting screen,** with the sets drawer, timer, and numbers showing whichever exercise is active. A small switch swaps between the exercises.
- **One Done per exercise, in any order.** Before the round is finished, Done swaps straight to the next exercise in the round with no timer.
- **Rest starts when every exercise in the round is done.**
- **Any number of exercises** can be in a superset.
- **Supersets can be split, and exercises paired, mid-workout.** These changes only affect that workout's history, never the plan (`docs/data.md` "Decided").

How the superset's rest is stored is in `docs/data.md` under "The plan".

**The rest rule**, written once in one place and tested:

| Situation | Rest used |
|---|---|
| Normal exercise | That set's rest |
| Planned superset, including leftover rounds | That round's rest, for the current scheme |
| Split from a planned superset mid-workout | Each exercise uses its round's rest |
| Grouped at the gym | The longest rest involved. An exercise from a planned superset contributes its round rest |

**Rejected: storing rest on the "second" exercise.** The order changes from workout to workout, so "second" has no fixed meaning.

**Rejected: rest on every set, with a round resting as long as its longest set.** With a per-scheme exercise (Bench) and an all-schemes exercise (Face Pull) in one superset, a shorter 531 rest can't be set: Face Pull's single list would either override it or carry the change into every scheme.

### Day and rotation

- **Day and scheme are picked separately.** Tapping the "PUSH · 5x5" pill drops a drawer from the top with a Day grid (with ticks on days done this week) and a Scheme switch, confirmed with a Switch button.
- **Two top drawers are fine** (sets and day/scheme) because they do different jobs. Things at the top open downward, and things at the bottom open upward.
- **Switching day or scheme mid-workout ends the current workout.** Its finished sets are kept and that day counts as done (a partial workout counts, `docs/data.md`). The new day or scheme starts its own workout on its first Done. It works just like doing half a workout and coming back hours later.
- **Going back to a day and scheme done earlier picks up where it left off,** if it's before rollover. The sets already done stay ticked and it continues as the same workout.
- **A scheme override changes the scheme for the rest of the week,** and the rotation carries on from there like a wheel (see `docs/data.md` "Rotation").
- **A finished day stays on screen until rollover,** with every exercise ticked, a summary (sets, time, and PRs), and each exercise's jump shown as "+2.5". A "Start Pull now" button moves on by hand. Home never moves on by itself.

**Rejected: a native iOS pull-down menu for the picker** (Matt doesn't like the look), and **a bottom sheet** (it opened from the opposite end of the screen to the button).

### Music

- **Whether Home has music controls is decided later,** once Music is planned and Spotify's limits are checked.

## Look and feel

- **Light and dark mode.**
- **Modern, high-tech, and smooth,** with a very quick, intuitive UI. Matt has no reference apps in mind and is trusting judgement here.
- **Minimal and clean.** No labels where an icon is enough. The user isn't stupid.
- **Planned in two passes:** layout first (pass 1, done), then the visual style (pass 2, next).

## Where we left off

Pass 1 (layout) is finished, and Matt is happy with the nine screens on the "Home · every feature" page of the canvas. The doc edits from this session (this file, `docs/data.md`, and `CLAUDE.md`) are not committed yet.

## Next: pass 2 (visual style)

Pass 2 keeps the agreed layout and decides how it looks:

- **Colors:** background, accent, and how each day's color shows up (1.0 showed Push in red)
- **Fonts:** which typeface, and how big and bold the numbers are
- **Shapes:** corner rounding, borders vs shadows, how much depth
- **Light and dark mode:** both versions
- **Motion,** described rather than built: Done turning into the timer, drawers sliding, Undo appearing

**Plan:** make 2–3 different style directions on the Lifting and Resting screens, each in light and dark, on a new page of the same canvas. Matt picks one or mixes them. Then apply the chosen style to all nine screens.

## Still open

- How pairing works in detail ("Select" in the day list is sketched, not decided)
- The kg wheel step (see "Logging sets")
- Empty states: no program, no days, no exercises
- What the Live Activity shows, including mid-superset
- The overall layout (tab bar, where Settings lives, whether the timer shows on every page), planned in its own doc
