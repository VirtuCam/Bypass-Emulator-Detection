# 🚀 Emulator Detection Bypass Guide

## 📌 Prerequisites
Ensure you have an emulator set up with **SDK version 12 or below** and **Play Store support** on `x86_64` architecture.

---

## 1️⃣ Magisk Image Flashing
### Steps:
1. Join [VirtuCam](https://t.me/+S2CIAkxeGf4wNmFk) (Private Channel).
2. Watch the [Rooting Emulator Video](https://t.me/c/2237658876/140).

✅ Your emulator is now **rooted and ready**!

```bash
# Magisk Setup
* Once your emulator has Magisk installed, launch the app.
* Grant permissions when prompted, which will reboot your device.
* After reboot, open Magisk settings and enable **Zygisk**.
* Reboot your device from Magisk options.
```

---

## 2️⃣ Setting Up Termux
### Installation:
- Download the latest Termux APK from [Termux Releases](https://github.com/termux/termux-app/releases) based on your emulator architecture.

### Setup:
```bash
pkg update && pkg upgrade
termux-setup-storage  # Grant storage permission
```
✅ Termux is now ready!

---

## 3️⃣ Additional Apps/Tools Needed
- Download and install **Root Explorer APK** for file management.

---

## 4️⃣ Installing Frida and Dependencies in Termux
```bash
pkg install build-essential python python-pip git wget binutils openssl
```
- Download **Frida Core DevKit** for your device architecture from [Frida Releases](https://github.com/frida/frida/releases).
- Extract and move the 4 files into your emulator.
- Using **Root Explorer**, navigate to `root -> sdcard -> downloads` to locate the copied files.
- Move these files to `root -> sdcard -> devkit` (Create the folder if it doesn't exist).
- Set up Frida Core path in Termux:
```bash
export FRIDA_CORE_DEVKIT=/sdcard/devkit/
```
- Install Frida and its CLI tools:
```bash
pip install frida frida-tools
```
- Verify Frida installation:
```bash
frida-ps --version
```

✅ Frida is now installed!

---

## 5️⃣ Installing Frida Server
- Download the latest **Frida Server** for your device (`server` & `android` keywords in the filename) from [Frida Releases](https://github.com/frida/frida/releases).
- Extract and move the file to your emulator.
- Using **Root Explorer**, navigate to `root -> sdcard -> download` and locate the Frida server file.
- Move the file to `root -> data -> local -> tmp` and rename it to `frida-server`.
- Grant execution permissions:
  - Long-press the file in Root Explorer.
  - Select **Additional Options > Permissions**.
  - Enable **Execution Rights** for all user types.

### Starting Frida Server in Termux:
```bash
su
cd /data/local/tmp
ls  # Verify frida-server is present
./frida-server -l 127.0.0.1
```
✅ Frida Server is now running!

---

## 6️⃣ Running Emulator Bypass Script
- Move the `bypass.js` script to `root -> data -> local -> tmp`.
- Check Frida connection:
```bash
frida-ps -H 127.0.0.1
```
- Run the bypass command:
```bash
frida -H 127.0.0.1 -f your.packagename -l /data/local/tmp/bypass.js
```
✅ Emulator bypass is now complete!

---

## 7️⃣ Allowing Termux to Accept External Commands
```bash
value="true"; key="allow-external-apps"; file="/data/data/com.termux/files/home/.termux/termux.properties";
mkdir -p "$(dirname "$file")";
chmod 700 "$(dirname "$file")";
if ! grep -E '^'"$key"'=.*' $file &>/dev/null; then
  [[ -s "$file" && ! -z "$(tail -c 1 "$file")" ]] && newline=$'\n' || newline="";
  echo "$newline$key=$value" >> "$file";
else
  sed -i'' -E 's/^'"$key"'=.*'/'"$key=$value"'/' $file;
fi
```

### Adding Necessary Permissions in Your Android App:
```xml
<uses-permission android:name="com.termux.permission.RUN_COMMAND"/>
```
```xml
<queries>
    <package android:name="com.termux" />
    <intent>
        <action android:name="android.intent.action.MAIN" />
    </intent>
</queries>
```

### Sending Commands to Termux from Your App:
```kotlin
intent.setClassName("com.termux", "com.termux.app.RunCommandService")
intent.action = "com.termux.RUN_COMMAND"
intent.putExtra("com.termux.RUN_COMMAND_PATH", "/data/data/com.termux/files/usr/bin/frida")
intent.putExtra(
    "com.termux.RUN_COMMAND_ARGUMENTS",
    arrayOf("-H", "127.0.0.1", "-f", "your.app.package.name", "-l", "/data/local/tmp/bypass.js")
)
intent.putExtra("com.termux.RUN_COMMAND_BACKGROUND", true)
intent.putExtra("com.termux.RUN_COMMAND_SESSION_ACTION", "4")
startService(intent)
```

---

## 8️⃣ Troubleshooting Frida Errors
If you encounter the following error:
```bash
File "/data/data/com.termux/files/usr/lib/python3.11/re/_parser.py", line 455, in _parse_sub
```
Run:
```bash
pip uninstall pygments && pip install pygments
```

✅ Issue resolved!

---

## 📚 References
- [Rooting Emulator](https://avicoder.me/2021/09/02/Root-AVD-and-install-Magisk/)
- [Frida Setup on Termux](https://github.com/frida/frida/discussions/2411)
- [Termux Property Editing](https://github.com/termux/termux-tasker#allow-external-apps-property-optional)
- [Frida Run Commands](https://github.com/termux/termux-app/wiki/RUN_COMMAND-Intent)

---

### 🎯 Congratulations! You have successfully bypassed emulator detection. 🎯
