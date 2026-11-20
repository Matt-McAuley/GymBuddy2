# Layout and Settings

What ties the pages together: the tab bar, the timer bar that follows you off Home, where Settings lives, the Settings screen, help, and the first time the app is opened.

**Status:** behavior and visual style (Liquid Glass) settled in planning, not built. See "Where we left off".

## What Matt is trying to do

- Move between Home, Programs, and Tracker in one tap, from anywhere
- Keep an eye on rest while checking a graph or a program, without going back to Home
- Change the rarely-touched settings (lb or kg, jump size, rollover) without them cluttering the pages used every workout

## Decided

### Tab bar

- **Three tabs: Home, Programs, Tracker.** Each has an icon and a label.
- **Apple's own iOS 26 tab bar:** a floating glass capsule. The current tab gets a grey fill, not a day color, since color always means a day.
- **Shows on every screen,** including inside a program, a day, an exercise, and Settings, so Home is one tap away from anywhere. The one exception is the first-program walkthrough, a setup flow you finish or cancel.
- **Shrinks when you scroll down** a long list (Log, Lifts, a program) to a small button for the current tab, and comes back when you scroll up. Home doesn't scroll, so it never shrinks there.

**Rejected:** Settings as a 4th tab (a tab for something rarely opened), and a tab bar that hides inside drill-down pages.

### Timer bar

- **Why it's needed:** iOS hides an app's own Live Activity from the Dynamic Island while that app is open. Without the bar, resting and flipping to Tracker would show no countdown anywhere.
- **A glass bar just above the tab bar,** like Apple Music's mini player. It uses SwiftUI's built-in spot for this (the tab bar's bottom accessory).
- **Shows only while resting, and never on Home,** since Home already has the big timer.
- **Holds a pause button, the time left, and the next exercise** ("Bench"). Time and pause are in the day's color, with a thin day-color line along the bottom showing how much rest has passed.
- **Tapping the bar (outside pause) goes to Home.**
- **When the tab bar shrinks,** the timer bar moves up beside the small tab button.
- **When rest ends,** the bar shows "Rest over" with a buzz, and tapping it goes to Home. It goes away once you're back on Home.

**Rejected:** a solid day-color bar (option A on the canvas), and Home-only with no bar.

### Where Settings lives

- **A gear in the top right of Programs,** beside the + button, above the title and the Programs | Exercises switch, so it shows on both lists. It's gone once you open a program.
- **Settings opens as a full page,** pushed in from the right with a "‹ Programs" back button.
- **Rotation settings stay on each program's page** (`docs/programs.md` "Program page"). Settings only holds what's app-wide.

**Rejected:** a gear on Home (clutter mid-workout), a gear on every page, and opening Settings as a sheet.

### Settings screen

One plain list, no group headings:

| Row | Control | Default |
|---|---|---|
| Unit | lb \| kg switch in the row | Set in the walkthrough |
| Default jump | Scroll wheel, for the current unit only | 2.5 lb, or 1 kg |
| New day starts | Scroll wheel (the rollover time, `docs/data.md` "Rotation") | 12:00 AM |
| Timer buttons | Scroll wheel: 5, 10, 15, 30, or 60 sec (`docs/home.md` "Rest timer") | 15 sec |
| Appearance | Auto \| Light \| Dark switch in the row | Auto (match the phone) |
| Show tips again | Button: one tap brings every hint back | |
| Help | Opens the help page › | |
| Rate the app | Opens the App Store review page | |

- **Jump size is stored for lb and kg separately** (`docs/data.md` "Weight units"), but Settings only shows the one for the current unit. Switching to kg shows the kg jump.
- **"Show tips again" is a button, not a switch.** A switch would have no clear meaning once turned on.
- **Rows that open something show ›; Rate the app shows ↗,** since it leaves the app.
- **Wheel rows show their value in a grey pill,** which opens Home's scroll wheel (`docs/home.md` "Logging sets").

**Rejected:** three groups (Weights, Workout, App), and pop-up menus for every row (two taps for lb or kg).

### Help page

- **A short list of topics:** Schemes, Jumps, Rest timer, Supersets, and Rotation. Each opens a few lines on that topic.
- **For looking things up.** TipKit hints still point things out the first time (`docs/home.md` "Hints", `docs/programs.md` "Hints").

### First open

- **No separate welcome screen.** Home with no program shows "Create your first program" (`docs/programs.md` "Nothing empty").
- **lb or kg is the walkthrough's first step:** Step 1 of 5, "lb or kg?" with two big buttons, "You can change this in Settings" below, and "Next: name". It comes before naming the program. Every weight typed after it uses that unit. The steps are then unit → name → days → adding exercises → schemes.

## Mockups

**Canvas:** https://claude.ai/artifact/P5uocnirXmJ72uDcjekpkt (the same canvas as Home, Programs, and Tracker), page "Layout · timer bar". Light mode only, on Tracker's Bench page, mid-rest on Push day.

| Screen | Shows |
|---|---|
| A · solid | Solid Push-red timer bar, full tab bar (rejected) |
| A · solid, shrunk | The same, tab bar shrunk (rejected) |
| B · glass | Glass timer bar with red time and progress line, full tab bar (picked) |
| B · glass, shrunk | The same, with the bar beside the small Tracker button (picked) |

The tab bar on these screens is the agreed design. Under B, the second row is B in dark mode.

**Page "Settings · Liquid Glass":** grey, like Programs outside a day. Light on top, dark underneath.

| # | Screen | Shows |
|---|---|---|
| 1 | Gear on Programs | Program list with the gear beside +, and the real tab bar |
| 2 | Settings | The full list, with lb \| kg and Auto \| Light \| Dark switches and grey value pills |
| 3 | Help | The five topics |
| 4 | Help: Jumps | A topic page, with draft wording |
| 5 | Walkthrough: lb or kg | Step 1 of 5, lb picked, no tab bar |

## Still open

- The tab bar is still a placeholder on the Home, Programs, and Tracker mockups
- The help page's wording. The Jumps draft is too wordy: keep every topic short, plain, and to the point (`RULES.md` rule 5)
- The other help topics aren't written yet

## Where we left off

Behavior and visual style are finished. Matt is happy with the screens on the "Settings · Liquid Glass" and "Layout · timer bar" pages of the canvas. Read the canvas back with the Artifact tool before editing it.
