# GymBuddy 2

A rebuild of GymBuddy, Matt's personal gym companion app, in Swift for iPhone.

## Rules

Follow every rule in this file:

@RULES.md

## The idea

At the gym, Matt was juggling Google Sheets for the workout program, a timer app with annoying ads, a counter app for sets, and Spotify for music. GymBuddy puts all of that in one app. It was built for Matt's personal use in real workouts.

## Why we are rebuilding

GymBuddy 1.0 was built with Expo and React Native. Moving to Swift is meant to fix these things:

- **Live Activities were the main pain.** The lock screen and Dynamic Island timer had to be written in Swift anyway, then bridged into the React Native app. That one feature took a large chunk of development time.
- **React Native styling was annoying to work with.**
- **Matt wants to learn iOS development.** This project is the way to learn it, so understanding the code matters as much as getting it working.
- **Cross-platform support has no value here.** The app is iPhone-only.

## Goals

- Keep what makes the app useful: the program builder, the rest timer with Live Activity, the set counter, and Spotify controls in one place.
- Fix the known problems in each section as we rebuild that section, rather than carrying them over. They are listed in `GYMBUDDY_1.md`.
- Build with Apple's own tools wherever they do the job, to avoid fighting third-party packages again.

## Still undecided

We will work through these together:

- Which features to keep, change, or add
- The look and design of the app
- How data is stored, and whether existing data on Matt's phone needs to move over
- Which section to build first

## GymBuddy 1.0 reference

The old app's source code is in `../GymBuddy`. Read `GYMBUDDY_1.md` when working on anything that replaces or relates to a 1.0 feature. It covers the features, how the app was built, and its problems.
