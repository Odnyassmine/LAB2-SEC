# Android Rooting Lab – Security Integrity Verification

## Project Overview

This project demonstrates a controlled Android laboratory setup to understand **rooting**, **Verified Boot**, and the security impact of elevated privileges on a test application running inside an Android Virtual Device (AVD).

The objective is to analyze how rooting affects device integrity and application security while maintaining a safe, isolated testing environment.

---

## Objectives

- Understand Android rooting and system privilege escalation
- Verify boot integrity mechanisms (Verified Boot / AVB)
- Test application behavior on a rooted emulator
- Collect technical evidence through ADB commands
- Document security risks and mitigation measures
- Restore environment to clean state after testing

---

## Scope

| Item | Description |
|------|-------------|
| Application | lab2 debug APK |
| Platform | Android Emulator (Pixel 5 API 33) |
| Goal | Understand rooting and impacts |
| Data | Fictitious only |
| Network | Isolated test environment |

---

# 1. Environment Setup

## AVD Information

- Emulator: Pixel 5 API 33
- Android Version: API 33
- Build Type: Emulator userdebug
- Root Access: Enabled via ADB



```

---

# 2. Device Detection

The emulator was detected successfully using ADB.

## Command

```bash
adb devices
```

## Result

```text
emulator-5554 device
```



<img width="648" height="116" alt="1" src="https://github.com/user-attachments/assets/98a68b7c-71bb-48ce-9a85-7be5d7adeab2" />



---

# 3. Root Activation

Root privileges were enabled successfully.

## Command

```bash
adb root
```

## Result

```text
restarting adbd as root
```

This confirms that the emulator supports root mode.


<img width="552" height="130" alt="2" src="https://github.com/user-attachments/assets/f8edf3c5-f051-4d39-9f33-a5d2745a09dc" />



---

# 4. Root Verification

The shell identity was checked to verify root privileges.

## Command

```bash
adb shell id
```

## Output

```text
uid=0(root)
gid=0(root)
```

This confirms superuser access.



# 5. Verified Boot Checks

Android Verified Boot properties were analyzed.

## Commands

```bash
adb shell getprop ro.boot.verifiedbootstate
adb shell getprop ro.boot.veritymode
adb shell getprop ro.boot.vbmeta.device_state
```

## Interpretation

- `verifiedbootstate`: indicates integrity state
- `veritymode`: indicates dm-verity enforcement
- `vbmeta.device_state`: indicates AVB metadata state

Observed:

- verity mode = enforcing
- root shell = enabled
- emulator supports privileged analysis



<img width="1830" height="307" alt="4" src="https://github.com/user-attachments/assets/d1bd13bc-6580-4853-a2bb-1b6e30ae9188" />



---

# 6. SU Binary Test

A direct `su` command was tested.

## Command

```bash
adb shell "su -c id"
```

## Result

```text
su: invalid uid/gid '-c'
```

This indicates:

- the shell is already running as root
- standalone `su` binary is unnecessary in emulator mode

---

# 7. Log Collection

System logs were exported for evidence.

## Command

```bash
adb logcat -d > logcat_root_check.txt
```

Generated file:

```text
logcat_root_check.txt
```
<img width="1072" height="84" alt="5" src="https://github.com/user-attachments/assets/b60b587c-a968-41cc-aa1d-c2cc691bcb9b" />

---

# 8. APK Installation

The application was installed using ADB.

## Command

```bash
adb install app-debug.apk
```

## Result

```text
Success
```



Place here:
<img width="1257" height="109" alt="6" src="https://github.com/user-attachments/assets/0790cb52-3d61-4eb7-8479-af65be282d2a" />



# 9. Application Testing

The application launched successfully.

Three simple test scenarios were performed.

---

## Scenario 1 – Open Home Screen

The emulator starts correctly and reaches the Android home screen.


<img width="491" height="911" alt="7" src="https://github.com/user-attachments/assets/3d2ef9a2-a680-49b6-818a-cdbcb25a68f6" />


---

## Scenario 2 – Search Feature

A search query (`test`) was entered to validate app interaction.


<img width="506" height="897" alt="8" src="https://github.com/user-attachments/assets/bf85cdc7-18d8-45f8-8ed0-ba1b534a48be" />

---

## Scenario 3 – Network Settings

Android network settings were opened to inspect connectivity.

<img width="479" height="938" alt="9" src="https://github.com/user-attachments/assets/b9cb7dfb-e0e9-419c-919e-f1915d17db1d" />




# 10. Rooting Definition

Rooting means obtaining superuser privileges on Android.

It allows:

- access to protected system areas
- modification of system files
- inspection of private application data
- execution of privileged commands

In a laboratory context, rooting is useful for observing how applications behave under privileged attack conditions.

---

# 11. Verified Boot

Verified Boot ensures that Android boots only trusted software.

## Main Purpose

To guarantee system integrity at startup.

## Chain of Trust

Each boot component verifies the authenticity of the next component before execution.

If one component is modified, the trust chain is broken.

---

# 12. Android Verified Boot (AVB)

AVB is the modern implementation of Verified Boot.

It adds:

- stronger integrity verification
- rollback protection
- boot metadata validation

This protects against loading older vulnerable system versions.

---

# 13. Risks Identified

| Risk | Description |
|------|-------------|
| Integrity loss | Security results may be biased |
| Data exposure | Root can access private files |
| System instability | Emulator may become unstable |
| Attack surface | Root expands privileges |
| Network leakage | Test traffic may escape |
| Persistence | Changes may survive reboot |
| Misconfiguration | Wrong conclusions possible |
| Traceability issues | Missing logs reduce reliability |

---

# 14. Defensive Measures

| Measure | Description |
|---------|-------------|
| Isolated environment | Dedicated emulator |
| Fictitious data | No personal information |
| Controlled APK | Trusted application only |
| Local testing | No external systems |
| Screenshots | Evidence collection |
| Logs | Command traceability |
| Reset after test | Clean state |
| Documentation | Repeatable audit |

---

# 15. OWASP MASVS References

## STORAGE-1

Sensitive information must be stored securely.

## NETWORK-1

All network communication must use secure TLS transport.

---

# 16. OWASP MASTG Test Ideas

Two practical tests:

- inspect shared preferences for cleartext sensitive data
- analyze runtime logs using `adb logcat`

---

# 17. Traceability Sheet

Collected evidence includes:

- ADB command outputs
- Root verification
- Verified Boot status
- APK installation logs
- Screenshots
- Logcat export

Artifacts:

```text
logcat_root_check.txt
```

---

# 18. Cleanup Procedure

The emulator must be reset after testing.

## Command

```bash
adb emu avd wipe-data
```

Or via Android Studio:

- Device Manager
- Wipe Data

This restores a clean testing environment.

---

# 19. Final Structure

Project organization:

```text
lab2/
│
├── README.md
├── logcat_root_check.txt
├── app-debug.apk
│
└── images/
    ├── adb_devices.png
    ├── adb_root.png
    ├── root_check.png
    ├── verified_boot.png
    ├── apk_install.png
    ├── avd_home.png
    ├── search_test.png
    ├── settings_test.png
    └── app_test.png
```

---

# Conclusion

This laboratory successfully demonstrated:

- Android emulator rooting
- Root privilege verification
- Verified Boot inspection
- APK deployment
- Functional testing
- Security analysis
- Controlled cleanup

The experiment provides a practical introduction to Android mobile security testing in a safe environment.
