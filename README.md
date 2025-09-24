# Chromium iOS Build Automation

This repository contains a GitHub Actions workflow to automatically build unsigned Chromium iOS IPA files.

## Overview

This workflow builds Chromium for iOS without code signing, making it suitable for:
- Development testing
- Internal distribution
- Analysis and research purposes

## Supported Targets

The workflow can build the following Chromium iOS targets:

- **chrome** - Full Chromium browser for iOS
- **content_shell** - Minimal browser shell (useful for testing)
- **ios_web_shell** - Basic web shell application

## How to Use

You can manually trigger a build:

1. Go to the **Actions** tab in your GitHub repository
2. Select **Build Chromium iOS (Unsigned)**
3. Click **Run workflow**
4. Choose your build target (chrome, content_shell, or ios_web_shell)
5. Select the minimum iOS version requirement (13.0, 14.0, 15.0, 16.0, or 17.0)
6. Click **Run workflow**

### Build Process

The workflow performs these steps:

1. **Environment Setup**
   - Sets up Xcode 15.0 (for iOS 15 support)
   - Accepts Xcode license
   - Installs depot_tools

2. **Source Code**
   - Fetches Chromium iOS source code (without full git history to save time)
   - Sets up build configuration for unsigned builds

3. **Build**
   - Configures build with no code signing
   - Builds the specified target using `autoninja`

4. **IPA Creation**
   - Creates unsigned IPA file from built app
   - Names it with timestamp: `{target}-unsigned-YYYYMMDD-HHMMSS.ipa`

5. **Artifact Upload**
   - Uploads IPA as GitHub Actions artifact
   - Artifacts are kept for 7 days

## Download Built IPA

After a successful build:

1. Go to the workflow run page
2. Scroll down to **Artifacts** section
3. Download **chromium-ios-unsigned-ipa**
4. Extract the zip file to get your IPA

## License

This workflow configuration is provided as-is. Chromium itself is subject to its own licensing terms.
