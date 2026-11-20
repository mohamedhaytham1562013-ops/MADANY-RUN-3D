# MADANY RUN 3D — V9 source update

This update is based on the V8 source archive.

## Gameplay and reliability changes
- Corrected daily mission distance accounting: the 250 m objective now uses actual forward travel from the current run, not the score total.
- Kept the 15-coin requirement independent, so both objectives must be reached in the same run.
- Clarified score presentation as PTS throughout the HUD, results screen, and best-score labels; coin and double-score bonuses are points, not meters.
- Added live distance/coin progress to the daily mission screen.
- Removed the fixed random seed so obstacle and reward layouts vary between launches.
- Improved audio shutdown sequencing to pause the AudioTrack and allow its worker thread to stop before releasing the track.
- Updated Android app metadata to version 1.6 / versionCode 6.

## Validation performed
- Compiled the platform-independent Kotlin gameplay mechanics with kotlinc.
- Ran all 12 RunnerMechanicsTest cases with a local JUnit-compatible harness: all passed.
- Verified the source archive after packaging.

## Not verified here
- A full Android APK build and on-device OpenGL rendering test were not possible because the Android SDK and Gradle executable are not installed in this environment. Build in AppMama and test on the target phone.
