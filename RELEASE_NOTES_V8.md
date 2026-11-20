# MADANY RUN 3D — V8 source update

This is a source-code update based on the supplied V7 project.

## Gameplay and reliability changes
- Corrected swept lane-change collision detection so the collision radius is measured from the nearest point on the runner's actual movement segment.
- Corrected the daily mission: the 250 m target and 15-coin target must both be achieved during the same run. A previous best score or saved wallet balance no longer incorrectly completes it.
- Adjusted coin-trail placement to favor a neighboring lane when an obstacle is spawned.
- Made the jump/slide HUD tap tolerance density independent.
- Added explicit OpenGL shader compile and program link checks with diagnostic error messages.
- Set explicit depth-buffer clear and comparison defaults.
- Fixed a missing color argument in one street-lamp draw call.
- Updated Android app metadata to version 1.5 / versionCode 5.

## Validation performed in this environment
- Compiled the platform-independent Kotlin gameplay mechanics with `kotlinc`.
- Ran all 10 `RunnerMechanicsTest` cases using a small local JUnit-compatible test harness: all passed.
- Ran a separate gameplay smoke test for lane collision, mission completion, and maximum speed: passed.
- Verified the project archive after packaging.

## Not verified here
- A full Android APK build and on-device OpenGL rendering test were not possible because this environment does not expose the Android SDK or Gradle executable. Install/build the project in AppMama to perform the device build and rendering check.

The runner, pursuer, dog, trains, city geometry, shop, skins, power-ups, score, and saved progression remain part of the original project. This update does not import Subway Surfers' copyrighted characters or assets.
