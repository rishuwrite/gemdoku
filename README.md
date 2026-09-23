# Gemdoku — Android build via GitHub Actions

This repo wraps the `www/index.html` game (plain HTML/JS, no build step) in a
[Capacitor](https://capacitorjs.com/) shell so it can be compiled into a real
Android `.apk`. The actual compiling happens on GitHub's servers via Actions —
you don't need Android Studio installed locally.

## One-time setup

1. Create a new **empty** GitHub repo and push these files to it:
   ```bash
   git init
   git add .
   git commit -m "Gemdoku: Capacitor + Actions APK build"
   git branch -M main
   git remote add origin https://github.com/<you>/<repo>.git
   git push -u origin main
   ```
2. Open `capacitor.config.json` and change `"appId"` from
   `com.yourname.gemdoku` to your own reverse-domain id (e.g.
   `com.janedoe.gemdoku`) — this is Android's unique package name and you
   can't easily change it after you've published anywhere.
3. That's it. Pushing to `main` triggers the workflow in
   `.github/workflows/build-apk.yml` automatically.

## Getting the APK

1. Go to your repo's **Actions** tab → the latest **Build Gemdoku APK** run.
2. Scroll to **Artifacts** → download `gemdoku-debug-apk` → unzip it to get
   `app-debug.apk`.
3. Copy that file to an Android phone (or `adb install app-debug.apk`) and
   install it. You'll need to allow "install unknown apps" for whichever app
   you copied it with, since this isn't from the Play Store.

This is a **debug-signed** build, fine for testing on your own device. For a
Play Store release you'd generate a real signing keystore and switch the
workflow's `assembleDebug` to `assembleRelease` with the keystore stored in
repo Secrets — ask me when you're ready for that step and I'll add it.

## Making a tagged release build

Pushing a tag like `v1.0.0` also attaches the built APK directly to a GitHub
Release, so anyone can grab it from the repo's **Releases** page instead of
digging through Actions:

```bash
git tag v1.0.0
git push origin v1.0.0
```

## Editing the game

All game logic and styling lives in the single file `www/index.html` — grid
generation, the three hint types, mistake tracking, and level progression.
Edit that file, commit, push, and the next Actions run rebuilds the APK
automatically. There's no separate "build" step for the web content itself
since it's plain HTML/CSS/JS.

## Growing the difficulty over time

Difficulty already scales inside the game itself (`sizeForLevel()` in
`www/index.html` grows the grid from 8×8 toward 15×15 as the player's level
increases, saved locally on-device). If you want difficulty to also change
*between app versions* (e.g. raise the level cap, add new hint types, retune
region sizes), just edit `www/index.html`, bump
`"version"` in `package.json`, tag a new release, and push.
