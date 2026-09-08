[CSC_4820_Basic_Dynamic_Analysis_Lab_with_Screenshots.md](https://github.com/user-attachments/files/31969517/CSC_4820_Basic_Dynamic_Analysis_Lab_with_Screenshots.md)
# Lab-3[CSC_4820_Basic_Dynamic_Analysis_Lab.md](https://github.com/user-attachments/files/31969458/CSC_4820_Basic_Dynamic_Analysis_Lab.md)
# Basic Dynamic Analysis Lab

## Behavioral Analysis of `csc-4820-sample-03.exe`

Based on the screenshots, the sample behaves primarily as a **keylogger
with persistence and network exfiltration capabilities**. The strongestevidence is the captured keystrokes, persistence through the Windows Run
key, and repeated connections to `10.16.103.203:8000`.

------------------------------------------------------------------------

## Process Activity

### 1. Does the process remain running or terminate immediately after execution?

**Answer:** The process **remains running after execution**.

The Process Monitor screenshots show `csc-4820-sample-03.exe` continuing
to perform registry and network operations over time. The keylogger
output also shows continuous keyboard activity being captured rather
than the program immediately terminating.

**Screenshot Evidence:** `Screenshot 2026-09-08 134936.png` and
`Screenshot 2026-09-08 135041.png`

<img width="1264" height="905" alt="Screenshot 2026-09-08 134936" src="https://github.com/user-attachments/assets/39906407-7ad8-4876-a2d1-09049723178a" />

------------------------------------------------------------------------

### 2. What child processes, if any, does the main process create?

**Answer:** The malware creates **`conhost.exe` (Console Window Host)**
as a child process.

Process Monitor shows:

`Process Create -> C:\Windows\System32\Conhost.exe`

This is consistent with the malware running or interacting with a
console.

**Screenshot Evidence:** `Screenshot 2026-09-08 134936.png`

<img width="1264" height="905" alt="Screenshot 2026-09-08 134936" src="https://github.com/user-attachments/assets/39906407-7ad8-4876-a2d1-09049723178a" />

------------------------------------------------------------------------

### 3. What privilege level does the process run under (user vs. system)?

**Answer:** The available evidence indicates that the malware runs at
the **current user's privilege level**, rather than SYSTEM.

One strong indicator is its use of:

`HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`

`HKCU` stands for **HKEY_CURRENT_USER**, meaning the persistence
mechanism applies to the currently logged-in user.

There is no screenshot showing the malware executing as
`NT AUTHORITY\SYSTEM`, so SYSTEM-level privileges should not be claimed
from the available evidence.

**Screenshot Evidence:** `Screenshot 2026-09-08 135430.png` and
`Screenshot 2026-09-08 134936.png`

<img width="1010" height="438" alt="Screenshot 2026-09-08 135430" src="https://github.com/user-attachments/assets/ff9fa850-6611-4210-84f7-505190fbe2ee" />

<img width="1264" height="905" alt="Screenshot 2026-09-08 134936" src="https://github.com/user-attachments/assets/39906407-7ad8-4876-a2d1-09049723178a" />

------------------------------------------------------------------------

## File System Interactions

### 6. What files does the malware create, modify, or delete?

**Answer:** The provided screenshots do **not clearly demonstrate a
specific file being created, modified, or deleted**.

The strongest dynamic evidence instead shows registry modifications,
process creation, keylogging, and network communication. Therefore, a
specific created file should not be claimed without additional Process
Monitor evidence using operations such as `CreateFile`, `WriteFile`,
`SetDispositionInformationFile`, or `DeleteFile`.

------------------------------------------------------------------------

### 7. What is the content and purpose of any created files?

**Answer:** **No created file is clearly identified in the provided
screenshots**, so its contents and purpose cannot be confirmed.

The malware clearly captures keystrokes, but the screenshots do not
establish that those keystrokes are stored in a local file before being
transmitted.

**Screenshot Evidence:** `Screenshot 2026-09-08 134248.png`

<img width="1058" height="494" alt="Screenshot 2026-09-08 134248" src="https://github.com/user-attachments/assets/2ee9e3a4-7672-4d08-9222-77ae171c16bd" />

------------------------------------------------------------------------

### 8. Where are these files located?

**Answer:** **No file location can be confirmed from the current
evidence** because the screenshots do not clearly show a malicious file
being created.

A Process Monitor filter for the malware with operations such as
`CreateFile` and `WriteFile` would be needed to determine whether it
writes into `%TEMP%`, `%APPDATA%`, the user's profile, or another
directory.

------------------------------------------------------------------------

### 9. Are there any file operations that occur at regular intervals?

**Answer:** **No regular file operations can be confirmed from the
provided screenshots.**

There is repeated network activity, but that should not be confused with
periodic file-system activity.

------------------------------------------------------------------------

## Registry Modifications

### 11. What registry keys does the malware create or modify?

**Answer:** The most important registry modification is:

`HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\SystemMonitor`

The malware creates/modifies a Run-key value called **`SystemMonitor`**.

Process Monitor also shows modifications/access involving Windows
Internet Settings, including entries associated with:

-   `Cache\Content\CachePrefix`
-   `Cache\Cookies\CachePrefix`
-   `Cache\History\CachePrefix`
-   `ZoneMap\ProxyBypass`
-   `ZoneMap\IntranetName`
-   `ZoneMap\UNCAsIntranet`
-   `ZoneMap\AutoDetect`

The Run-key modification is the most significant because it establishes
persistence.

**Screenshot Evidence:** `Screenshot 2026-09-08 134936.png`,
`Screenshot 2026-09-08 135041.png`, and
`Screenshot 2026-09-08 135430.png`

<img width="1264" height="905" alt="Screenshot 2026-09-08 134936" src="https://github.com/user-attachments/assets/39906407-7ad8-4876-a2d1-09049723178a" />

------------------------------------------------------------------------

### 12. Does it attempt persistence through Run keys or other autostart mechanisms?

**Answer:** **Yes.** The malware establishes persistence through the
Windows \*\*CurrentVersion`\Run*`{=tex}\* key.

The value is:

`HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\SystemMonitor`

Autoruns confirms that **`SystemMonitor`** appears under:

`HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`

This means Windows can automatically launch the malware when that user
logs in.

**Screenshot Evidence:** `Screenshot 2026-09-08 135430.png`

<img width="1010" height="438" alt="Screenshot 2026-09-08 135430" src="https://github.com/user-attachments/assets/ff9fa850-6611-4210-84f7-505190fbe2ee" />

------------------------------------------------------------------------

## Basic Network Behavior

### 15. Does the malware initiate any network connections?

**Answer:** **Yes.** The malware repeatedly initiates network
communication.

Process Monitor shows numerous `TCP Send` events from the infected VM
to:

`10.16.103.203:8000`

VirusTotal also identifies the contacted URL:

`http://10.16.103.203:8000/upload`

**Screenshot Evidence:** `Screenshot 2026-09-08 134936.png`,
`Screenshot 2026-09-08 135041.png`, and
`Screenshot 2026-09-03 162518.png`

<img width="1262" height="616" alt="Screenshot 2026-09-08 135041" src="https://github.com/user-attachments/assets/43f54266-7177-4f4e-8036-2adadcc2d9de" />

------------------------------------------------------------------------


<img width="1261" height="722" alt="Screenshot 2026-09-03 162518" src="https://github.com/user-attachments/assets/875b345c-aa02-4869-9649-b39a0569c418" />
### 16. What protocols does it use?

**Answer:** The malware uses **TCP and HTTP**.

Process Monitor directly shows `TCP Send` operations, while VirusTotal
identifies:

`http://10.16.103.203:8000/upload`

Therefore, HTTP application traffic is being carried over TCP.

**Screenshot Evidence:** `Screenshot 2026-09-03 162518.png` and
`Screenshot 2026-09-08 135041.png`

<img width="1261" height="722" alt="Screenshot 2026-09-03 162518" src="https://github.com/user-attachments/assets/875b345c-aa02-4869-9649-b39a0569c418" />

------------------------------------------------------------------------


<img width="1262" height="616" alt="Screenshot 2026-09-08 135041" src="https://github.com/user-attachments/assets/43f54266-7177-4f4e-8036-2adadcc2d9de" />
### 17. What destination ports does it connect to?

**Answer:** The primary malicious destination port shown is:

**TCP port 8000**

The local/source ports change (`3262`, `3270`, `3276`, `3278`, `3281`,
`3283`, etc.), while the destination remains:

`10.16.103.203:8000`

**Screenshot Evidence:** `Screenshot 2026-09-08 135041.png`

<img width="1262" height="616" alt="Screenshot 2026-09-08 135041" src="https://github.com/user-attachments/assets/43f54266-7177-4f4e-8036-2adadcc2d9de" />

------------------------------------------------------------------------

### 18. Can you identify the remote IP addresses or domains?

**Answer:** Yes. The most significant remote destination is:

**`10.16.103.203`**

with the URL:

`http://10.16.103.203:8000/upload`

VirusTotal additionally lists connections involving:

-   `162.159.36.2`
-   `184.27.218.92`
-   `192.168.0.62`
-   `20.99.133.109`
-   `www.microsoft.com`

However, **`10.16.103.203:8000` is the strongest candidate for the
malware's data collection/exfiltration server** because Process Monitor
directly shows repeated TCP transmissions to it and VirusTotal
identifies its `/upload` endpoint.

**Screenshot Evidence:** `Screenshot 2026-09-03 162518.png` and
`Screenshot 2026-09-08 135041.png`

<img width="1261" height="722" alt="Screenshot 2026-09-03 162518" src="https://github.com/user-attachments/assets/875b345c-aa02-4869-9649-b39a0569c418" />

------------------------------------------------------------------------


<img width="1262" height="616" alt="Screenshot 2026-09-08 135041" src="https://github.com/user-attachments/assets/43f54266-7177-4f4e-8036-2adadcc2d9de" />
## Communication Patterns

### 19. Is network communication continuous or periodic?

**Answer:** The communication appears to be **repeated/periodic rather
than a single connection**.

Process Monitor shows numerous TCP Send events to the same destination
over time. This indicates that the malware repeatedly sends data to
`10.16.103.203:8000`.

**Screenshot Evidence:** `Screenshot 2026-09-08 134936.png` and
`Screenshot 2026-09-08 135041.png`

<img width="1262" height="616" alt="Screenshot 2026-09-08 135041" src="https://github.com/user-attachments/assets/43f54266-7177-4f4e-8036-2adadcc2d9de" />

------------------------------------------------------------------------

### 20. What triggers network activity?

**Answer:** The evidence suggests that network activity is **event-based
and associated with captured keyboard activity**, although the exact
transmission timer/trigger cannot be conclusively determined from the
screenshots alone.

The malware captures keyboard input continuously, and repeated TCP
transmissions are then observed to the `/upload` server. This is
consistent with a keylogger periodically or event-wise sending collected
keystrokes.

**Screenshot Evidence:** `Screenshot 2026-09-08 134248.png` and
`Screenshot 2026-09-08 135041.png`

<img width="1058" height="494" alt="Screenshot 2026-09-08 134248" src="https://github.com/user-attachments/assets/2ee9e3a4-7672-4d08-9222-77ae171c16bd" />

------------------------------------------------------------------------

### 21. What data appears to be transmitted?

**Answer:** The malware appears to transmit **captured
keystrokes/keylogging data**.

The keylogging output visibly records entries including:

-   `[ENTER]`
-   `[BACKSPACE]`
-   `[LEFT]`
-   `[RIGHT]`
-   `[UP]`
-   `[DOWN]`
-   `[TAB]`

as well as actual typed text and commands.

VirusTotal independently supports this conclusion: the sample is labeled
**`trojan.keylogger/misc`**, and the YARA result states that it detects
a potential keylogger that captures keystrokes. The imported USER32
functions such as `SetWindowsHookExA`, `GetAsyncKeyState`,
`GetKeyState`, and `GetKeyNameTextA` also strongly support keyboard
monitoring.

**Screenshot Evidence:** `Screenshot 2026-09-08 134248.png`,
`Screenshot 2026-09-03 162323.png`, `Screenshot 2026-09-03 162504.png`,
and `Screenshot 2026-09-03 162543.png`

<img width="1058" height="494" alt="Screenshot 2026-09-08 134248" src="https://github.com/user-attachments/assets/2ee9e3a4-7672-4d08-9222-77ae171c16bd" />

------------------------------------------------------------------------


<img width="1256" height="903" alt="Screenshot 2026-09-03 162323" src="https://github.com/user-attachments/assets/a9af6bc8-e9c7-4ff7-9e81-d65d895d5689" />

<img width="1158" height="375" alt="Screenshot 2026-09-03 162504" src="https://github.com/user-attachments/assets/a9d4667c-ed61-4e52-91d9-7e3efdb1176c" />

<img width="1208" height="352" alt="Screenshot 2026-09-03 162543" src="https://github.com/user-attachments/assets/5ee09a35-d153-4ca9-b37c-693aa773a84f" />
### 22. Does the malware receive any commands or data from remote systems?

**Answer:** **There is no clear evidence in the provided screenshots
that the malware receives commands from the remote server.**

The Process Monitor evidence shows repeated **`TCP Send`** operations,
which supports outbound data exfiltration. There are no corresponding
screenshots clearly demonstrating `TCP Receive` operations or
command-and-control instructions being received.

Therefore, the safest conclusion is that the observed behavior is
primarily **outbound keylogging data exfiltration**, while remote
command execution cannot be confirmed from the current evidence.

**Screenshot Evidence:** `Screenshot 2026-09-08 135041.png`

<img width="1262" height="616" alt="Screenshot 2026-09-08 135041" src="https://github.com/user-attachments/assets/43f54266-7177-4f4e-8036-2adadcc2d9de" />

------------------------------------------------------------------------

## Overall Behavioral Conclusion

`csc-4820-sample-03.exe` behaves as a **persistent network-enabled
keylogger**. It captures keyboard input using Windows USER32 keyboard
functions, establishes user-level persistence through
`HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\SystemMonitor`, and
repeatedly communicates with **`10.16.103.203` on TCP port `8000`**.
VirusTotal identifies an `/upload` endpoint, strongly suggesting that
captured information is uploaded to that server.

The evidence does **not** currently establish specific malicious file
creation/deletion or inbound command-and-control behavior, so those
portions should be reported as **not confirmed** rather than inferred.
