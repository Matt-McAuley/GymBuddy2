# GymBuddy 2

A rebuild of GymBuddy, Matt's personal gym companion app, in Swift for iPhone.

## Rules

Follow every rule in this file:

@RULES.md

## The idea

At the gym, Matt was juggling Google Sheets for the workout program, a timer app with annoying ads, a counter app for sets, and Spotify for music. GymBuddy puts all of that in one app. Matt uses it in real workouts, and the new version is meant for the App Store.

## Why we are rebuilding

GymBuddy 1.0 was built with Expo and React Native. Moving to Swift is meant to fix these things:

- **Live Activities were the main pain.** The lock screen and Dynamic Island timer had to be written in Swift anyway, then bridged into the React Native app. That one feature took a large chunk of development time.
- **React Native styling was annoying to work with.**
- **Matt wants to learn iOS development.** This project is the way to learn it, so understanding the code matters as much as getting it working.
- **Cross-platform support has no value here.** The app is iPhone-only.

## Goals

- Release on the App Store, flexible enough for people who train differently from Matt.
- Keep what makes the app useful: the program builder, the rest timer with Live Activity, the set counter, and Spotify controls in one place.
- Add a Tracker page with workout history.
- Rotate the workout day and scheme automatically, with manual override.
- Fix the known problems in each section as we rebuild that section, rather than carrying them over. They are listed in `GYMBUDDY_1.md`.
- Build with Apple's own tools wherever they do the job, to avoid fighting third-party packages again.

## Pages

Home, Programs, Music, and Tracker (new).

## Planning docs

Each doc gives a high-level overview of its area, then records the decisions and trade-offs made along the way. Read only the ones relevant to the current work.

- `docs/data.md`: how programs and history are stored, and how rotation works
- `docs/home.md`: the workout screen, including logging sets, the rest timer, and supersets

Planned: `docs/programs.md`, `docs/music.md`, `docs/tracker.md`, and a doc for the overall layout and Settings.

## Where we are

Planning only. No app code exists yet. The data model is settled in `docs/data.md`. Home's behavior and layout (pass 1) are settled in `docs/home.md`, with mockups linked there. Next step: Home's visual style (pass 2), as planned at the end of `docs/home.md`.

## Still undecided

- Which features to keep, change, or add on each page
- The look and design of the app. Direction so far: light and dark mode, modern, high-tech, smooth, and quick to use
- The overall layout: the tab bar, where Settings lives, and whether the timer appears on every page
- How the Settings screen is designed
- Which section to build first
- Whether Spotify's developer limits allow a public App Store release. This has not been verified and should be checked when planning Music

## GymBuddy 1.0 reference

The old app's source code is in `../GymBuddy`. Read `GYMBUDDY_1.md` when working on anything that replaces or relates to a 1.0 feature. It covers the features, how the app was built, and its problems.
