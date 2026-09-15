# Install Mural on iPhone from Windows

Mural is a SwiftUI app, so a Mac is normally required. This route replaces the
Mac with a GitHub Actions macOS runner that produces an **unsigned** `.ipa`, and
replaces Xcode's installer with **AltStore**, which re-signs the app with your
own free Apple ID on the phone.

Nothing here needs an Apple Developer Program membership, and no Apple
credentials are ever stored in GitHub.

## Requirements

- iPhone on **iOS 26.1 or later** (the project's deployment target)
- A free Apple Account
- Windows PC on the same Wi-Fi network as the phone
- An OpenAI **API** project with billing and access to GPT-Live-1 and GPT-5.6
  Luna. A ChatGPT subscription is not API credit.

## 1. Build the IPA

1. Fork `Chuloo/mural` to your own GitHub account.
2. Make sure `.github/workflows/ios-ipa.yml` is on your fork's default branch.
3. Open **Actions → iOS unsigned IPA → Run workflow**.
4. When it finishes, download the `Mural-unsigned-ipa` artifact and unzip it.
   You get `Mural-unsigned.ipa` and `SHA256SUMS.txt`.

The build is unsigned on purpose. Do not try to open the `.ipa` directly on the
phone; it has to go through AltStore.

If the run fails on the `macos-26` label, change `runs-on` to `macos-latest`.
If it then fails the Xcode check, GitHub's image does not carry Xcode 26 yet and
this route is blocked until it does.

## 2. Install AltServer on Windows

Use **AltStore Classic** (`altstore.io`), not AltStore PAL. PAL is the EU
alternative marketplace for notarized apps and cannot sideload your own build.

AltServer needs Apple's Windows builds of **iTunes** and **iCloud**, downloaded
from `apple.com`. The Microsoft Store versions do not expose the drivers
AltServer requires.

1. Install iTunes and iCloud from apple.com, then AltServer.
2. Connect the iPhone by USB and unlock it. Trust the computer when asked.
3. From the AltServer tray icon choose **Install AltStore → <your iPhone>** and
   sign in with your Apple Account. The password goes to Apple, not to AltStore.
4. On the phone, open **Settings → General → VPN & Device Management** and trust
   your developer certificate.

## 3. Sideload Mural

1. Copy `Mural-unsigned.ipa` to the phone, or keep it on the PC and use
   AltStore's file picker over Wi-Fi.
2. Open AltStore on the iPhone, go to **My Apps**, tap **+**, and pick
   `Mural-unsigned.ipa`.
3. AltStore re-signs it with your Apple ID and installs it. The bundle
   identifier changes from `no.william.mural` to a team-specific variant; that is
   expected and harmless.

## 4. First run

Open Mural, choose a learning language and a subtitle language, then go to
**Settings → Advanced → Use your own API key** and paste your OpenAI project
key. Allow microphone access when the first conversation starts.

Leave managed accounts, hosted trials and purchases disabled. They are not
active in this build.

## Limits of free provisioning

- The signature **expires after 7 days**. Keep AltServer running on the PC and
  AltStore refreshes over Wi-Fi while both are awake. If it lapses, the app stops
  launching until you refresh it; your data survives a refresh.
- A free Apple ID allows **3 sideloaded apps** at a time and **10 new app IDs per
  week**.
- Export a learning backup from Settings before changing Apple ID or phone.

## Why not the other routes

- A cloud Mac can build and sign, but you cannot attach your iPhone over USB to
  it, so you still end up sideloading from Windows.
- TestFlight would remove the 7-day expiry, but it needs the paid Developer
  Program and a macOS archive step anyway.
- The project publishes Android APKs in GitHub Releases, but no `.ipa`.
