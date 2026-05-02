# Installation

WS Terminal ships as a Windows desktop app. No installer wizard, no
registry edits — you download a zip, extract it to a folder of your
choice, and run the .exe.

This page covers getting the files onto your machine. The
[first-run wizard walkthrough](first-run.md) covers what happens when
you actually launch it.

## System requirements

| Requirement   | Minimum            | Recommended       |
|---------------|--------------------|-------------------|
| OS            | Windows 10 64-bit  | Windows 11 64-bit |
| RAM           | 8 GB               | 16 GB             |
| Disk          | 500 MB             | 1 GB free         |
| Internet      | Reliable broadband | Wired connection  |

Mac and Linux are not supported in v1. A Mac version is on the roadmap.

## Step 1 — Make sure prerequisites are in place

Before you install the bot, you should already have:

1. **An IBKR account with futures permissions enabled.** If not, see
   [Requirements → Broker Account](https://ws-trading.co/requirements.html#01-broker-account)
   on the marketing site. Approval takes 1-3 business days, so start
   it before you install.
2. **An active WS Terminal subscription** (Basic or Pro) on Whop.
   You'll sign in to Whop from inside the bot's first-launch wizard,
   so make sure you remember which Whop account you used.
3. **Either IB Gateway or TWS installed** — see
   [IB Gateway setup](../broker/ib-gateway.md) or
   [TWS setup](../broker/tws.md). The bot connects through one of
   these, so it needs to exist on the same machine.

## Step 2 — Download

1. Go to <https://github.com/bbenjamin1116/wsterminal-releases/releases/latest>
2. Under **Assets**, click `WSTerminal-X.Y.Z.zip` (the version number
   updates with each release)
3. Save the zip to your Downloads folder

The download is around 110 MB.

!!! info "Why GitHub for the download?"
    GitHub Releases gives us versioned, hash-verified download URLs
    that we can safely link from the bot's auto-update check. Whop
    handles your subscription; GitHub hosts the binary. Two clean
    surfaces, one product.

## Step 3 — Extract

1. Right-click the downloaded zip → **Extract All...**
2. Pick a folder to extract to. Recommended:
   `C:\WSTerminal\` (clean, easy to find, no spaces in the path)
3. After extraction you'll have a `WSTerminal` folder containing
   `WSTerminal.exe`, a `static\` folder, a `config_product.py` file,
   plus a few hundred supporting files

!!! warning "Don't extract into Program Files"
    Avoid `C:\Program Files\` or `C:\Program Files (x86)\` — Windows
    locks those directories down and the bot can't write its temp
    files there. Stick with `C:\WSTerminal\` or any other normal
    folder under your user profile.

## Step 4 — Run

1. Open the extracted folder
2. Double-click **WSTerminal.exe**

A console window opens with the WS Terminal startup banner. Within a
few seconds it'll print:

```
============================================================
  WS Terminal — W.S. Trading Co.
============================================================
  App directory: C:\WSTerminal
  Dashboard:     http://localhost:8080
  WebSocket:     ws://localhost:8080/ws
============================================================
```

Open your browser and go to <http://localhost:8080>.

The dashboard appears with a first-launch wizard overlay — that's
where you sign in with Whop and configure the bot. See the
[first-run wizard walkthrough](first-run.md) for what to do next.

!!! warning "Windows Defender / SmartScreen"
    On first launch, Windows may pop up a "Windows protected your PC"
    SmartScreen warning. WS Terminal's .exe isn't signed with a paid
    code-signing certificate (we're working on it), so SmartScreen is
    cautious about new downloads.

    To proceed: click **More info** → **Run anyway**. The bot is safe
    — the warning is purely about the certificate, not the contents.
    You only see this once per version.

## Where the bot stores your data

Most files in the install folder are static (the .exe and its
dependencies). Your personal data lives in
**`%APPDATA%\WSTerminal\`**, which on most machines resolves to
`C:\Users\<your-username>\AppData\Roaming\WSTerminal\`.

This folder contains:

| File                       | What it is                          |
|----------------------------|-------------------------------------|
| `license.json`             | Your activated license key          |
| `trade_log.json`           | Per-trade history                   |
| `presets.json`             | Custom presets (Pro only)           |
| `acknowledgments.json`     | Audit log of legal acknowledgments  |
| `last_active_config.json`  | Active config snapshot              |

Putting it in `%APPDATA%` means it survives across .exe updates —
when you download a new version of WS Terminal and extract it over
the old folder, your license + trade history + presets stay put.

## Updating the bot

When a new version ships, the dashboard's About page surfaces a
"Check for updates" button. Click it → the bot downloads the new zip
URL → unzip into the same folder (or a fresh one), restart.

Detailed walkthrough on the [Updates page](../updates.md).

## Uninstalling

1. Close the bot (Ctrl+C in the console window, or close the window)
2. Delete the `C:\WSTerminal` folder
3. To also remove your license + trade log, delete
   `C:\Users\<your-username>\AppData\Roaming\WSTerminal\`

That's it — no registry entries, no system services.

## Troubleshooting

### Double-click does nothing

Try opening Command Prompt, navigate to the install folder
(`cd C:\WSTerminal`), and run `WSTerminal.exe` from there. If
there's an error, it'll print to the console.

### Console window opens then immediately closes

Usually means another instance of WS Terminal is already running and
holding port 8080. Look for a `WSTerminal.exe` process in Task
Manager → Background processes, end it, then try again.

### "DLL not found" or "missing dependency" error on launch

The zip didn't extract cleanly — some of the supporting files are
missing. Re-download the zip, extract to a fresh folder, try again.

### Browser shows "Can't reach this page" at localhost:8080

Wait a few seconds — the bot takes 2-3 seconds after the console
banner appears before the web server is ready. If it's still not
loading after 10 seconds, check the console window for error
messages.

## Where to go next

- [First-run wizard walkthrough](first-run.md) — sign in with Whop,
  configure your broker connection
- [IB Gateway setup](../broker/ib-gateway.md) — if you haven't
  configured your broker yet
