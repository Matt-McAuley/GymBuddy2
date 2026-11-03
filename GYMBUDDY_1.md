# GymBuddy 1.0 Notes

Notes on the old Expo and React Native app. Source code is in `../GymBuddy`, and all paths below are relative to that folder. Line numbers are as of commit `7ef4a23`.

The problems below come from reading the code. The app was not run, so anything marked "likely" still needs confirming.

## At a glance

- **Stack:** Expo 52, React Native 0.76, TypeScript, Zustand for app state, NativeWind for styling, and expo-sqlite for the database
- **Native code:** Swift for the Live Activity and timer notification, in `modules/activity-controller/` and `targets/widget/`
- **Size:** about 5,400 lines of code and 262 commits, starting November 2024
- **Distribution:** installed on Matt's phone through Xcode, not on the App Store
- **Bundle ID:** `com.mattmcauley.GymBuddy`
- **Screenshots:** `assets/images/readme/`

## Features

### Home tab

Screen: `app/(tabs)/index.tsx`

- **Day picker:** shows the current day's name in its color and opens a bottom sheet to switch days
- **Rest timer:** `components/Home/Timer.tsx`
  - Counts down from the rest time set on the current set, with play/pause and reset
  - Resets itself when the set or exercise changes (`:58`)
  - For a superset, uses the longer of the two rest times (`:19`) and labels itself with initials such as "LR & TE" (`:38`)
- **Live Activity:** lock screen and Dynamic Island countdown with pause, resume, and reset buttons that stay in sync with the app (`targets/widget/TimerWidgetLiveActivity.swift`)
- **Finish notification:** fires when the timer ends (`modules/activity-controller/ios/ActivityControllerModule.swift:113`)
- **Set counter:** up and down arrows, showing current set, total sets, and reps (`components/Home/Counter.tsx`)
- **Exercise card:** swipe between exercises. Shows name and weight, plus the previous and next exercises (`components/Home/exerciseDisplays/`)
- **Supersets:** show both exercises. With uneven set counts, the card is meant to switch to the remaining exercise once the shorter one runs out
- **Remembers your place:** set, exercise, and day survive an app restart (`store.tsx`)
- **Empty states:** "No Program Selected", "No Days in Program", and "No Exercises in Day" (`app/(tabs)/index.tsx:78`)

### Programs tab

Screens: `app/(tabs)/(programTabs)/`. Forms: `components/Programs/Forms/`.

- Three sub-tabs: Programs, Days, and Exercises
- **Exercise:** a name plus any number of sets, each with its own rest, weight, and reps. Sets can be dragged to reorder or swiped to delete
- **Day:** a name, a color, and an ordered list of exercises or supersets chosen from a bottom sheet
- **Program:** a name and an ordered list of days
- Tap the check mark to choose the active program, which is highlighted green
- Editing uses nearly the same screen as adding, with existing values filled in and a delete option
- Editing or deleting sends the Home tab back to the first day and exercise, to avoid pointing at something that no longer exists
- Errors and successes appear as toast messages

### Music tab

Screens: `app/(tabs)/music.tsx` and `components/Music/`. Uses the Spotify Web API.

- Log in and out with a Spotify account
- Now playing: song, artist, album art, and a seek slider
- Pause/play, next, previous, shuffle, and like
- Horizontal playlist picker
- Queue view, where tapping a song skips forward to it
- Shows "No music playing" when Spotify has no active device

## How data is stored

SQLite database called `programs.db`, set up in `db/dbFunctions.ts:4`.

| Table | Holds |
|---|---|
| `exercises` | Exercise names |
| `exercise_details` | One row per set: rest, weight, reps, position |
| `days` | Day name and color |
| `day_details` | Ordered exercises or supersets in a day |
| `programs` | Program names |
| `program_details` | Ordered days in a program |
| `current_program` | Which program is active |

- Queries live in `db/programDBFunctions.ts`, and `getProgram` builds the full program for the Home tab (`db/dbFunctions.ts:648`)
- Shapes of the data are in `types/programType.ts`
- Set, exercise, and day position are stored separately in AsyncStorage, a simple key-value store
- A seeded program in `addMockProgram` (`db/dbFunctions.ts:94`) looks like Matt's real training program

## Problems

Each of these should be fixed when we rebuild its section.

### Timer and Live Activity

- **Hardest part of the project.** Documentation had to be pieced together, and no direct tutorials existed.
- **Timer state lives in three places:** the screen (`components/Home/Timer.tsx:53`), the Swift module (`modules/activity-controller/ios/ActivityControllerModule.swift:14`), and the Live Activity itself. Each does its own calculation and they have to be kept in sync.
- **Roundabout messaging:** Live Activity buttons write to shared storage and send a system-wide signal, the Swift module picks that up, and then it messages React (`targets/widget/AppIntent.swift:27`, `modules/activity-controller/ios/ActivityControllerModule.swift:71`).
- **Likely missing setup:** the code uses the shared app group `group.com.mattmcauley.GymBuddy.share`, but the group is not declared in `app.json` or `targets/widget/generated.entitlements`.
- **Duplicate file:** `Attributes.swift` exists in both `modules/activity-controller/ios/` and `targets/widget/`.
- **Running timer is lost on restart.** The start time is only held in memory (`components/Home/Timer.tsx:53`).
- **Notification is scheduled a second early** on purpose (`modules/activity-controller/ios/ActivityControllerModule.swift:127`).
- **Requires iOS 18.** The Swift code does nothing on older versions (`modules/activity-controller/ios/ActivityControllerModule.swift:32`).

### Supersets

- **Likely off-by-one bug with uneven set counts.** The check uses `>` where it needs `>=`. With 4 sets of squats and 3 of curls, set 4 matches no case, and the card likely shows curls rather than squats. The same logic is copied into three files: `components/Home/exerciseDisplays/SuperSet.tsx:13`, `components/Home/Timer.tsx:12`, and `components/Home/Counter.tsx:17`.
- **Wrong reps past the shorter exercise.** Reps fall back to each exercise's first set rather than the current one (`components/Home/Counter.tsx:26`).
- **Supersets are stored in forms as comma-joined text,** so an exercise name containing a comma would break (`db/programDBFunctions.ts:101`).

### Database

- **Delete rules are likely off.** The setting that turns them on sits in `dbSetup` (`db/dbFunctions.ts:7`), which only runs from `resetApp`, and that call is commented out (`app/(tabs)/index.tsx:52`). This likely explains commit `aca4625`, "cascade not functioning properly".
- **Deleting probably leaves stray data.** `deleteExercise`, `deleteDay`, and `deleteProgram` only remove the main row (`db/programDBFunctions.ts:78`, `:127`, `:172`).
- **A fresh install likely has no tables,** for the same reason `dbSetup` never runs.
- **Edits are risky.** Saving deletes rows and adds them back with no transaction, so a crash midway could lose data (`db/programDBFunctions.ts:139`, `:198`).
- **Items are linked by name** rather than by a permanent ID, which is why renaming takes so much rewriting.
- **Whole pounds only.** Weights are stored as integers and read with `parseInt`, so 2.5 lb jumps are not possible (`components/Programs/Forms/AddExercise.tsx:62`).

### Workout tracking

- **No history.** Nothing records what was actually lifted. Weights only change by editing the exercise.
- **Workout position needs a workaround to restore.** Changing exercise resets the set to 0 (`components/Home/Counter.tsx:39`), so a saved set has to be re-applied afterwards (`components/Home/Counter.tsx:46`).

### Spotify

- **Pause is faked.** A real pause disconnects the device, and restarting means opening the Spotify app. So "pause" mutes the phone and keeps seeking back to the same spot (`components/Music/MusicControl.tsx:15`, `:112`). Worth checking whether Spotify's iOS SDK solves this.
- **Secret key ships inside the app.** Login also skips PKCE, a method that needs no secret (`components/Music/Login.tsx:17`, `:28`).
- **Login tokens are stored unencrypted** in AsyncStorage (`components/Music/Login.tsx:56`).
- **Liked status can be wrong.** It only checks one page of liked songs, so older likes likely show as not liked (`components/Music/MusicControl.tsx:99`).
- **Too many requests.** Several loops run every second, and each player update also triggers a liked-songs check, which risks rate limits (`app/(tabs)/music.tsx:47`, `components/Music/MusicPage.tsx:14`, `components/Music/MusicControl.tsx:36`, `components/Music/Queue.tsx:33`).
- **Needs an active device.** Nothing can be controlled until Spotify is already playing somewhere (`components/Music/MusicPage.tsx:64`).
- **Errors are invisible.** Failures only go to the console, so nothing appears on screen.

### Code and upkeep

- **React Native styling was annoying to work with,** and sizes are often hard-coded.
- **Six near-duplicate Add and Edit forms,** about 1,850 lines (`components/Programs/Forms/`).
- **No tests,** though Jest is configured.
- **At least 8 packages are not imported by the app's code:** `native-notify`, `expo-notifications`, `react-native-background-timer`, `react-native-draglist`, `react-native-element-dropdown`, `react-native-tab-view`, `react-native-progress`, and `react-native-webview`.
- **Deprecated swipe component** (`components/Programs/Forms/AddExercise.tsx:7`).
- **Screen-specific scroll math** using a hard-coded 195 (`app/(tabs)/index.tsx:63`).
- **Developer database tool is left in the Home screen** (`app/(tabs)/index.tsx:20`).

## Lessons for the rebuild

- Keep one source of truth for the timer, and have the screen and Live Activity read from it.
- Store the timer's end time rather than counting down second by second, so it survives restarts and backgrounding.
- Write superset logic once, in one place, with tests for uneven set counts.
- Give items permanent IDs so renaming is simple, and make saves all-or-nothing.
- Use PKCE for Spotify login and Apple's Keychain for tokens.
- Check for changes less often, and show errors on screen when something fails.
