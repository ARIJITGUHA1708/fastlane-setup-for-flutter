# 🚀 Fastlane CI/CD Setup for Flutter iOS App

This project demonstrates how to automate **Continuous Integration (CI)** and **Continuous Deployment (CD)** for a **Flutter iOS** app using [Fastlane](https://fastlane.tools/).

Fastlane helps streamline the process of building, signing, and deploying your app to **TestFlight** with just one or two commands from the terminal — no need to manually open Xcode or manage certificates.

---

## 🛠️ Setup Instructions

### Step 1: Create a Flutter Project

```bash
flutter create --org com.yourcompany ci_cd_demo
cd ci_cd_demo
```

Replace `com.yourcompany` with your actual package name.

---

### Step 2: Remove Unused Platforms (Optional)

If you're targeting only iOS, remove other platform folders:

```bash
rm -rf web android linux macos windows
```

---

### Step 3: Install Fastlane

Install via Homebrew:

```bash
brew install fastlane
```

Check installation:

```bash
fastlane --version
```

---

### Step 4: Initialize Fastlane for iOS

Navigate to the `ios/` directory:

```bash
cd ios
fastlane init
```

- Choose: `2. Automate beta distribution to TestFlight`
- Enter your Apple Developer credentials
- Fastlane will:
  - Create App Identifier (if not existing)
  - Register provisioning profiles
  - Set up App Store Connect project

---

### Step 5: Configure Fastlane Files

Fastlane creates a `fastlane/` folder with two key files: `Appfile` and `Fastfile`.

#### `Appfile`

```ruby
app_identifier("com.fourbrains.ciCdDemo2") # Replace with your bundle ID
apple_id("your_email@example.com")         # Apple developer email
team_id("XXXXXXXXXX")                      # Developer portal team ID
itc_team_id("YYYYYYYYYY")                  # App Store Connect team ID

ENV["FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD"] = "your-app-specific-password"
```

> 💡 You can generate an [App-Specific Password](https://support.apple.com/en-in/HT204397) from your Apple ID account settings.

---

#### `Fastfile`

```ruby
default_platform(:ios)

platform :ios do
  desc "Build and archive iOS app"
  lane :beta do
    increment_build_number(xcodeproj: "Runner.xcodeproj")
    build_app(
      workspace: "Runner.xcworkspace",
      scheme: "Runner",
      clean: true,
      export_method: "app-store",
      export_options: {
        provisioningProfiles: {
          "com.fourbrains.ciCdDemo2" => "com.fourbrains.ciCdDemo2 AppStore"
        }
      }
    )
  end

  desc "Upload .ipa to TestFlight"
  lane :upload_app do
    deliver(
      ipa: "./build/ios/ipa/Runner.ipa",
      force: true,
      skip_screenshots: true,
      skip_metadata: true,
      submit_for_review: false
    )
  end

  desc "Build and upload to TestFlight"
  lane :upload_to_testflight do
    beta
    upload_app
  end
end
```

---

### Step 6: Download and Install Certificates & Profiles

You can manually pull signing credentials:

```bash
fastlane get_certificates
fastlane get_provisioning_profile
```

> ✅ Recommended: Use [match](https://docs.fastlane.tools/actions/match/) to manage certs and profiles in a secure GitHub repo.

---

### Step 7: Upload to TestFlight

Build and upload using:

```bash
fastlane beta          # Builds the .ipa file
fastlane upload_app    # Uploads the IPA to TestFlight
```

Or both together:

```bash
fastlane upload_to_testflight
```

This will:

- Build the app
- Generate the `.ipa`
- Upload to TestFlight via App Store Connect

---

## 🧯 Troubleshooting

- Ensure provisioning profiles and certificates are installed locally.
- Validate your Apple Developer credentials.
- Use shared schemes in Xcode (`Product > Scheme > Manage Schemes > Share`).
- Set environment variables securely using `.env` files or GitHub Secrets in CI.
- Double-check your `Appfile` and `Fastfile` configuration.
- App-Specific Password is required for App Store Connect upload.

---

## 🧪 CI Integration (Optional)

Fastlane can be integrated with:

- GitHub Actions
- GitLab CI
- Bitrise
- Jenkins
- CircleCI

### Example GitHub Actions Workflow

```yaml
name: iOS Build and Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: "3.13.0"
      - run: flutter pub get
      - run: flutter build ios --release
      - name: Install Fastlane
        run: brew install fastlane
      - name: Deploy to TestFlight
        run: |
          cd ios
          fastlane upload_to_testflight
```

---

## 📦 Future Enhancements

- [ ] Android Fastlane integration
- [ ] Jenkins pipeline support
- [ ] Auto-changelog and semantic versioning
- [ ] Slack/Discord notifications
- [ ] GitHub releases and tags

---

## 🤝 Contributing

Pull requests are welcome!  
Please fork the repo and submit your improvements or new automation lanes.

---

## 📚 Resources

- [Fastlane Documentation](https://docs.fastlane.tools/)
- [Flutter iOS Deployment](https://flutter.dev/docs/deployment/ios)
- [Apple App-Specific Passwords](https://support.apple.com/en-in/HT204397)
- [Provisioning Profiles & Certificates](https://developer.apple.com)

---

## ✅ Summary

Fastlane enables one-command deployment from your terminal:

```bash
fastlane upload_to_testflight
```

No more manual archiving or uploads through Xcode. This setup helps reduce friction, saves time, and ensures consistency in your release process.

---
