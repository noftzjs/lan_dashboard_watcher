# LAN Dashboard watcher

A small tray program for Windows that reads the LanDashboard addon's SavedVariables file and forwards new events to the group's dashboard server.

## What it is

`savedvars_watcher.py` is the whole program. It runs in the system tray, polls the addon's `LanDashboard.lua` SavedVariables file every few seconds, and POSTs any lines it hasn't sent yet to the dashboard's `/api/log-update` endpoint. It ships to players as a single `savedvars_watcher.exe` built with PyInstaller from this file, with a config file beside it that the dashboard's setup page generates.

This repository is published so players can read what runs on their PC. It is developed inside the dashboard project and mirrored here on each release.

## What it reads and sends

- **Reads:** one file, the addon's SavedVariables, found by scanning every drive for `World of Warcraft/_*_/WTF/Account/*/SavedVariables/LanDashboard.lua`. With several accounts it follows the most recently written one. It keeps checking: if another install's or account's file starts changing more recently (the game moving from a beta folder to the live one, or you switching accounts), it follows that one and says so in the log. A path set in the config file is never switched away from automatically.
- **Sends:** the addon's queued lines, verbatim, to the server address in its config, over HTTPS, with the shared ingestion token as a header. It also sends two random ids so the operator can group one person's characters: a watcher id generated on first run and a hash of the account folder name. Neither is your account name or email. It also sends its own version and the addon's, so the operator can tell who is on an old build.
- **Keeps locally, next to the exe:** `savedvars_watcher_config.json` (server address and token), `.savedvars_watcher_state.json` (how far it has sent), `savedvars_watcher_backup.csv` (a copy of everything sent, so nothing is lost if the server is down), and `savedvars_watcher.log`.

It never reads chat logs, the game's memory, or anything outside that one file. The older `log_watcher.py` is a fallback that tails `WoWChatLog.txt`; it is kept for reference and is not what the setup page distributes.

## One copy at a time

Only one watcher runs per PC. Starting a copy from a different folder, such as a newer download, ends the one already running and takes over, and keeps Start with Windows pointed at the copy you just ran. Starting the same exe twice does nothing but tell you it is already running.

## Tray menu

Status with a green, amber or red dot, open the dashboard, open the log, open the folder, **Start with Windows** (on by default for the packaged exe, switch it off here or in Task Manager's Startup tab), **Choose WoW folder...**, and Quit. Problems raise one Windows notification each, not one per poll.

## Run from source

```
python -m venv .venv
.venv\Scripts\pip install -r requirements-watcher.txt
.venv\Scripts\python savedvars_watcher.py --server-url https://<dashboard>/api/log-update --ingestion-token <token>
```

Run from source it prints to the terminal and may prompt. Add `--tray` for the tray-icon mode the packaged exe always uses. Without `--savedvars-path` it scans for the file.

## Build the exe

```
.venv\Scripts\python -m PyInstaller --onefile --noconsole --name savedvars_watcher --distpath dist --workpath build --specpath build savedvars_watcher.py
```

The exe is unsigned, so Windows SmartScreen warns on first run. The setup page shows the SHA-256 of the file it serves so you can check what you downloaded.

## Tests

```
.venv\Scripts\pip install pytest
.venv\Scripts\python -m pytest
```

The tests here cover the startup and timing decisions. The end-to-end tests that need the server live in the dashboard repository.
