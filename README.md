# Emulator Detection Bypass Guide

## Prerequisites
- **Emulator**: Ensure you have an emulator running with an SDK version **12 or below** and a **x86_64 architecture**.
- **Play Store**: Required for installing necessary applications.

---

## 1️⃣ Magisk Image Flashing

### Steps:
1. Download and extract the **rootAVD** repository from [this link](https://github.com/newbit1/rootAVD).
2. Open a terminal on your PC and navigate to the extracted directory.
3. Run the command:
   ```sh
   ./rootAVD.sh
   ```
   This will list available commands.
4. Run:
   ```sh
   ./rootAVD.sh ListAllAVDs
   ```
   Select the image matching your emulator's architecture type.
5. After flashing, the emulator will reboot.
6. Install Magisk by running:
   ```sh
   ./rootAVD.sh InstallApps
   ```
7. Start the Magisk app, grant permissions when prompted, and reboot the emulator.
8. Enable **Zygisk** from Magisk settings and reboot again.

✅ Your device is now **rooted and ready**!

---

## 2️⃣ Setting up Termux

### Steps:
1. Download the latest **Termux APK** based on your emulator's architecture from [this link](https://github.com/termux/termux-app/releases).
2. Open Termux and run:
   ```sh
   pkg update && pkg upgrade
   ```
3. Grant storage permissions:
   ```sh
   termux-setup-storage
   ```
✅ Termux is now ready for use!

---

## 3️⃣ Essential Tools & Apps
- **Root Explorer APK**: Install for file management.
- **Frida**: Needed for security bypassing.

---

## 4️⃣ Installing Frida in Termux

### Steps:
1. Install dependencies:
   ```sh
   pkg install build-essential python python-pip git wget binutils openssl
   ```
2. Download **Frida Core DevKit** from [Frida Releases](https://github.com/frida/frida/releases).
3. Extract and move files to `/sdcard/devkit/`.
4. Set up Frida environment:
   ```sh
   export FRIDA_CORE_DEVKIT=/sdcard/devkit/
   ```
5. Install Frida via pip:
   ```sh
   pip install frida frida-tools
   ```
6. Verify installation:
   ```sh
   frida-ps --version
   ```

---

## 5️⃣ Setting Up Frida Server

### Steps:
1. Download **Frida Server** (matching your architecture) from [Frida Releases](https://github.com/frida/frida/releases).
2. Extract and move the file to `/data/local/tmp/`.
3. Rename the file to `frida-server`.
4. Change execution permissions:
   ```sh
   chmod +x /data/local/tmp/frida-server
   ```
5. Start the server:
   ```sh
   su
   cd /data/local/tmp
   ./frida-server -l 127.0.0.1
   ```

---

## 6️⃣ Connecting to Frida Server

### Steps:
1. Open a new Termux session.
2. Run the following to check Frida connection:
   ```sh
   frida-ps -H 127.0.0.1
   ```
3. To run an actual bypass command:
   ```sh
   frida -H 127.0.0.1 -f your.packagename -l /data/local/tmp/bypass.js
   ```
✅ If successful, your Frida setup is complete!

---

## 7️⃣ Configuring Termux to Accept External Commands

### Steps:
1. Modify Termux properties:
   ```sh
   value="true"; key="allow-external-apps"; file="/data/data/com.termux/files/home/.termux/termux.properties"; mkdir -p "$(dirname "$file")"; chmod 700 "$(dirname "$file")"; if ! grep -E '^'"$key"'=.*' $file &>/dev/null; then [[ -s "$file" && ! -z "$(tail -c 1 "$file")" ]] && newline=$'\n' || newline=""; echo "$newline$key=$value" >> "$file"; else sed -i'' -E 's/^'"$key"'=.*/'"$key=$value"'/' $file; fi
   ```
2. Enable permissions in **AndroidManifest.xml**:
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
3. Send commands to Termux from your app:
   ```kotlin
   intent.setClassName("com.termux", "com.termux.app.RunCommandService")
   intent.action = "com.termux.RUN_COMMAND"
   intent.putExtra("com.termux.RUN_COMMAND_PATH", "/data/data/com.termux/files/usr/bin/frida")
   intent.putExtra(
            "com.termux.RUN_COMMAND_ARGUMENTS",
            arrayOf("-H", "127.0.0.1", "-f", "your app package name", "-l", "/data/local/tmp/bypass.js")
   )
   intent.putExtra("com.termux.RUN_COMMAND_BACKGROUND", true)
   intent.putExtra("com.termux.RUN_COMMAND_SESSION_ACTION", "4")
   startService(intent)
   ```

---

## 8️⃣ Troubleshooting Frida Errors
If you encounter an error like:
```
File "/data/data/com.termux/files/usr/lib/python3.11/re/_parser.py", line 455, in _parse_sub itemsappend(_parse(source, state, verbose, nested + 1
```
Run:
```sh
pip uninstall pygments && pip install pygments
```

---

## References
1. [Rooting Emulator](https://avicoder.me/2021/09/02/Root-AVD-and-install-Magisk/)
2. [Frida Setup on Termux](https://github.com/frida/frida/discussions/2411)
3. [Termux Property Edit](https://github.com/termux/termux-tasker#allow-external-apps-property-optional)
4. [Frida Run Commands](https://github.com/termux/termux-app/wiki/RUN_COMMAND-Intent)

✅ **You are now fully set up to bypass emulator detection using Frida!** 🚀

