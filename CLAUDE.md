<!-- appmama:managed -->
# Workspace

This directory is the workspace for a single Android app, built and
installed on the phone it runs on.

## Build environment
- JDK 21, Gradle 9.7, `aapt2` and `apksigner` are already on PATH.
- An Android SDK is installed at `$ANDROID_HOME` (already set): the
  API 36 platform and build-tools 36.0.0. Use `compileSdk = 36`,
  `targetSdk = 36` and the Android Gradle Plugin 9.4.1. Don't download,
  install or point `sdk.dir` at another SDK, and there is no
  `sdkmanager`; if an existing project asks for another platform,
  move it to 36 instead.
- `~/.gradle/gradle.properties` already sets `android.aapt2FromMavenOverride`
  and the Gradle daemon's memory, and wins over the project's own
  `gradle.properties`: don't set `org.gradle.jvmargs` or other daemon
  options. If a build really needs different ones, pass them on the
  command line for that build instead (`--no-configuration-cache`,
  `-Dorg.gradle.jvmargs=...`); edits to either file are overwritten.
- Build a debug APK with plain `gradle assembleDebug`. No Gradle
  wrapper (`./gradlew` downloads a second Gradle), no `clean`,
  `--no-daemon`, `--rerun-tasks` or `--refresh-dependencies`, and no
  `gradle build`, `check` or `lint` (lint downloads its own tools and
  runs for minutes). A Gradle daemon may already be running from an
  earlier build; it is reused, which is what makes builds fast.
- AGP 9 compiles Kotlin itself: don't apply `org.jetbrains.kotlin.android`
  (`kotlin("android")`). For Jetpack Compose, apply
  `org.jetbrains.kotlin.plugin.compose` version 2.2.10, the Kotlin
  version AGP 9.4.1 uses.
- AppMama prepares AGP 9.4.1 and the Kotlin standard library in the
  background, so a project that uses only those (with `minSdk = 26`,
  the level they are prepared for) usually builds without downloading
  anything. Every library beyond them — AndroidX, Compose, Material —
  is downloaded, dexed and compiled on the phone on first use, which
  takes minutes on a slow connection. Use the Android framework unless
  the app really needs a library, and `minSdk = 26` unless it needs a
  higher one.
- This compiles on a phone, not a laptop — keep the project minimal and
  buildable.

## How to respond
- If the user asks you to create or change an app, do it here, in this
  directory.
- If the user asks a question, or is just talking, answer them. Do not
  scaffold a project, do not create files, and do not run a build.
- An empty directory is not a request to fill it.
- After changing the app, build it. A change is finished when
  `gradle assembleDebug` succeeds.

## Name, package and icon
- Set `app_name` to the app's real name.
- Give a new app an `applicationId` that no other project next to this
  one uses (check `../*/app/build.gradle*`): two apps with one id
  install over each other. Keep it once it is set.
- Draw the app its own launcher icon as an adaptive icon (a vector
  foreground with the motif inside the central 66 of 108 dp, a
  background colour, and a monochrome layer) that shows what this app
  is, in the app's own style. An icon that came from a sample, another
  project or Android's default is a placeholder: replace it. Keep the
  icon when changing the app unless the user asks for a new one.

## Your final message
The person reading it is usually not a developer. AppMama shows your
last message as the summary of what you did; when a build succeeded it
sits under an Install button, and everything you said before it is
folded away behind a "Thought for …" line.
- Lead with what the app now does, or what changed, in plain words:
  two to five short bullets, or a short paragraph.
- Leave out file names, paths, package names, Gradle, SDK and library
  versions, and how the build environment was set up — unless the user
  asked a technical question.
- Don't link to the APK or say where it is. AppMama finds it and offers
  to install it.
- If something could not be done, say so plainly, and what you suggest.

## Skills
- Skills for particular kinds of app live in `/data/user/0/dev.appmama/files/home/.agents/skills`
  (one folder each, with a `SKILL.md`). Use the one that fits the request.
- If the user wants a game, read
  `/data/user/0/dev.appmama/files/home/.agents/skills/android-game/SKILL.md` before
  writing any code, and follow it.
- For any other app that needs a home-screen widget, reminders or
  notifications, photos or the camera, export/import or sharing,
  saving data, or speech, read
  `/data/user/0/dev.appmama/files/home/.agents/skills/android-app-features/SKILL.md`
  and use its tested recipes.
- If the app should use AI in any way (chat, an assistant, summarise,
  suggest, sort or tag things, answer questions), read
  `/data/user/0/dev.appmama/files/home/.agents/skills/android-ai/SKILL.md` and copy its
  `ai/` kit (the backend only); never add an AI SDK. Design the AI
  screens for this app; never copy the kit sample's screens. The app
  talks to the AI provider itself:
  the user's own ChatGPT plan first, then OpenRouter's free models,
  then an API key the user picks in the app's AI settings screen.
  Never put an API key in the app, and never route its AI through
  AppMama.

## Crash reports
Every app built here reports its crashes back to AppMama, so the user
can get them fixed with one tap. When you create an app (or find one
without it), install this handler first thing in the `Application`
subclass's `onCreate` — create one and register it in the manifest if
there is none. If the app already has a crash handler that doesn't send
`dev.appmama.extra.SENDER`, replace it with this one. Translate it to Java if the
project is Java.

```kotlin
val previousHandler = Thread.getDefaultUncaughtExceptionHandler()
Thread.setDefaultUncaughtExceptionHandler { thread, error ->
    try {
        // Proves to AppMama which app sent the report.
        val sender = android.app.PendingIntent.getBroadcast(
            this, 0,
            android.content.Intent("dev.appmama.action.REPORT_CRASH").setPackage(packageName),
            android.app.PendingIntent.FLAG_IMMUTABLE,
        )
        startActivity(
            android.content.Intent("dev.appmama.action.REPORT_CRASH")
                .setPackage("dev.appmama")
                .putExtra("dev.appmama.extra.PACKAGE", packageName)
                .putExtra("dev.appmama.extra.SENDER", sender)
                .putExtra("dev.appmama.extra.TRACE", android.util.Log.getStackTraceString(error).take(20000))
                .addFlags(android.content.Intent.FLAG_ACTIVITY_NEW_TASK)
        )
    } catch (_: Throwable) {
    }
    // The system's handler logs the crash and ends the process.
    if (previousHandler != null) {
        previousHandler.uncaughtException(thread, error)
    } else {
        android.os.Process.killProcess(android.os.Process.myPid())
        kotlin.system.exitProcess(10)
    }
}
```

Also add `<queries><package android:name="dev.appmama" /></queries>`
to the manifest. Keep the handler when you change the app.

## Open from AppMama
Give the app's launcher activity this second intent filter, next to its
MAIN/LAUNCHER one, so AppMama can tell the app is installed and open it
(Android hides installed apps from AppMama otherwise). Add it to any app
that lacks it, and keep it.

```xml
<intent-filter>
    <action android:name="dev.appmama.action.OPEN_APP" />
    <category android:name="android.intent.category.DEFAULT" />
</intent-filter>
```

## Shake to send feedback
Every app built here also lets the user shake the phone to send AppMama
a screenshot of what they're looking at, so they can say what to change
without describing the screen. Install this in the same `onCreate`,
right after the crash handler (same rules: create it if missing, keep
it, translate it for Java). It only runs on a phone that has AppMama,
and only while one of the app's screens is showing. Leave it out only
if the app itself reacts to shaking the phone (a "shake to roll" dice
app, say).

```kotlin
val app: android.app.Application = this
val appMamaInstalled = try {
    packageManager.getPackageInfo("dev.appmama", 0)
    true
} catch (_: Exception) {
    false
}
if (appMamaInstalled) registerActivityLifecycleCallbacks(object : android.app.Application.ActivityLifecycleCallbacks {
    private val sensors = app.getSystemService(android.hardware.SensorManager::class.java)
    private var listener: android.hardware.SensorEventListener? = null

    override fun onActivityResumed(activity: android.app.Activity) {
        val accelerometer = sensors?.getDefaultSensor(android.hardware.Sensor.TYPE_ACCELEROMETER) ?: return
        var jolts = 0
        var firstJolt = 0L
        var lastJolt = 0L
        listener = object : android.hardware.SensorEventListener {
            override fun onSensorChanged(event: android.hardware.SensorEvent) {
                val (x, y, z) = event.values
                // About 2.7 g: a deliberate shake, not walking or tilting.
                if (x * x + y * y + z * z < 26f * 26f) return
                val now = android.os.SystemClock.elapsedRealtime()
                if (now - lastJolt < 200) return
                if (now - firstJolt > 1500) { jolts = 0; firstJolt = now }
                lastJolt = now
                if (++jolts == 3) sendScreenshot(activity)
            }
            override fun onAccuracyChanged(sensor: android.hardware.Sensor?, accuracy: Int) {}
        }
        sensors.registerListener(listener, accelerometer, android.hardware.SensorManager.SENSOR_DELAY_UI)
    }

    override fun onActivityPaused(activity: android.app.Activity) {
        listener?.let { sensors?.unregisterListener(it) }
        listener = null
    }

    private fun sendScreenshot(activity: android.app.Activity) {
        val view = activity.window.decorView
        if (view.width == 0 || view.height == 0) return
        val shot = android.graphics.Bitmap.createBitmap(view.width, view.height, android.graphics.Bitmap.Config.ARGB_8888)
        // PixelCopy sees what is on screen, SurfaceView games included.
        android.view.PixelCopy.request(activity.window, shot, { result ->
            if (result != android.view.PixelCopy.SUCCESS) return@request
            val scale = minOf(1f, 1080f / maxOf(shot.width, shot.height))
            val small = android.graphics.Bitmap.createScaledBitmap(
                shot, (shot.width * scale).toInt().coerceAtLeast(1), (shot.height * scale).toInt().coerceAtLeast(1), true,
            )
            val jpeg = java.io.ByteArrayOutputStream()
                .also { small.compress(android.graphics.Bitmap.CompressFormat.JPEG, 75, it) }
                .toByteArray()
            try {
                // Proves to AppMama which app sent the screenshot.
                val sender = android.app.PendingIntent.getBroadcast(
                    app, 1,
                    android.content.Intent("dev.appmama.action.SEND_FEEDBACK").setPackage(app.packageName),
                    android.app.PendingIntent.FLAG_IMMUTABLE,
                )
                activity.startActivity(
                    android.content.Intent("dev.appmama.action.SEND_FEEDBACK")
                        .setPackage("dev.appmama")
                        .putExtra("dev.appmama.extra.PACKAGE", app.packageName)
                        .putExtra("dev.appmama.extra.SENDER", sender)
                        .putExtra("dev.appmama.extra.SCREENSHOT", jpeg)
                )
            } catch (_: Throwable) {
            }
        }, android.os.Handler(android.os.Looper.getMainLooper()))
    }

    override fun onActivityCreated(activity: android.app.Activity, savedInstanceState: android.os.Bundle?) {}
    override fun onActivityStarted(activity: android.app.Activity) {}
    override fun onActivityStopped(activity: android.app.Activity) {}
    override fun onActivitySaveInstanceState(activity: android.app.Activity, outState: android.os.Bundle) {}
    override fun onActivityDestroyed(activity: android.app.Activity) {}
})
```

When you tell the user about a new app, mention once that they can
shake it to send a screenshot back to AppMama.

## Attachments
The user may attach screenshots, sketches or files (PDF, Excel, Word,
text, Markdown, CSV, JSON). They are saved under
`.appmama/attachments/` and named in the message — open them to see
them. For a PDF, spreadsheet or Word file, AppMama has already saved
its text beside it as `<name>.txt` (a spreadsheet as CSV per sheet);
read that rather than the original. Files from earlier messages stay
there, so you can go back to them later. Don't copy attachments into
the app unless the user asks, e.g. "use this price list as the data".

## Versions
AppMama commits this directory to a local git repository after every
successful build, so the user can go back to an earlier version. Don't
commit, rewrite or delete that history yourself.
