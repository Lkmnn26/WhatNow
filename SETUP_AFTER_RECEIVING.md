# Setup After Receiving the Project (ZIP)

Use this guide **after you have received and extracted** the compressed project folder. The project was shared after `flutter clean`, so dependencies and build cache are not included.

---

## Prerequisites

- **Flutter SDK** ([flutter.dev](https://flutter.dev)) — installed and on your `PATH`
- **Android Studio** or **VS Code** (optional but helpful for running on device/emulator)
- **Android device or emulator** — for running the app (primary target is Android)
- **Google API key** — for Maps and Places (see [Step 4](#step-4-create-env-file))

Check Flutter:

```bash
flutter doctor
```

Fix any issues it reports (e.g. Android licenses: `flutter doctor --android-licenses`).

---

## Step 1. Extract the ZIP

- Unzip the file to a folder, e.g. `C:\Projects\whatnow` or `~/projects/whatnow`
- The project root must contain: `pubspec.yaml`, `lib/`, `android/`, `functions/`, etc.

---

## Step 2. Open a Terminal in the Project Root

- **Windows:** In File Explorer, open the project folder, then in the address bar type `cmd` and press Enter  
  Or: Right‑click the folder → “Open in Terminal” (if available)  
  Or: Open Terminal / PowerShell and `cd` to the project path.

- **macOS / Linux:** `cd /path/to/whatnow`

---

## Step 3. Get Dependencies

Because the project was cleaned before sharing, run:

```bash
flutter pub get
```

This restores packages (Firebase, Google Maps, etc.) into `.dart_tool/` and resolves `pubspec.yaml`.

---

## Step 4. Create `.env` File

The app needs a `.env` file in the **project root** (same level as `pubspec.yaml`).

1. Check if `.env` already exists in the project root.  
   - If **yes** and it already has `GOOGLE_API=...`, you can skip to [Step 5](#step-5-firebase-and-android-config).

2. If **no**, create a new file named **`.env`** in the project root with:

   ```env
   GOOGLE_API=YOUR_GOOGLE_MAPS_API_KEY
   ```

   Replace `YOUR_GOOGLE_MAPS_API_KEY` with a valid key that has **Maps SDK for Android** and **Places API** (and **Geocoding API** if used) enabled.

- **If the person who sent the project shared a key:** use that (respect their usage/quote rules).  
- **If you need your own:**  
  1. Go to [Google Cloud Console](https://console.cloud.google.com)  
  2. Create or select a project  
  3. Enable: **Maps SDK for Android**, **Places API**, **Geocoding API**  
  4. **APIs & Services → Credentials → Create credentials → API key**  
  5. Copy the key into `.env` as above.

---

## Step 5. Firebase and Android Config

The project uses **Firebase** (Auth, Firestore, Cloud Functions). The ZIP may already include:

- `lib/firebase_options.dart`
- `android/app/google-services.json`

If those files **are present** and point to the **original author’s Firebase project**, the app will work as long as that project is still active and the backend (e.g. Cloud Functions) is deployed. You do **not** need to change anything for Firebase in that case.

If you want to use **your own** Firebase project:

1. Install Firebase CLI:  
   [firebase.google.com/docs/cli](https://firebase.google.com/docs/cli)

2. Log in and connect the app:

   ```bash
   firebase login
   flutterfire configure
   ```

   Follow the prompts to select/create a project and generate `firebase_options.dart` and `google-services.json`.  
   Then deploy Cloud Functions so the greeting and weather features work:

   ```bash
   firebase deploy --only functions
   ```

---

## Step 6. Run the App

1. Connect an **Android phone** (USB debugging on) or start an **Android emulator**.

2. From the project root:

   ```bash
   flutter run
   ```

   Flutter will pick an available device. To choose one:

   ```bash
   flutter devices
   flutter run -d <device_id>
   ```

---

## Step 7. (Optional) Build an APK to Install on a Phone

To create an APK you can copy to a phone and install:

```bash
flutter build apk
```

The APK will be at:

```
build/app/outputs/flutter-apk/app-release.apk
```

Copy `app-release.apk` to the phone and open it to install. The user may need to allow “Install from unknown sources” for the app used to open the APK.

---

## Troubleshooting

| Problem | What to try |
|--------|-------------|
| `flutter: command not found` | Install Flutter and add it to your `PATH`. Run `flutter doctor`. |
| `No .env file` or crash on startup | Create `.env` in the project root with `GOOGLE_API=...` (see [Step 4](#step-4-create-env-file)). |
| `flutter pub get` fails | Run `flutter clean`, then `flutter pub get` again. Check your internet connection. |
| `Waiting for another flutter command to release the startup lock` | Close other Flutter/IDE processes or delete `bin/cache/lockfile` in your Flutter SDK and try again. |
| Google Sign-In / Maps / Places errors | Ensure `GOOGLE_API` in `.env` is correct and the needed APIs are enabled in Google Cloud. |
| Firebase / Cloud Function errors | If you use the original project’s config, ensure that Firebase project and its Functions are still deployed. Otherwise, set up your own project and run `firebase deploy --only functions`. |
| `*_variant in compileSdkVersion` / Gradle errors | In the project root: `cd android` then `./gradlew clean` (or `gradlew.bat clean` on Windows). Go back to project root and run `flutter run` again. |
| App builds but crashes on device | Check that `google-services.json` and `firebase_options.dart` match the Firebase project that actually has Auth, Firestore, and Functions set up and deployed. |

---

## Quick Checklist

- [ ] Extracted the ZIP to a folder  
- [ ] Opened a terminal in the **project root**  
- [ ] Ran `flutter pub get`  
- [ ] Created `.env` with `GOOGLE_API=...` (if missing)  
- [ ] Confirmed `lib/firebase_options.dart` and `android/app/google-services.json` exist (or ran `flutterfire configure`)  
- [ ] Device or emulator is connected and listed by `flutter devices`  
- [ ] Ran `flutter run` (or `flutter build apk` for an APK)

---

## Need More Detail?

- Main project overview and design: **README.md**  
- Flutter: [docs.flutter.dev](https://docs.flutter.dev)  
- Firebase: [firebase.google.com/docs](https://firebase.google.com/docs)
