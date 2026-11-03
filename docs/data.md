# Data Model

How GymBuddy 2 stores programs and workout history, and how Home, Programs, and Tracker share that data.

**Status:** settled in planning, not built. One check remains before writing the model (see "Before building").

## What Matt is trying to do

**Matt's training:** a modified 5/3/1 on a Push, Pull, Upper, Lower split (legs matter less because of basketball). 1.0 called Lower "Lower & Arms", but the name is just Lower.

- Three schemes rotate: 5x5, then 5x3, then a 5-3-1-3-5 pyramid
- Compound lifts change with the scheme. Weight can differ per set within a scheme
- The usual week is Tuesday Push, Thursday Pull, Saturday Upper, Sunday Lower, but life moves days around

**The app must:**

- Work for anyone on the App Store, not only people running this exact plan
- Stop Matt from having to pick 5x5, 5x3, or 531 by hand. The day and scheme rotate automatically, with manual override
- Log every workout, including which day and scheme it was
- Make creating and updating exercises, programs, and schemes much faster than 1.0, which takes far too long

## What was clunky in 1.0

From the seeded program in `../GymBuddy/db/dbFunctions.ts:94`:

- **Schemes did not exist as a concept.** Matt's 2 real programs (PPUL and PPL) were stored as 6 programs and 30 days, because each scheme needed its own full copy.
- **Sets were stuck to the exercise.** That forced names like `Bench - 5x5`, `Bench - 3x5`, `Bench - 531`, and `Dips` vs `Dips - SS`, where the only difference was rest time.
- **Only compound lifts actually change by scheme.** Accessories such as `DB OHP` and `Lateral Raise` are identical in all three. Their copies only add editing work.
- **The pyramid reuses the other schemes' weights.** Bench is 215 x 5 in 5x5, 235 x 3 in 5x3, and 215 / 235 / 260 / 235 / 215 in 531. One weight increase means editing many places.
- **The same lift can have different weights per set** (`Lat Pulldown` 143, 143, 121), so weight has to live on each set.
- **Machine weights do not move in 2.5 lb steps** (`Tricep Extension` 33, `Face Pull` 22), so a single global jump will not fit every exercise.

## Decided

- **Plan and history are separate.** History keeps its own copy of the numbers, so editing a plan never changes past workouts.
- **Exercises are archived, not deleted,** so history keeps working.
- **An exercise is just a name.** "Bench" is one exercise. The program sets weight, reps, and rest.
- **Sets can be overridden while lifting.** The actual weight and reps get recorded.
- **Workouts start on the first completed set and finish on the last completed set.** There is no Start or Finish button.
- **Exercises can be skipped or reordered during a workout,** and superset pairings can change.
- **A set's rep number is also its jump number.** There is no separate target field. Bench 5x5 is entered as 5, 5, 5, 5, 7, and 531 as 5, 3, 2, 3, 5. Hit the numbers and you jump. Do less and you stay.
- **Every set must hit its number at the planned weight or heavier** to earn a jump.
- **The jump popup appears after all sets of the exercise are finished,** as a yes/no, and the amount can be changed there.
- **Jump size is per exercise,** with a default of 2.5 lb for exercises that do not set their own.
- **Home has two separate weight controls.** "Today" changes the weight for all remaining sets at that weight in this workout, and leaves the plan alone. A separate button edits the number in the program.
- **A partial workout counts as done.** One completed set marks that day as done at rollover.
- **Order and superset changes during a workout never save to the plan.** Those are edited in Programs. Weight is the only plan number that can change from Home.
- **Warm-up sets are not logged.**
- **The database is SQLite through SQLiteData,** an MIT-licensed library built on GRDB. Matt already knows SQL, Tracker benefits from database-level totals, and upgrade steps are explicit and testable. SwiftData and Core Data were considered and passed over. The costs are relying on a small company's maintenance and about 12 extra packages.
- **Each set has an optional RPE (how hard it felt), and each workout has optional notes.** Matt will not use these, but other people will.
- **The heaviest weight in the list jumps automatically, along with sets equal to it.** "Equal" means same weight and same reps. Everything else can be selected and jumps by the same amount.
- **Other sets go up by manual choice.** For `DB Curl` at 35, 35, 30 with a 5 lb jump, you select which sets jump.
- **Tracker includes a calendar and a log of every workout,** including which day and scheme were done.
- **The day rotates at a rollover time.** It defaults to midnight local time and is a setting, for people who lift late.
- **Rotation can always be overridden** by skipping around manually.
- **Day order is Push, Pull, Upper, Lower,** then back to Push.
- **The scheme changes when the week is up, even if a day was missed.** The week ends on a fixed reset day, which is a setting and defaults to Monday.
- **After a week change, the next workout is the first day** (Push). Missed days are dropped.
- **A week with no workouts keeps the same scheme by default,** so a vacation does not skip one. This is a setting.
- **Finishing every day early shows the week as complete.** Manual override still allows training.
- **When the scheme changes is a setting:** "every week" or "after all days are done".
- **Places with a different weight, or the same weight with different reps, are never ticked automatically.** If ticked by hand, they increase by the same amount rather than matching the new weight.
- **Rotation settings are per program.**
- **PRs are per exercise and rep count,** with filters by program.
- **Lbs and kg are both supported.** Switching converts weights automatically.
- **Bodyweight exercises are supported,** and only added weight is tracked.
- **No timed exercises** for now.
- **No import from 1.0.** Matt will enter the program by hand, which also tests how easy that is.

## Design

### The plan

```
Exercise                  name, is it bodyweight, jump size (optional), archived?
                          Shared by all programs.

Program                   name, archived?, rotation settings
├── Scheme                name, position (5x5, 5x3, 531)
└── Day                   name, color, position (Push, Pull, Upper, Lower)
    └── DayExercise       which Exercise, position, superset group (optional)
        └── PlannedSet    scheme (blank = all schemes), position, weight, reps, rest
```

- **One Day, not one per scheme.** Push exists once. Bench inside Push has a set list for each scheme, and accessories have one list marked "all schemes". This reduces 30 days to 10.
- **A program with one scheme is just a simple program.** People who do not rotate never need to think about schemes.
- **A superset is a label shared by two exercises,** not a special type. This removes 1.0's duplicated superset logic and makes uneven set counts simple. Each exercise just has its own set list.
- **Rest belongs to the set in a specific day,** which removes the need for `Dips - SS`.
- **Days belong to one program** rather than being shared. A "duplicate program" action covers the reuse case. Only exercises are shared between programs.
- **Everything has a permanent ID,** so renaming is one simple change.

### Making setup and updates fast

Numbers are stored as independent copies on each planned set, which keeps any program free to differ. Two tools remove the repetition.

**Copy when creating:**

- **New scheme:** starts as a copy of the previous scheme, then you override what differs
- **Accessories:** use one "all schemes" set list, so only main lifts need separate numbers
- **Add set:** copies the set above it
- **Adding an exercise to a day:** pre-fills from where that exercise is already used
- **Duplicate:** copy a whole program, or copy a day or scheme from another program

**Find and update when changing**:

- One "update everywhere" screen lists every place an exercise is used, grouped by program, day, and scheme
- Other places with the same weight and the same usual reps are already ticked. Everything else is unticked, and you can tick anything
- It opens from a jump popup and from editing an exercise in Programs
- Weight, reps, and rest can all be updated this way
- In the gym, the popup stays short: "Curls 35 → 37.5, 8 matching places. Yes / No / See all"
- A place with a different weight that gets ticked by hand goes up by the same amount

**Rejected: linking across programs so one edit changes everything.** Updates would be automatic, but editing one program could silently change another.

This part adds nothing to the data model. "Where is this used?" is a search of the planned sets.

### Jump rules

**The rule:** if every set in the workout's list hit its rep number at the planned weight or heavier, the app offers a jump for the heaviest weight in that list. There is no target-set setting, because the goal is just the rep number entered on the set.

The popup then uses the same ticking as the update list: sets with the same weight and usual reps start ticked, and everything else can be ticked by hand and goes up by the same amount.

Matt's Bench, using the weights from 1.0 (the 5x3 goal is a placeholder):

| Scheme | What is entered | What jumps |
|---|---|---|
| 5x5 | 215 x 5, 5, 5, 5, 7 | 215: all of 5x5, plus 531's two 5s |
| 5x3 | 235 x 3, 3, 3, 3, 7 | 235: all of 5x3, plus 531's two 3s |
| 531 | 215 x 5, 235 x 3, 260 x 2, 235 x 3, 215 x 5 | 260: only the top set |

`DB Curl` at 35, 35, 30 x 12 with a 5 lb jump: the two 35s start ticked and go to 40. Tick the 30 by hand and it goes to 35.

**Known trade-off:** matching relies on weight and reps. If two groups of sets ever land on the same weight and reps, they will both start ticked, and you untick the wrong one. Matt is fine with this: BB Row's top set at 195 really is done for 3 reps, so it moving with the 3s is correct.

**Rejected: target sets and weight groups.** A setting on each set for what jumps, plus groups of sets that move together. It would make matching exact, but it adds concepts that the rep numbers and tick list already cover.

### The history

```
Workout                   date, start and last-set times, day and scheme,
│                         links to program, day, and scheme plus copies of their names,
│                         notes (optional)
└── WorkoutExercise       which Exercise, order actually done, superset group
    └── WorkoutSet        planned weight and reps (copied), actual weight and reps,
                          RPE (optional), time completed
```

- Saving planned and actual numbers lets Tracker show hits and misses
- Changes during a workout (order, skips, superset swaps) only affect that workout's history, not the plan
- Only raw sets are stored. Charts and records are calculated from them, so new stats can be added later and still cover old workouts

### Rotation

**No scheduled job is needed.** iPhone apps cannot reliably wake up at an exact time. Instead, the app works out what has changed when opened (and if it stays open past rollover).

Each program remembers its current scheme, when that scheme's week began, and which days are done this week. Two things move separately:

**The day follows what you did:**

1. Home shows the next day not yet done this week, under the current scheme
2. Logging a set marks that day as done, and it stays shown as completed until rollover
3. After rollover, the next undone day appears
4. Days without a workout change nothing. The same day waits
5. Choosing a different day is a manual override. Doing Lower early marks Lower as done, and the skipped day stays next

**The scheme follows the calendar:** when the week is up, the scheme moves on (5x5 → 5x3) even if days were missed.

Example with the default Monday reset: Tuesday Push, Thursday Pull, and Sunday Upper on 5x5, with Lower missed. On Monday the scheme becomes 5x3, and the next workout is Push.

A week with no workouts keeps the current scheme by default, and several empty weeks never skip more than one scheme.

### Weight units

Weights are stored in one internal unit and converted only for display. Stored numbers are never rewritten on a switch, so 225 lb going to kg and back stays 225 lb. Jump sizes are stored per unit, because a sensible kg jump is not exactly 2.5 lb.

### Records and Tracker stats

"Best weight for 5 reps" compares like with like, so 5x5 and 531 sets can count together, and 215 x 5 is the same achievement in any program. Each set's workout records its program and scheme, which is what the filters use. Estimated one-rep max can be added at any point.

### Settings

Weight unit, rollover time, week reset day, when the scheme changes, whether empty weeks advance the scheme, default jump size for each unit, and active program.

## What each page needs

- **Programs:** build exercises, schemes, days, and planned sets. Needs a fast way to enter set lists, since the plan is typed in by hand.
- **Home:** reads the plan and rotation, then writes history. Timer rest comes from the current planned set.
- **Tracker:** reads history only, so nothing done there can affect the plan.

## Before building

Check SQLiteData's iCloud sync rules. They could not be confirmed during planning, and they may affect how tables are structured.

## App Store consequences

- **User data can never be wiped by an update.** 1.0 could drop all tables and start over. This app needs planned, versioned database upgrades from version 1, so getting the model right up front matters.
- **Backups and moving to a new phone** need an answer before release.
