# Programs

The tab where programs are built and kept up to date: programs, their schemes and days, the exercises in each day with their sets and rest, supersets, and the shared list of exercises.

**Status:** behavior and layout (pass 1) settled, not built. Visual style (pass 2, Liquid Glass) settled. See "Where we left off".

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
- **A two-part "Programs | Exercises" switch sits under the title** at the top of the tab, so both lists are one tap apart. Mockup: "Programs · switch options" page on the canvas (`docs/home.md` "Mockups"), screens A1 and A2.
- **Programs drill down:** program list → a program's page → a day → an exercise or superset.
- **There is no Days section.** Days belong to one program (`docs/data.md` "The plan"), so they live inside it.
- **Rows that open something show a › arrow.** The day list shows exercise names only, with no set summary.

**Rejected:** an "Exercises" button on the program list that opens Exercises as its own page, which looked clunky. Also 1.0's three sub-tabs (Programs, Days, Exercises), and **no Exercises section** (jump size and bodyweight belong to the exercise and need a home).

### Program list

- **Lists every program by name only,** with no summary such as a scheme count. The active one is marked.
- **"Use this program"** on a program's page makes it the active program. There is no check mark on the list.

### Program page

- **Name, schemes, days, and rotation settings.**
- **Schemes:** add, rename, drag ≡ to reorder, archive. A new scheme starts as a copy of the scheme before it (`docs/data.md` "Making setup and updates fast").
- **A "Schemes" toggle on the program page turns schemes on or off.** Off (the default for a new program): the program runs on one hidden default scheme, and nothing about schemes shows anywhere (no scheme list, no "Scheme changes" setting, no scheme controls on exercise pages). On: the scheme list and "Add scheme" appear, and scheme controls show even with one scheme.
- **Turning schemes off hides them; it doesn't delete them.** Turn the toggle back on and the old schemes return as they were.
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
- **If the exercise is already used somewhere, a "Copy sets from" list appears** after tapping it: every place it's used, grouped by program, plus "Start blank". If it isn't used anywhere, it's added with one blank set. **Rejected:** picking a place automatically (same program first, or the most recently edited place).

**Rejected:** only offering "Create" when nothing matches, since a near match would hide it.

### New-exercise form

- **Name, bodyweight, and jump size,** all shown. Nothing is hidden under "More", since there are only three things.
- **Jump size can be left blank** to use the default for the weight unit.

### Exercise page (inside a day)

- **The exercise's sets:** weight, reps, and rest.
- **"Same every scheme" is on by default.** Turning it off gives the exercise a list for each scheme, each starting as a copy, with a scheme switch at the top.
- **With schemes on, every exercise page shows "Same every scheme".** The scheme switch appears once it's turned off. With schemes off, neither shows.
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
- **Programs shows a banner while that day is being edited:** "Changes start next Push"
- **Home can add exercises and sets for today only,** for when a machine is taken (`docs/home.md` "Day list").

### Nothing empty

- **A program must have at least one day, and a day at least one exercise.** Empty programs and days aren't allowed.
- **A new program can't be saved until it has a day with an exercise.** Backing out before then asks "Discard this program?" and throws it away. There are no drafts (kept simple for the first version).
- **The last day in a program can't be archived, and the last exercise in a day can't be removed.** The button is greyed out with a short reason.
- **Home with no program shows a welcome screen** telling you to create your first program, with a button that starts the walkthrough.

### First program

- **Making the first program walks through every step:** name → days → adding exercises → schemes. Nothing can be skipped.
- **The schemes step is the Schemes toggle, off by default,** with one line explaining what schemes are. People who don't rotate just tap Finish.
- **Every program after that just asks for a name** and opens its page, still under the "Nothing empty" rules.
- Welcome screens and the help page are app-wide and belong in the layout and Settings doc.

### Hints

TipKit shows one hint at a time, each only once. Programs has three:

| Hint | When it shows |
|---|---|
| "Lift changes by scheme? Turn this off." | The first time an exercise is opened in a program with schemes on |
| "Update every place this is used at once" | On "Used in N places", the first time an exercise is opened in Exercises |
| "Tap Select to make a superset" | On a day page, once it has 2 or more exercises |

**Rejected:** a hint for "Use this program" (Matt doesn't want it), and hints for things that are covered elsewhere: tapping a day (the walkthrough), drag ≡ (standard iPhone behavior), typing rest (the box shows "3:30" as example text), and matching sets (already a prompt).

### Layout (pass 1)

Settled before drawing the full set of grey-box screens:

- **Program page is one scrolling page:** name and "Use this program", then Days, Schemes, Rotation settings, and Archived. **Rejected:** hiding rotation behind a "Rotation ›" row.
- **Each set on an exercise page is its own card** with labelled Weight, Reps, and Rest boxes. The scheme switch sits at the top when there are 2 or more schemes. **Rejected:** a table with columns, which is compact but has small boxes.
- **"Add exercise" slides up a sheet** over the day, like Home's drawers. Swipe down to cancel. **Rejected:** a new page with a back button.
- **The first-program walkthrough uses the real pages** with a "Step 2 of 4" banner at the top and a Next button. **Rejected:** separate full-screen steps.
- **The matching-sets offer is a standard iPhone pop-up:** "4 other sets are 215. Change them to 220?" with Just this / Change all. **Rejected:** a bar under the set.
- **The update-everywhere screen is a grouped tick list:** program → day → scheme, each with its sets, matching places already ticked, the new value typed once, then "Apply to N". **Rejected:** a list of links to edit each place one at a time.

### Visual style (pass 2)

Programs uses Home's Liquid Glass look (`docs/home.md` "Visual style"): frosted glass panels, a soft glow behind them, capsule buttons, SF Pro, and the same rule that only main actions are solid.

- **Outside a day, Programs is black, white, and grey.** This covers the program list, the program page, the Exercises list, an exercise opened from Exercises, and update-everywhere. The glow is soft grey. Main actions such as "Use this program", Save, Next, and Apply are solid black in light mode and solid white in dark mode.
- **Inside a day, the screen takes that day's color, like Home.** This covers the day page and everything opened from it: an exercise, a superset, and the Add exercise sheet. The day's glow is behind the glass, the day page's title is in the day color, and main actions are solid day color.
- **A Default day stays black, white, and grey,** with a grey glow, so it looks like the screens outside a day.
- **Color always means a day.** With no app color, the day dots on the program page are the only color there, so they are easy to pick out.
- **Toggles are black (white in dark mode), not Apple's green,** so no color appears that isn't a day's.
- **"+ Add day", "+ Add scheme", and "+ Add exercise" are plain text rows** at the bottom of their lists: black or white outside a day, and the day color inside one.
- **Tips inside a day get a light wash of the day color** with a day-color edge.

**Rejected:** a separate app color for Programs, which would make color mean two things ("a day" and "the app").

- **The same Liquid Glass details as Home:** sheets and pop-ups are floating glass panels over a dimmed screen, the box being typed in gets a light fill and an outline in the screen's color, and buttons and switches are capsules.
- **Section labels are in sentence case** ("Days", "Weight"). Small tags such as "SUPERSET", "ACTIVE", and "STEP 1 OF 4" stay in capitals, as on Home.

Mockups: the "Programs · color check" page on the canvas (the program page in grey and the Push day page in red, in light and dark), and the "Programs · Liquid Glass" page (every screen in light and dark, see "Mockups").

## Mockups

**Canvas:** https://claude.ai/artifact/P5uocnirXmJ72uDcjekpkt (the same canvas as Home, private to Matt)

- **Page "Programs · every feature":** pass 1 grey boxes, one screen per feature. They decide where things go, not how they look.
- **Page "Programs · switch options":** the chosen Programs | Exercises switch (A1, A2), kept for history.
- **Page "Programs · color check":** the comparison used to settle color in pass 2.
- **Page "Programs · Liquid Glass":** pass 2, the same screens restyled, light above dark. Screens in a day use Push red, except screen 6 (DB Curl), which is on Pull, in blue. The walkthrough stays grey, since a new day starts as Default.

"Arms" (screen 15) is filler. Rest times and the 5x3 weights are placeholders.

| # | Screen | Shows |
|---|---|---|
| 1 | Program list | Programs \| Exercises switch, names only, ACTIVE tag, + for a new program, Archived row |
| 2 | Program page | Use this program, Days with color dots, Schemes toggle (on) and list, Rotation settings, Archived days (whole scroll) |
| 3 | Day page | Color row, Select tip, exercises and a superset by name with ›, + Add exercise |
| 4 | Add exercise sheet | Search "Ben", + New exercise "Ben" always shown, matching Bench |
| 4b | Copy sets from | After tapping Bench: every place it's used, grouped by program, and Start blank (in the bottom row) |
| 5 | New-exercise form | Name filled in from the search, Bodyweight, Jump size left blank for the default |
| 6 | Exercise page, schemes off | A card per set with Weight, Reps, Rest, no scheme controls, + Add set |
| 7 | Exercise page, schemes on | First-time tip, "Same every scheme" off, 5x5 \| 5x3 \| 531 switch |
| 8 | Change matching sets? | iPhone pop-up after typing 220: Just this / Change all |
| 9 | Superset page | Its exercises with ›, Split, rest after each round |
| 10 | Exercise inside a superset | Weight and Reps only, since the superset sets rest |
| 11 | Rest after splitting | Sheet listing each exercise's rest and where it came from, Done |
| 12 | Exercises list | The switch on Exercises, search, Archived row |
| 13 | An exercise | Name, Bodyweight, Jump size, first-time tip, "Used in 6 places" |
| 14 | Update everywhere | Weight \| Reps \| Rest, 215 → 220, grouped tick list, Apply to 4 places |
| 15 | Archived days | Restore, the archive confirmation, the greyed-out last-day archive |
| 16 | Day in progress | "Changes start next Push" banner |
| 17 | Home with no program | Welcome and "Create your first program" |
| 18–21 | Walkthrough | Real pages with a "Step N of 4" banner: name, days, each day's exercises, Schemes toggle, off by default |

**Approved in review along with the screens:** a ··· menu on the program, exercise, and exercise-in-day pages (for rename, duplicate, archive, remove), and swiping a scheme or set row to rename, archive, or remove it.

## Where we left off

Pass 1 (layout) and pass 2 (visual style) are finished. Matt is happy with the 22 screens on the "Programs · Liquid Glass" page of the canvas. **Next: plan Tracker** in its own `docs/tracker.md`. Read the canvas back with the Artifact tool before editing it, since the local files were in a temporary folder.

## Still open

- Layout of every screen
