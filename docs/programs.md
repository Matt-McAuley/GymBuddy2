# Programs

The tab where programs are built and kept up to date: programs, their schemes and days, the exercises in each day with their sets and rest, supersets, and the shared list of exercises.

**Status:** behavior settled in planning, not built. Layout and visual style come next. See "Where we left off".

## What Matt is trying to do

- Type in a whole program quickly, without repeating the same numbers over and over
- Change a weight once and have it update everywhere it should
- Create a new exercise without leaving what he's doing
- Keep simple programs simple for people who don't rotate schemes

## What was clunky in 1.0

From `GYMBUDDY_1.md` and the screenshots in `../GymBuddy/assets/images/readme/`:

- **Updating was spread across many places.** The same lift existed once per scheme (`Bench - 5x5`, `Bench - 3x5`, `Bench - 531`), so one change meant several edits.
- **Creating an exercise took forever.** It needed its own form in its own sub-tab before it could be added to a day.
- **Every set was typed in by hand,** with a box for each rest, weight, and reps (`Edit_Exercise_Top.PNG`).
- **Six near-duplicate Add and Edit forms** (`components/Programs/Forms/`).

How the data model removes the per-scheme copies is in `docs/data.md` "The plan".

## Decided

### Structure

- **Two sections: Programs and Exercises.** Programs is where programs are built. Exercises is the shared list of every exercise, with each exercise's own settings.
- **Programs drill down:** program list → a program's page → a day → an exercise or superset.
- **There is no Days section.** Days belong to one program (`docs/data.md` "The plan"), so they live inside it.
- **Rows that open something show a › arrow.** The day list shows exercise names only, with no set summary.

**Rejected:** 1.0's three sub-tabs (Programs, Days, Exercises), and **no Exercises section** (jump size and bodyweight belong to the exercise and need a home).

### Program list

- **Lists every program.** The active one is marked.
- **"Use this program"** on a program's page makes it the active program. There is no check mark on the list.

### Program page

- **Name, schemes, days, and rotation settings.**
- **Schemes:** add, rename, drag ≡ to reorder, archive. A new scheme starts as a copy of the scheme before it (`docs/data.md` "Making setup and updates fast").
- **An "Add scheme" button is always on the program page,** even with one scheme. It is how a simple program gets its second scheme, after which scheme controls appear elsewhere.
- **Days:** add, drag ≡ to reorder, archive, and tap to open.
- **Rotation settings live here, per program:** the week reset day, when the scheme changes ("every week" or "after all days are done"), and whether empty weeks advance the scheme. App-wide settings stay in Settings (`docs/data.md` "Settings").

### Day colors

- **A new day starts as "Default":** black in light mode and white in dark mode, so Home's Done button and timer are always readable.
- **The other choices are Apple's system colors.** Black and white are not offered separately.

### Day page

- **The day's exercises and supersets in order.** Drag ≡ to reorder.
- **Tap an exercise or superset to open its page.**
- **Select → pick two or more → Superset,** the same as pairing on Home.

### Adding an exercise to a day

- **A search box lists matching exercises** from the Exercises list.
- **A "New exercise" button is always shown,** whether or not anything matches. It opens the new-exercise form with the name filled in from the search text, which can be fixed before saving.
- **Adding an exercise pre-fills its sets** from where it's already used (`docs/data.md` "Making setup and updates fast").

**Rejected:** only offering "Create" when nothing matches, since a near match would hide it.

### New-exercise form

- **Name, bodyweight, and jump size,** all shown. Nothing is hidden under "More", since there are only three things.
- **Jump size can be left blank** to use the default for the weight unit.

### Exercise page (inside a day)

- **The exercise's sets:** weight, reps, and rest.
- **"Same every scheme" is on by default.** Turning it off gives the exercise a list for each scheme, each starting as a copy, with a scheme switch at the top.
- **Scheme controls only appear once a program has two or more schemes.** People who don't rotate never see them.
- **Typing uses the number pad.** Weight has a decimal point (217.5). Rest is typed as digits, so `330` shows as `3:30`. "Next" moves to the next box.
- **"Add set" copies the set above.**
- **Changing a weight or reps offers to change matching sets.** Changing 215 to 220 offers every other set in the list at 215, above and below. It is always a prompt and never automatic.
- **Rest is required** for an exercise on its own. The page can't be left without it.

**Rejected:** a shorthand line (`5 x 5 @ 215`), which other people would have to learn, and scroll wheels, which are slow for typing 215 from scratch.

### Superset page

- **Tapping a superset in a day opens its own page.** It lists the exercises in it, each with a ›, and the round rests (Round 1, Round 2…, per scheme where needed, `docs/data.md` "The plan").
- **Weight and reps are still set on each exercise's page,** opened from the superset page. The rest column is hidden there, since the superset controls rest.
- **The round rest is required.** It starts filled in with the longest rest of the exercises in it.
- **An exercise's own rest isn't required inside a superset,** so exercises can be made inside a superset without typing a rest that won't be used.
- **Each exercise keeps its own rest in the background** while in a superset.
- **Splitting a superset shows a "Rest after splitting" panel.** It lists each exercise with its rest filled in (its remembered rest, or the round rest if it never had one). Change anything, then Done. This makes sure nobody forgets to check rest, without making them type it.

### Exercises section

- **Every exercise, shared by all programs.**
- **Tap one to see its name, bodyweight, and jump size,** plus **"Used in N places"**, which opens the update-everywhere screen (`docs/data.md` "Making setup and updates fast").

### Archiving

- **Programs, days, schemes, and exercises are archived, never deleted.** Archiving asks for confirmation, since getting something back takes effort.
- **Each list has an Archived section at the bottom** to restore from: archived programs on the program list, archived days on the program page, and archived exercises on the Exercises list.
- **Archived days drop out of the rotation.**
- **There is no delete for good.** Typos are fixed by renaming. History keeps working either way.

### Editing a day that's in progress

- **Programs changes reach Home from the next workout.** When a workout starts, Home locks in a copy of the day, so the exercise being done stays accurate.
- **Programs shows a banner while that day is being edited:** "Push is in progress. Changes start next Push."
- **Home can add exercises and sets for today only,** for when a machine is taken (`docs/home.md` "Day list").

### Nothing empty

- **A program must have at least one day, and a day at least one exercise.** Empty programs and days aren't allowed.
- **A new program can't be saved until it has a day with an exercise.** Backing out before then asks "Discard this program?" and throws it away. There are no drafts (kept simple for the first version).
- **The last day in a program can't be archived, and the last exercise in a day can't be removed.** The button is greyed out with a short reason.
- **Home with no program shows a welcome screen** telling you to create your first program, with a button that starts the walkthrough.

### First program

- **Making the first program walks through every step:** name → days → adding exercises → schemes. Nothing can be skipped.
- **The schemes step explains schemes and lets you carry on with just one,** so people who don't rotate see it without having to make several.
- **Every program after that just asks for a name** and opens its page, still under the "Nothing empty" rules.
- Welcome screens and the help page are app-wide and belong in the layout and Settings doc.

### Hints

TipKit shows one hint at a time, each only once. Programs has three:

| Hint | When it shows |
|---|---|
| "Turn off 'Same every scheme' for lifts that change between schemes" | The first time an exercise is opened in a program with 2 or more schemes |
| "See everywhere this exercise is used, and update them together" | On "Used in N places", the first time an exercise is opened in Exercises |
| "Tap Select to make a superset" | On a day page, once it has 2 or more exercises |

**Rejected:** a hint for "Use this program" (Matt doesn't want it), and hints for things that are covered elsewhere: tapping a day (the walkthrough), drag ≡ (standard iPhone behavior), typing rest (the box shows "3:30" as example text), and matching sets (already a prompt).

## Where we left off

Behavior is settled. Next is the layout (pass 1) as grey-box mockups, then the visual style (pass 2), carrying over Home's Liquid Glass look.

How to start pass 1, following how Home was done (`docs/home.md` "Mockups"):

1. **First, a small comparison** of the main open choice: how to switch between Programs and Exercises (a two-part switch at the top, or Exercises opened from a button on the program list). Settle it before drawing anything else.
2. **Then grey-box screens, one per feature:** program list, program page, day page, adding an exercise (search + "New exercise"), new-exercise form, exercise page (one scheme and several schemes, with the "apply to matching sets" prompt), superset page, "Rest after splitting" panel, Exercises list and an exercise's page, update-everywhere screen, Archived sections, the in-progress banner, the walkthrough steps, and Home's no-program welcome screen.
3. **Add them as a new page** on the Home canvas (https://claude.ai/artifact/P5uocnirXmJ72uDcjekpkt), read back with the Artifact tool first, and link them here.

## Still open

- Layout of every screen, including how Programs and Exercises are switched between
- The update-everywhere screen's layout
- What each walkthrough step looks like
