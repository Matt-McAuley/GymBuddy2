# GymBuddy 2

A rebuild of GymBuddy, Matt's personal gym companion app, in Swift for iPhone.

## Rules

Follow every rule in this file:

@RULES.md

## The idea

At the gym, Matt was juggling Google Sheets for the workout program, a timer app with annoying ads, a counter app for sets, and Spotify for music. GymBuddy puts the program, timer, and counter in one app. Matt uses it in real workouts, and the new version is meant for the App Store.

## Why we are rebuilding

GymBuddy 1.0 was built with Expo and React Native. Moving to Swift is meant to fix these things:

- **Live Activities were the main pain.** The lock screen and Dynamic Island timer had to be written in Swift anyway, then bridged into the React Native app. That one feature took a large chunk of development time.
- **React Native styling was annoying to work with.**
- **Matt wanted to learn iOS development.** That is no longer a goal (2026-11-20). What matters now is clean, well-structured code that follows Swift and Apple conventions.
- **Cross-platform support has no value here.** The app is iPhone-only.

## Goals

- Release on the App Store, flexible enough for people who train differently from Matt.
- Keep what makes the app useful: the program builder, the rest timer with Live Activity, and the set counter in one place.
- Add a Tracker page with workout history, plus streaks, PRs, graphs, and workout summaries, like Strava.
- No music. Spotify's limits block an App Store release (5 users in development mode, and wider access needs a registered business with 250k monthly users), its recommendation tools are closed to new apps, and YouTube Music has no official way in. Decided 2026-11-19.
- Rotate the workout day and scheme automatically, with manual override.
- **Rep numbers are jump goals, not a pass/fail plan.** Bench 5x5 is entered as 5, 5, 5, 5, 7, and hitting every number earns a weight jump (a PR, which is rare). Falling short is normal and expected, not a miss. Never design anything that treats a set below its number as a failure (ticks vs warnings, hit rates, "missed" labels). See `docs/data.md` "Jump rules".
- Fix the known problems in each section as we rebuild that section, rather than carrying them over. They are listed in `GYMBUDDY_1.md`.
- Build with Apple's own tools wherever they do the job, to avoid fighting third-party packages again.

## Pages

Home, Programs, and Tracker (new).

## Planning docs

Each doc gives a high-level overview of its area, then records the decisions and trade-offs made along the way. Read only the ones relevant to the current work.

- `docs/data.md`: how programs and history are stored, and how rotation works
- `docs/home.md`: the workout screen, including logging sets, the rest timer, and supersets
- `docs/programs.md`: building programs, schemes, days, sets, supersets, and the shared exercise list
- `docs/tracker.md`: workout history, streaks, PRs, graphs, and the workout summary

- `docs/layout.md`: the tab bar, the timer bar, where Settings lives, the Settings screen, help, and first open

## Where we are

Planning is finished. No app code exists yet. The data model is settled in `docs/data.md`. Home's behavior, layout (pass 1), and visual style (pass 2, Liquid Glass) are settled in `docs/home.md`, with mockups linked there. Programs' behavior and layout (pass 1, grey boxes) are settled in `docs/programs.md`, with mockups linked there. Programs' visual style (pass 2, Liquid Glass) is settled too: grey outside a day, the day's color inside one. Tracker's behavior, layout (pass 1, grey boxes), and visual style (pass 2, Liquid Glass) are settled in `docs/tracker.md`, with mockups linked there: grey, with day colors only as dots on the calendar and Log. The overall layout and Settings (behavior and Liquid Glass style) are settled in `docs/layout.md`, with mockups linked there: three tabs, a glass timer bar above the tab bar while resting, and a Settings gear on Programs. Pre-build questions were answered on 2026-11-20 and written into each doc, including iCloud sync rules in `docs/data.md` "iCloud sync". Next step: build.

## Building

Matt is vibe coding this and won't be around during the build.

- **The builder picks the build order** and decides small trade-offs without asking. Follow the docs. Where they're silent, make the sensible call and note it in the relevant doc.
- **No tests.**
- **Commit freely to a `build` branch** as work progresses, without asking (an exception to `RULES.md` rule 1, approved 2026-11-20). Never push, and never commit to main. Matt reviews the branch when he's back.
- **Code quality comes first:** idiomatic SwiftUI, Apple's frameworks, and standard naming and structure.
- **Target:** iOS 26 only, iPhone only, portrait, English. Same bundle ID as 1.0. Matt has a paid developer account.

## GymBuddy 1.0 reference

The old app's source code is in `../GymBuddy`. Read `GYMBUDDY_1.md` when working on anything that replaces or relates to a 1.0 feature. It covers the features, how the app was built, and its problems.
