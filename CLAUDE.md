# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

This is a **branded app repository** for the "Basspistol" brand, one of many production branded apps based on OwnTube.tv. This repo is a **delivery mechanism only** — it contains NO application source code.

**What's in this repo:**
- `.customizations` file: Environment variables for branding configuration
- `/assets` folder: Brand-specific image assets (icons, splash screens, TV banners)
- `.github/workflows/`: CI/CD workflows that pull code from upstream and apply customizations

**What's NOT in this repo:**
- Application source code (lives in `OwnTube-tv/web-client`)
- Development environment (no `package.json`, no `npm` commands)
- Local build/test capabilities (all builds happen via GitHub Actions)

## For Complete Documentation

**→ See the upstream repository's documentation for:**
- **[CLAUDE.md](https://github.com/OwnTube-tv/web-client/blob/main/CLAUDE.md)** - Full project architecture, build commands, code style guidelines, testing practices, core technologies, development workflow, and contribution guidelines
- **[docs/customizations.md](https://github.com/OwnTube-tv/web-client/blob/main/docs/customizations.md)** - Complete reference of all available `EXPO_PUBLIC_*` customization variables
- **[docs/pipeline.md](https://github.com/OwnTube-tv/web-client/blob/main/docs/pipeline.md)** - CI/CD pipeline setup and GitHub secrets configuration

**Upstream repository:** https://github.com/OwnTube-tv/web-client

**Template for new branded apps:** https://github.com/OwnTube-tv/cust-app-template

## Working With This Branded App Repository

### Repository Structure
```
.customizations           # Brand configuration (EXPO_PUBLIC_* environment variables)
assets/                   # Brand-specific images with dimension-specific naming
  ├── icon_512x512.png
  ├── splashScreen_1152x1152.png
  ├── AppleTvIcon_1280x768.png
  ├── androidTvBanner_320x180.png
  └── ...
.github/workflows/        # CI/CD workflows (pulled from upstream)
  ├── build_branded_main.yml
  └── google_play_initial_bundle.yml
```

### The `.customizations` File

Contains `EXPO_PUBLIC_*` prefixed environment variables that configure branding:

**App Identity:**
- `EXPO_PUBLIC_APP_NAME`: App display name (e.g., "Basspistol")
- `EXPO_PUBLIC_APP_SLUG`: Unique identifier (e.g., com.owntubetv.basspistol)
- `EXPO_PUBLIC_IOS_BUNDLE_IDENTIFIER`: iOS bundle ID
- `EXPO_PUBLIC_ANDROID_PACKAGE`: Android package name
- `EXPO_PUBLIC_PRIMARY_BACKEND`: PeerTube instance hostname (e.g., v.basspistol.org)

**Assets** (must use `../customizations/assets/` path prefix):
- `EXPO_PUBLIC_ICON`: App icon (512x512)
- `EXPO_PUBLIC_SPLASH_IMAGE`: Splash screen (1152x1152)
- `EXPO_PUBLIC_FAVICON_URL`: Web favicon (32x32)
- `EXPO_PUBLIC_APPLE_TV_*`: Apple TV icons and top shelf images (7 different sizes)
- `EXPO_PUBLIC_ANDROID_TV_BANNER`: Android TV banner (320x180)

**Branding & Legal:**
- `EXPO_PUBLIC_PROVIDER_LEGAL_ENTITY`: Legal entity name
- `EXPO_PUBLIC_PROVIDER_LEGAL_EMAIL`: Legal contact email
- `EXPO_PUBLIC_PROVIDER_CONTACT_EMAIL`: General contact email
- `EXPO_PUBLIC_FOOTER_LOGO`: Custom footer logo URL

**Feature Toggles:**
- `EXPO_PUBLIC_HIDE_GIT_DETAILS`: Set to "1" to hide build info
- `EXPO_PUBLIC_HIDE_VIDEO_SITE_LINKS`: Set to "1" to hide PeerTube site links

**Analytics:**
- `EXPO_PUBLIC_POSTHOG_API_KEY`: PostHog project key (or null to disable)

See upstream [docs/customizations.md](https://github.com/OwnTube-tv/web-client/blob/main/docs/customizations.md) for complete list of available customizations.

### Asset Path Convention ⚠️

**CRITICAL:** When referencing assets in `.customizations`, always use the `../customizations/assets/` prefix:

```bash
# ✅ CORRECT
EXPO_PUBLIC_ICON=../customizations/assets/icon_512x512.png

# ❌ WRONG
EXPO_PUBLIC_ICON=./assets/icon_512x512.png
EXPO_PUBLIC_ICON=assets/icon_512x512.png
```

This is the path structure expected by the upstream build process.

### GitHub Actions Workflows

**`build_branded_main.yml`** - Main build and deployment workflow
- **Trigger:** Manual workflow dispatch (push-button releases)
- **What it does:**
  1. Reads `.customizations` file
  2. Pulls source code from `OwnTube-tv/web-client@main`
  3. Applies customizations during build
  4. Builds all platforms (web, iOS, tvOS, Android, Android TV)
  5. Optionally uploads to TestFlight and/or Google Play
- **Options:**
  - Upload iOS to TestFlight (checkbox)
  - Upload tvOS to TestFlight (checkbox)
  - Google Play track selection (none/internal/alpha/beta/production)
  - Include Android TV in Google Play upload (checkbox)

**`google_play_initial_bundle.yml`** - Generate first bundle for Google Play
- **Trigger:** Manual workflow dispatch
- **Purpose:** Google requires first upload to be manual through their console
- **Output:** Downloads `.aab` artifact for manual upload

All workflows delegate to reusable workflows in `OwnTube-tv/web-client/.github/workflows/`.

### Common Tasks

**Update branding assets:**
1. Replace image files in `/assets` folder (maintain required dimensions)
2. Commit and push changes
3. Trigger `build_branded_main.yml` workflow in GitHub Actions UI
4. Test changes via TestFlight (iOS/tvOS) or Google Play Internal Test (Android)

**Update configuration:**
1. Edit `.customizations` file
2. Commit and push changes
3. Trigger `build_branded_main.yml` workflow
4. Test changes via TestFlight (iOS/tvOS) or Google Play Internal Test (Android)

**Deploy new release:**
1. Go to GitHub Actions → "Build mobile artifacts & deploy static content to Pages for a branded app"
2. Click "Run workflow"
3. Select deployment options (TestFlight, Google Play track, etc.)
4. Click "Run workflow" button
5. Wait for build completion (check GitHub Actions logs for any errors)
6. Test the deployed build:
   - **Web:** Visit the GitHub Pages URL immediately after deployment
   - **iOS/tvOS:** Check TestFlight app for new build availability (if uploaded)
   - **Android:** Check Google Play Console for new build in selected track (if uploaded)

**First-time Google Play setup:**
1. Run `google_play_initial_bundle.yml` workflow
2. Download the `.aab` artifact
3. Upload manually to Google Play Console
4. After first upload, automated uploads via `build_branded_main.yml` will work

## Testing & Development Workflow

**⚠️ IMPORTANT: No local development/testing support in branded app repos**

This repository does NOT support local development or testing. All changes must be:
1. Committed to the repository
2. Built via GitHub Actions workflows
3. Tested using one of these methods:
   - **Web:** Test directly on GitHub Pages deployment URL
   - **iOS/tvOS:** Use Apple TestFlight (enable "Upload to TestFlight" in workflow)
   - **Android/Android TV:** Use Google Play Internal Test track (select "internal" track in workflow)

**For application code development:**
- Clone the upstream repository: https://github.com/OwnTube-tv/web-client
- Follow development instructions in upstream [CLAUDE.md](https://github.com/OwnTube-tv/web-client/blob/main/CLAUDE.md)
- After testing changes upstream, branded apps will automatically get updates on next build

**Testing branded customizations (no upstream changes needed):**
1. Make changes to `.customizations` or `/assets` in this repo
2. Trigger `build_branded_main.yml` workflow with TestFlight/Internal Test options enabled
3. Wait for build completion (~15-30 minutes)
4. Download and test via TestFlight or Google Play Console:
   - TestFlight: Builds appear in TestFlight app within minutes
   - Google Play Internal: Log into Play Console, go to Internal Testing track
5. If issues found, repeat from step 1

**Developing features that require upstream changes:**

When you need to experiment with customizations that require changes to the upstream web-client code:

1. **Fork the upstream repository:**
   - Go to https://github.com/OwnTube-tv/web-client
   - Click "Fork" and create your own fork (e.g., `yourname/web-client`)

2. **Modify this repo's workflows to use your fork:**
   - Edit `.github/workflows/build_branded_main.yml`
   - Edit `.github/workflows/google_play_initial_bundle.yml`
   - Change `owntube_source: OwnTube-tv/web-client` to `owntube_source: yourname/web-client`
   - Optionally change `@main` to `@your-branch` in workflow references

3. **Develop in your fork:**
   - Clone your fork: `git clone https://github.com/yourname/web-client.git`
   - Make code changes, test locally following upstream [CLAUDE.md](https://github.com/OwnTube-tv/web-client/blob/main/CLAUDE.md)
   - Commit and push changes to your fork

4. **Test with branded app:**
   - Trigger `build_branded_main.yml` workflow in this repo (now using your fork)
   - Test via TestFlight/Google Play Internal as usual
   - Iterate on changes in your fork until satisfied

5. **Contribute upstream (optional):**
   - Once your changes are working, consider contributing them back to upstream
   - Create a pull request from your fork to `OwnTube-tv/web-client`
   - Follow upstream contribution guidelines

6. **Restore to upstream (when done):**
   - After upstream merges your changes OR you're done experimenting
   - Revert workflow files to use `owntube_source: OwnTube-tv/web-client`
   - Commit and push to restore normal operation

**Example use case:** You want to add a new customization option like `EXPO_PUBLIC_CUSTOM_HEADER_COLOR`. You'd fork the upstream, modify `app.config.ts` to support this variable, test it with your branded app using your fork, then contribute the change upstream so all branded apps can use it.

### Updating from Upstream

This repo automatically pulls the latest code and workflows from `OwnTube-tv/web-client@main` during each build. No action needed unless:

- **Workflow files change:** Manually update `.github/workflows/` files if upstream workflow signatures change
- **New customization options:** Check upstream [docs/customizations.md](https://github.com/OwnTube-tv/web-client/blob/main/docs/customizations.md) for new `EXPO_PUBLIC_*` variables
- **Breaking changes:** Monitor [upstream releases](https://github.com/OwnTube-tv/web-client/releases) for breaking changes that may require `.customizations` updates

### Important Constraints

**DO:**
- ✅ Modify `.customizations` file
- ✅ Update files in `/assets` folder
- ✅ Deploy via GitHub Actions workflows

**DO NOT:**
- ❌ Add application source code to this repository
- ❌ Attempt to run the app locally (no local development support - use GitHub Actions + TestFlight/Google Play Internal Test)
- ❌ Run `npm install` or any development commands (this repo has no `package.json`)
- ❌ Create additional configuration files or directories
- ❌ Modify workflow files (they're pulled from upstream; changes will be overwritten)
- ❌ Expect local builds to work (all builds must go through GitHub Actions)

## Basspistol Brand Information

**PeerTube Instance:** https://v.basspistol.org
**Description:** Music published by The Outernational Music Syndicate

**Deployment URLs:**
- Web: https://cust-app-basspistol.owntube.tv/
- iOS/tvOS TestFlight: https://testflight.apple.com/join/pqVV5Cu2
- Android Google Play: https://play.google.com/store/apps/details?id=com.owntubetv.basspistol

**Legal Entity:** OwnTube Nordic AB
**Contact:** hello@owntube.tv
