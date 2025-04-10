# Apple Vision Pro App Installation Guide

This guide provides instructions for installing this application on an Apple Vision Pro device through three different methods.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation Methods](#installation-methods)
  - [Method 1: Via Xcode](#method-1-via-xcode)
  - [Method 2: From GitHub](#method-3-from-github)
- [Troubleshooting](#troubleshooting)
- [Support](#support)

## Prerequisites

Before proceeding with any installation method, ensure you have the following:

* An Apple Vision Pro device running visionOS 2.0 or later
* A Mac computer with macOS 15 Sequoia or later
* Xcode 16 or later installed on your Mac
* njitresearch63@gmail.com account
* USB-C to USB-C cable for connecting your Mac to the Vision Pro
* Apple Vision Pro Developer Strap (recommended for development)

## Installation Method From Xcode to Vision Pro

#### Step 1: Configure Your Development Environment

1. Launch Xcode on your Mac.
2. Go to Xcode → Settings → Accounts.
3. Add your Apple ID if not already configured.
4. Select your Apple ID (njitresearch63) and click "Manage Certificates" to ensure you have a valid Developer certificate.

#### Step 2: Connect Your Apple Vision Pro

1. Connect your Apple Vision Pro to your Mac using the USB-C cable.
2. Put on the headset and navigate to Settings → Developer.
3. Enable "Developer Mode" if not already enabled.
4. When prompted on your Mac, trust the connected device.

#### Step 3: Open and Build the Project

1. Open the app project in Xcode.
2. Go to Window > Devices and Simulators
3. Find Vision Pro: listed in the left-hand column under "Devices"
4. Select your Vision Pro and click the "Pair" button
5. A code will appear on your Mac and you'll need to enter it on your Vision Pro when prompted
6. Select the appropriate build scheme for your app.
7. Click the "Run" button (▶️) in the Xcode toolbar or use shortcut `⌘R`.

#### Step 4: Authorize App Installation

1. When prompted on your Apple Vision Pro, look at the notification and select "Allow" to install the developer app.
2. Wait for the installation to complete. The app will automatically launch after successful installation.

## Installing Presence app from github to Xcode (if not already installed)

#### Step 1: Clone the Repository

1. Open Terminal on your Mac.
2. Navigate to your preferred directory.
3. Clone the repository using the following command:
   ```bash
   git clone https://github.com/username/repo-name.git
   ```
4. Navigate to the project directory:
   ```bash
   cd repo-name
   ```

#### Step 2: Install Dependencies (if applicable)

1. If the project uses Swift Package Manager, dependencies will be fetched automatically when you open the project in Xcode.
2. If the project uses CocoaPods, install dependencies:
   ```bash
   pod install
   ```
   Then open the `.xcworkspace` file instead of the `.xcodeproj` file.

#### Step 3: Build and Install

1. Open the Xcode project or workspace:
   ```bash
   open ProjectName.xcodeproj
   ```
   Or:
   ```bash
   open ProjectName.xcworkspace
   ```
2. Follow steps 2-4 from [Method 1: Via Xcode](#method-1-via-xcode) to build and deploy the app.

## Troubleshooting

### Common Issues and Solutions

<details>
<summary><b>App Won't Install</b></summary>

- Ensure your Apple Vision Pro is running a compatible visionOS version.
- Verify that your developer certificate is valid and not expired.
- Check that your device has sufficient storage space.
- Restart both your Mac and Apple Vision Pro device.
</details>

<details>
<summary><b>Permission Errors</b></summary>

- Make sure your Apple ID has the necessary permissions to deploy apps.
- For enterprise distribution, verify the device is properly provisioned with your organization's profile.
</details>

<details>
<summary><b>Build Errors</b></summary>

- Update Xcode to the latest version.
- Clean the build folder (`Shift + ⌘ + K`) and rebuild the project.
- Verify that all dependencies are correctly installed and compatible.
</details>
