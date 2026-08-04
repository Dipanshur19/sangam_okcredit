# Sangam — Sab ka ek hisaab

**A full-stack, unified kirana (shop) ledger app.** Sangam is an OkCredit-style
credit/khata tracker for small shop owners, built with Flutter and Firebase —
track customer dues, capture UPI payments automatically, manage staff, and
run the whole shop's accounting from one app.

---

## Features

- **Customer ledger** — add customers, record transactions, track who owes
  what and who's paid, with a running balance per customer.
- **Automatic UPI payment capture** — an Android notification-listener
  service reads incoming GPay / PhonePe / Paytm payment notifications and
  queues them for review. Nothing gets matched to a customer automatically —
  you confirm every match yourself.
- **Billing** — build bills from a product template, add line items and
  charges, and share them with customers.
- **Stock tracking** for shop inventory.
- **Multi-user shops** — an owner account plus staff accounts with an
  invite-code onboarding flow (no server component required). Owners can
  make a staff member view-only, restore edit access, reset their password,
  or remove them entirely.
- **Reports & dashboard** with charts for a quick view of shop performance.
- **Auto payment reminders** for outstanding dues.
- **Biometric app lock** for privacy on shared devices.
- **English / Hindi** language toggle.
- **Cloud sync** via Firebase Firestore, with local secure storage for
  offline-friendly use.

## Tech stack

| Layer | Tools |
|---|---|
| App | Flutter (Dart ≥ 3.3), [Riverpod](https://riverpod.dev/) for state, [go_router](https://pub.dev/packages/go_router) for navigation |
| Backend | Firebase Authentication (email/password), Cloud Firestore, Cloud Functions |
| Android native | Kotlin `NotificationListenerService` + Flutter `EventChannel` for UPI payment capture |
| Charts | [fl_chart](https://pub.dev/packages/fl_chart) |
| Local storage | `flutter_secure_storage`, `shared_preferences` |

Cloud Functions currently deployed (`functions/index.js`):
`claimShop`, `createStaffAccount`, `updateStaffAccess`, `removeStaffAccount`,
`resetStaffPassword` — all staff/team-management operations.

## Project structure

```
lib/
├── core/
├── domain/
│   ├── entities/
│   └── usecases/
├── models/
├── presentation/
│   ├── providers/
│   ├── screens/
│   │   ├── account/  add_transaction/  auth/  auto_reminder/  bill/
│   │   ├── customers/  dashboard/  help/  multi_device/  notifications/
│   │   ├── onboarding/  paste_sms/  profile/  report/  settings/
│   │   ├── sms_queue/  splash/  staff/  stock/  store_details/  store_setup/
│   └── widgets/
├── providers/
├── services/            # auth, cloud sync, notifications, UPI capture
└── widgets/

android/                 # native project, incl. UpiNotificationListenerService
functions/               # Firebase Cloud Functions (staff management)
assets/                  # images, icons, onboarding illustrations
web/                     # Flutter web target
test/                    # widget tests
tool/                    # icon/mipmap generation scripts
```

## Getting started

### Prerequisites

- [Flutter SDK](https://docs.flutter.dev/get-started/install) ≥ 3.3.0
- A [Firebase](https://console.firebase.google.com/) project with
  **Authentication → Email/Password** and **Firestore** enabled
- Android Studio (for the Android build) and/or Xcode (for iOS)

### 1. Clone

```bash
git clone https://github.com/Dipanshur19/sangam_okcredit.git
cd sangam_okcredit
```

### 2. Install dependencies

```bash
flutter pub get
```

### 3. Configure Firebase

- Download your project's `google-services.json` from the Firebase console
  and place it at `android/app/google-services.json`. This file is
  intentionally **gitignored** — never commit it.
- Update `.firebaserc` with your own Firebase project ID.
- Deploy the Firestore security rules:

  ```bash
  firebase deploy --only firestore:rules
  ```

- (Optional) Deploy the Cloud Functions used for staff management:

  ```bash
  cd functions
  npm install
  firebase deploy --only functions
  ```

### 4. Run

```bash
flutter run
```

### 5. Build a release APK

```bash
./build_apk.sh      # macOS/Linux
build_apk.bat        # Windows
```

Both scripts pass the Firebase Android client configuration via
`--dart-define` at build time, so the app only initializes Firebase when
those keys are present.

## Enabling automatic UPI capture

After installing, go to **Settings → Notification Access** and turn Sangam
on. This lets the app read incoming payment notifications from UPI apps
(GPay, PhonePe, Paytm) and add them to a review queue — you decide which
customer each payment belongs to before it's recorded.

## Privacy

See [`PRIVACY_POLICY.md`](./PRIVACY_POLICY.md) for details on what data the
app reads (notification content for UPI capture) and how it's handled.

## License

No license has been added yet. Add one (MIT, Apache-2.0, etc.) before
accepting outside contributions or distributing the app publicly.
