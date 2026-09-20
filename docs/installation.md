# Installing and running 21-simulator

Windows x64. No Python, no installer, no account, no network access needed.

---

## 1. Download

Get `21-simulator-v2.1.1-windows-x64.zip` from the
[latest release](https://github.com/shanla-gh/21-simulator/releases/latest).

Releases on this repository are the only official source. There is no other
download location, and no installer that asks for anything.

## 2. Verify the download

The release page publishes a SHA-256 for the archive. Check it before running
anything:

```powershell
Get-FileHash .\21-simulator-v2.1.1-windows-x64.zip -Algorithm SHA256
```

Compare the result with the value on the release page. For v2.1.1 it is:

```
5fb89f2231e4f414e02c5a15db648b587097c4479843b9afb6f3d509ed52a9ad
```

If they differ, do not run it — delete it and download again.

## 3. Extract

Right-click the archive → **Extract All**, or use any archive tool. Put the
folder wherever you like: Documents, Desktop, a USB stick. Paths with spaces are
fine. Administrator rights are not required, and nothing is written outside the
folder you choose.

## 4. Run

Open the extracted folder and run **`21-simulator.exe`**.

A small console window appears and prints the address it is listening on, then
your default browser opens at it. Use the application in the browser; the
console window is what keeps it running.

**Windows SmartScreen.** The build is not code-signed, so Windows will likely
show "Windows protected your PC" the first time. This is the expected behaviour
for an unsigned application from a small publisher — it is not a statement that
anything is wrong with the file, and it is also not a guarantee that anything is
right with it. That is what the checksum in step 2 is for. To continue:
**More info → Run anyway**.

## 5. Stop it

Close the console window, or press **Ctrl+C** in it. The server shuts down and
releases its port. Closing only the browser tab leaves the server running; close
the console window to stop it completely.

---

## Command-line options

Run these from a terminal in the extracted folder.

| Command | What it does |
| --- | --- |
| `21-simulator.exe` | start, and open a browser |
| `21-simulator.exe --serve` | start without opening a browser |
| `21-simulator.exe --port 9000` | use a specific port |
| `21-simulator.exe --no-browser` | start normally, but do not open a browser |
| `21-simulator.exe --diagnostics` | print a short, shareable report |
| `21-simulator.exe --version` | print the version |
| `21-simulator.exe --help` | list every option |

If the preferred port is already in use — a second copy, or another program on
8000 — the application takes another free port **on the same interface** and
says so. It never moves to a different network interface to find a free port.

---

## The security model, stated plainly

- **It listens on 127.0.0.1 only.** Only programs on your own computer can reach
  it. Binding any other address requires an explicit `--allow-remote`, because
  the local server has **no authentication**: anything that can reach it can
  drive a session.
- **Nothing is uploaded.** The mathematics runs locally. The research-
  contribution feature is off unless you switch it on, and a destination has to
  be configured before it could send anything at all.
- **No account, no licence key, no telemetry on by default.**
- **It writes nothing outside its own folder** during normal use.
- **Responses carry a strict content-security-policy**, are marked no-store, and
  refuse framing. Static files are served only from the bundled web folder.
- **Faults are not shown to the browser.** An unexpected error returns a plain
  message; the detail goes to the console window you started it from.

What this is *not*: audited, hardened against a hostile local user, or a
security product. It is a local research application built to behave sensibly.

---

## Diagnostics

`21-simulator.exe --diagnostics` prints a JSON report: version, whether it is a
packaged build, Python and platform versions, whether the bundled resources were
found, which research artifacts are present, and whether loopback is available.

It deliberately does not include environment variables, credentials, your files,
or anything you have analysed. Paths are shortened so your home directory is not
in it. It is safe to paste into an issue.

---

## Troubleshooting

**The browser does not open.** The console prints the address — open it
manually. This is normal if no default browser is configured.

**"This build is missing its web interface".** The extraction was incomplete, or
files were moved out of the folder. Extract the archive again, keeping the
folder intact.

**Antivirus quarantines it.** Unsigned PyInstaller executables are a common
false positive. Verify the checksum, then allow it if you are satisfied — or do
not run it. Both are reasonable.

**A page is slow.** Some states are genuinely expensive: a deep recursion on a
six-deck shoe can take a second or two the first time. The interface stays
responsive and marks what it is waiting for; repeated views of the same state
are immediate.

**It will not start on a work machine.** Some managed environments block
unsigned executables entirely. There is no workaround from this side.

---

## Running from source

The source is not publicly distributed, so this section applies only to those
who already have it: `python -m src.cli serve --open` runs the same application
the packaged build runs.
