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

### Automatic Builds

The workflow will automatically trigger on:
- Push to `main` or `develop` branches
- Pull requests to `main` branch

### Manual Builds

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

## iOS Version Requirements

### Setting iOS Deployment Target

You can set the minimum iOS version requirement for your app in two ways:

#### 1. Manual Build (Recommended)
When triggering a manual build, select from these iOS versions:
- **iOS 13.0** - For maximum compatibility
- **iOS 14.0** - Balanced compatibility and features  
- **iOS 15.0** - Default, good balance (recommended)
- **iOS 16.0** - Modern features, less compatibility
- **iOS 17.0** - Latest features, minimal compatibility

#### 2. Default for Automatic Builds
Automatic builds (triggered by push/PR) use **iOS 15.0** as the default minimum version.

### How It Works

The iOS version requirement is set using the `ios_deployment_target` GN argument:
```bash
ios_deployment_target="15.0"  # Your selected version
```

This affects:
- **Minimum supported iOS version** for the built app
- **Available APIs** - newer versions enable more iOS features
- **Device compatibility** - older iOS versions support more devices
- **App Store requirements** - Apple may have minimum version policies

### Version Impact

| iOS Version | Device Support | Features | Recommendation |
|-------------|----------------|----------|----------------|
| 13.0 | iPhone 6s+ | Basic | Legacy support |
| 14.0 | iPhone 6s+ | Widget support | Good compatibility |
| 15.0 | iPhone 6s+ | Focus modes, Safari extensions | **Recommended** |
| 16.0 | iPhone 8+ | Lock screen widgets | Modern features |
| 17.0 | iPhone XS+ | Interactive widgets | Latest features |

## Build Configuration

The workflow uses these build settings:
```
target_os="ios"
ios_enable_code_signing=false
ios_deployment_target="15.0"  # Configurable via workflow input
is_debug=false
is_component_build=false
symbol_level=1
enable_dsyms=false
use_blink=true
ios_code_signing_identity=""
ios_app_bundle_id_prefix="org.chromium"
```

## Download Built IPA

After a successful build:

1. Go to the workflow run page
2. Scroll down to **Artifacts** section
3. Download **chromium-ios-unsigned-ipa**
4. Extract the zip file to get your IPA

## System Requirements

The GitHub Actions runner provides:
- macOS 13
- Xcode 15.0
- Sufficient disk space for Chromium source and build

## Build Time

- **First build**: 6-8 hours (downloads source code and builds from scratch)
- **Incremental builds**: 2-4 hours (if source hasn't changed much)

Note: The workflow has an 8-hour timeout to accommodate long build times.

## Troubleshooting

### Build Failures

Common issues and solutions:

1. **Disk Space**: The build requires ~50GB of disk space
2. **Timeout**: Large builds may take up to 8 hours
3. **Xcode License**: The workflow automatically accepts the Xcode license

### Logs

Check the GitHub Actions logs for detailed build information:
- Each step shows its progress
- Build output is captured in the logs
- Disk usage is reported at the end

## Limitations

- **No Code Signing**: IPAs are unsigned and cannot be installed on real devices without jailbreaking
- **Simulator Only**: Unsigned IPAs can only run in iOS Simulator
- **No Distribution**: These builds are for development/testing only

## Advanced Configuration

To customize the build, modify the `.setup-gn` configuration in the workflow:

```bash
cat > .setup-gn << EOF
[gn_args]
target_os="ios"
ios_enable_code_signing=false
is_debug=false  # Change to true for debug builds
is_component_build=false
symbol_level=1  # Increase for more debug symbols
enable_dsyms=false
use_blink=true  # Set to false to disable Blink
ios_code_signing_identity=""
ios_app_bundle_id_prefix="org.chromium"
# Add more GN args here as needed
EOF
```

## Contributing

To improve this workflow:

1. Fork the repository
2. Make your changes
3. Test thoroughly
4. Submit a pull request

## License

This workflow configuration is provided as-is. Chromium itself is subject to its own licensing terms.
