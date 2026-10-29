# TechNova Motors — iPhone app (build & App Store submission)

This folder has everything needed to build the TechNova Motors iPhone app and
publish it on the App Store. The app is already finished: it is a Capacitor 8
shell that opens the live web app (https://motors.technovaos.com), with native
push notifications, a Face ID lock, haptics, a splash screen and an offline page.
**No code changes are expected.** Your job is signing, testing on a real iPhone,
the App Store Connect listing, and the upload.

The same app is already live on Google Play (`com.technovaos.motors`).

```
app/                      the Xcode project (Capacitor)
  ios/App/App.xcodeproj   ← open this (after npm install)
  capacitor.config.json   app id, live URL, colours
  mobile/www/             the offline page bundled in the app
app-store/
  icon-1024.png           App Store icon (no alpha — already in the project too)
  screenshots/en, /ar     iPhone 6.9" screenshots, 1290×2796, in upload order
  listing.md              every App Store Connect field: texts (EN + AR),
                          keywords, URLs, App Privacy answers, review notes
```

## 1. Requirements

- A Mac with the latest Xcode (16 or newer) and Node.js 20+.
- Access to the owner's Apple Developer account (Admin or App Manager role) —
  the app is published under the owner's account, not yours.
- A real iPhone for testing (push notifications don't work in the simulator).

## 2. Open the project

```bash
cd app
npm install          # installs Capacitor and its plugins (Swift packages point into node_modules)
npx cap sync ios     # refreshes the native project from capacitor.config.json
npx cap open ios     # opens Xcode
```

Xcode resolves the Swift packages on first open (Capacitor 8 uses Swift Package
Manager — there is no CocoaPods / Podfile).

## 3. Signing

1. Apple Developer ▸ Identifiers: register the App ID **`com.technovaos.motors`**
   with the **Push Notifications** capability ticked. Don't change the bundle id.
2. Xcode ▸ target **App** ▸ Signing & Capabilities: choose the owner's Team,
   "Automatically manage signing". The **Push Notifications** capability is
   already in `App/App.entitlements`; Xcode switches it to production when you
   archive for the App Store.
3. Version **1.0.0**, build **1** are already set. Raise the build number for
   every new upload.

## 4. Push notifications key (send this to the owner — securely)

The server sends iPhone notifications straight to Apple, so it needs an APNs key:

1. Apple Developer ▸ Keys ▸ **+** ▸ tick **Apple Push Notifications service (APNs)**
   ▸ Continue ▸ Register ▸ **Download** the `.p8` file (Apple lets you download
   it only once).
2. Send the owner, privately (not in a group chat or email thread):
   - the `.p8` file
   - its **Key ID** (10 characters, shown next to the key)
   - the **Team ID** (Membership details)

The owner puts them on the server. Until then the app works, but iPhones don't
get notifications.

## 5. Test on a real iPhone (Debug run from Xcode)

Sign in with the review account (ask the owner) and check:

- [ ] Splash screen (dark navy, TechNova logo), then the sign-in page
- [ ] Status bar has white icons on dark navy; nothing hides under the notch or home bar
- [ ] Notification permission prompt appears after sign-in
- [ ] Menu ▸ "Face ID / fingerprint lock" is On; close the app for 3+ minutes,
      reopen → Face ID asks to unlock
- [ ] Camera opens (Attendance ▸ face check-in, or adding a car photo)
- [ ] Location prompt appears on attendance check-in
- [ ] Microphone works for a voice note in the inbox
- [ ] A PDF / Excel export (CRM ▸ Reports) opens or shows the share sheet
- [ ] Airplane mode → the offline page shows; back online → retry works
- [ ] No prices, plans or "buy" buttons appear anywhere in the app

If anything fails, send the owner a screen recording — fixes happen on the web
side and reach the app without a new build.

## 6. App Store Connect

Create the app: platform iOS, name **TechNova Motors**, primary language
**English (U.K.)**, bundle id `com.technovaos.motors`, SKU `technova-motors-ios`.
Then fill everything from **`app-store/listing.md`**:

- Texts for English and the Arabic localization, keywords, URLs
- Screenshots: `app-store/screenshots/en` into English, `/ar` into Arabic
  (6.9" display slot; Apple scales them for smaller iPhones)
- App Privacy answers, age rating 4+, category Business, price Free
- App Review Information: the review account and the notes text

## 7. Upload & submit

1. Xcode ▸ scheme **App**, device **Any iOS Device (arm64)** ▸ Product ▸ **Archive**
2. Organizer ▸ **Distribute App** ▸ App Store Connect ▸ Upload
3. Test the build through **TestFlight** on an iPhone once more
4. Attach the build to version 1.0.0 and **Submit for Review**

## 8. When you're done

Send back any file you had to change inside `app/ios/` (if any), and tell the
owner the Key ID / Team ID were delivered. Don't keep copies of the `.p8` key or
the review password.
