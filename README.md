
# ADB Guide for Amazon Fire TV Stick

This repository provides a guide to enabling and using an Amazon Fire TV Stick as an **ADB (Android Debug Bridge) device** from both **Windows and Linux**.

Testing Notice: All commands and procedures documented here were personally tested on the specified device(s). **No device damage, instability, or unexpected behavior was observed during testing.**

ADB can be used to:

- Install applications
- Configure the Fire TV Stick
- Transfer files
- Manage installed packages
- Capture screenshots
- View real-time diagnostic information
- Mirror and control the Fire TV Stick using `scrcpy`

## Compatibility

This guide works with Fire TV Sticks that shipped with **Fire OS based on Android**, up to **Android 11**.

### Tested Devices

- Fire TV Stick HD (1st Generation)
- Fire TV Stick 4K (2nd Generation)
- Fire TV Stick 4K Max

### Important: Vega OS (Fire tv sticks shipped from November 2025)

This guide **does not work with newer Fire TV Stick models shipped with Vega OS**.

Vega OS is based on Linux rather than Android and therefore does not provide the same Android ADB environment used by Fire OS devices.

---

# Enable ADB Debugging

Before connecting your Fire TV Stick to your computer, enable developer options and ADB debugging.

1. Open **Settings** on your Fire TV Stick.
2. Go to **My Fire TV**.
3. Open **About**.
4. Click the name of your Fire TV Stick repeatedly, approximately **seven times**, until a message confirms that developer options have been enabled.
5. Return to **Settings**.
6. Open **Developer Options**.
7. Enable **ADB Debugging**.

---

# USB Connection (most efficient)

Connect the Fire TV Stick to your computer using a USB cable.

If the Fire TV Stick repeatedly bootloops after connecting it to your computer, connect the USB cable to your **motherboard I/O port** or use a suitable power port on your laptop instead. If it still bootloops go for the wifi method

---

# Installing ADB

## Windows

### 1. Download Android SDK Platform-Tools

Download the official portable **SDK Platform-Tools for Windows** from Google.

### 2. Extract Platform-Tools

Extract the downloaded ZIP archive to a convenient location, for example:

```text
C:\platform-tools
```

### 3. Open Command Prompt or PowerShell

Open **Command Prompt** or **PowerShell** and navigate to the extracted folder:

```powershell
cd C:\platform-tools
```

Replace `C:\platform-tools` with the actual location where you extracted Platform-Tools.

### 4. Verify the Installation

Run:

```powershell
adb version
```

If you are using PowerShell and the command is not recognized, run:

```powershell
.\adb version
```

The command should display the installed Android Debug Bridge version.

---

# Linux

This guide covers:

- Arch Linux
- Debian
- Ubuntu
- Linux Mint
- Fedora

## 1. Install ADB

Open your Linux terminal and install the Android tools package for your distribution.

### Arch Linux

```bash
sudo pacman -S --needed android-tools
```

### Debian / Ubuntu / Linux Mint

```bash
sudo apt update && sudo apt install -y adb
```

### Fedora

```bash
sudo dnf install -y android-tools
```

## 2. Verify the Installation

Run:

```bash
adb --version
```

The command should display the installed Android Debug Bridge version.

---

# Connect the Fire TV Stick via USB

USB is generally the most efficient connection method.

### 1. Connect the Device

Connect the Fire TV Stick to your computer using a **micro-USB cable that supports data transfer (better use the original cable)**.

Do not use a charge-only cable.

### 2. Check Connected Devices

Run:

```bash
adb devices
```

### 3. Authorize the Computer

Look at your TV screen.

A prompt should appear asking:

**Allow USB debugging?**

Select:

**Always allow from this computer**

Then select **OK**.

### 4. Verify the Connection

Run:

```bash
adb devices
```

A successful connection should display:

```text
<device-serial>    device
```

The device should not appear as `unauthorized`, and the output should not be empty.

---

# Connect the Fire TV Stick via Wi-Fi

ADB can also be used over Wi-Fi.

### 1. Connect Both Devices to the Same Network

Ensure that your computer and Fire TV Stick are connected to the **same Wi-Fi router or subnet**.

### 2. Find the Fire TV Stick IP Address

On the Fire TV Stick, open:

**Settings → My Fire TV → About → Network**

Find the device's IP address.

### 3. Connect Using ADB

Replace the example IP address with the actual IP address of your Fire TV Stick:

```bash
adb connect 192.168.1.50:5555
```

### 4. Authorize the Connection

If an authorization prompt appears on the TV, allow the connection.

### 5. Verify the Connection

Run:

```bash
adb devices
```

A successful connection should display:

```text
192.168.1.50:5555    device
```

---

# Useful ADB Commands

Once your Fire TV Stick is connected, ADB provides a wide range of useful commands for managing and troubleshooting the device.

---

## APK Installation

### Basic APK Sideloading

The basic installation command sends the APK to the Fire TV Stick and registers it with the Fire OS package manager:

```bash
adb install /path/to/app.apk
```

During installation, ADB may display:

```text
Performing Streamed Install
```

A successful installation ends with:

```text
Success
```

The application should then appear in your Fire TV's **Apps & Channels** section.

### Reinstall or Update an Existing Application

If the application is already installed, use the `-r` flag:

```bash
adb install -r app.apk
```

The `-r` option keeps the existing application data and cache while updating the application binary.

---

# Package Management and Bloatware Control

Fire OS includes background services, telemetry components, recommendation services, and other system packages that can consume system resources.

ADB provides access to the Android package manager without requiring root access.

## List Installed Third-Party Applications

```bash
adb shell pm list packages -3
```

This lists packages installed as third-party applications.

## Disable a System Application

Instead of deleting system files, packages can be disabled:

```bash
adb shell pm disable-user --user 0 com.amazon.bueller.photos
```

The package name above is an example.

Use the correct package name for the application you want to manage.

Disabling system packages should be done carefully, as disabling an essential package can cause unexpected behavior or require a reset.

## Re-enable a Disabled Package

To re-enable a previously disabled package:

```bash
adb shell pm enable com.amazon.bueller.photos
```

---

# File Transfers

ADB can transfer files directly between your computer and Fire TV Stick.

This eliminates the need for cloud storage, FTP servers, or USB flash drives for many file-transfer tasks.

## Push a File to the Fire TV Stick

Example:

```bash
adb push ~/Downloads/playlist.m3u /sdcard/Download/
```

This transfers a local file from your computer to the Fire TV Stick.

## Pull a File to Your Computer

Example:

```bash
adb pull /sdcard/Download/backup.xml ~/Desktop/
```

This copies a file from the Fire TV Stick to your computer.

---

# Screenshots and Diagnostic Logs

ADB can also be used to capture screenshots and inspect real-time system logs.

## Capture a Screenshot

Save a screenshot directly to your computer:

```bash
adb exec-out screencap -p > firetv_screenshot.png
```

The resulting `firetv_screenshot.png` file will be saved in your current terminal directory.

## View Real-Time Error Logs

To monitor application crashes, memory warnings, and other error messages:

```bash
adb logcat *:E
```

This provides a real-time stream of Android error-level logs.

---

# Screen Mirroring and Control with scrcpy

[`scrcpy`](https://github.com/Genymobile/scrcpy) can be used to mirror and control the Fire TV Stick from a Windows or Linux computer.

Install `scrcpy` on your computer and launch it while the Fire TV Stick is connected through ADB.

The Fire TV Stick's display will then appear on your computer, allowing it to function somewhat like a second screen.

There are, however, some limitations and trade-offs.

## USB Connection

A USB connection generally provides the lowest latency.

Typical latency can be around **30–50 ms**, depending on the hardware and configuration.

It can provide a smooth video feed with minimal network-related interference.

## Wi-Fi Connection

A wireless connection can be established using ADB over TCP/IP.

For example:

```bash
adb connect 192.168.1.50:5555
```

The quality of the mirrored video depends heavily on the local network.

Network interference or insufficient bandwidth can result in:

- Packet loss
- Compression artifacts
- Increased latency
- Occasional frame drops

## DRM Protection

Content protected by hardware DRM, such as **Widevine L1**, may appear as a black screen when mirrored through `scrcpy`.

For example, protected content from applications such as **Netflix** or **Prime Video** may not be visible through screen mirroring because of hardware DRM restrictions.

Open applications such as **SmartTube, Kodi, and TizenTube** can generally be mirrored without the same DRM restriction.

---

# Troubleshooting

If the Fire TV Stick does not appear when running:

```bash
adb devices
```

check the following:

1. ADB debugging is enabled.
2. The USB cable supports data transfer.
3. The Fire TV Stick is properly powered.
4. The computer and Fire TV Stick are connected to the same network when using Wi-Fi ADB.
5. The USB debugging authorization prompt on the TV has been accepted.
6. The correct IP address is being used for Wi-Fi connections.
7. ADB is installed correctly on the computer.

---

# Notes

ADB provides powerful access to the Fire TV Stick. Commands that modify or disable system packages should be used carefully.

Disabling or modifying essential Fire OS components can cause applications or system features to stop working.

For this reason, **disable packages rather than deleting system files whenever possible**.

---

# Disclaimer

This guide is provided for educational and personal-use purposes.

ADB provides low-level access to Android and Fire OS functionality. Commands that modify system packages, applications, or configuration can cause unexpected behavior if used incorrectly.

Use these commands at your own discretion and verify package names before disabling system components.
