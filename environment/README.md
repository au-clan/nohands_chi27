# Study environment

How the remote desktop participants used was built and instrumented. See `Dockerfile` in this
folder for the full build (credential/token lines redacted — see the note at the top of that
file and `supplement/QUESTIONS.md`).

## 1. Kasm Workspaces version and base image

- **Kasm Workspaces** (the platform serving the browser desktop): **1.15.0**, installed on a
  single Ubuntu Linux VPS.
- **Base container image**: `kasmweb/core-ubuntu-jammy:1.16.0` — Kasm's own minimal image
  (Ubuntu 22.04 LTS "Jammy" + VNC/noVNC session infrastructure), with nothing pre-installed beyond
  that; every desktop app is added by the study's own Dockerfile.

## 2. Per-tool versions and settings changed from default

| Tool | Version | Notes |
|---|---|---|
| **Cursor** | 3.14.7 | Installed from Cursor's own "latest" release endpoint at image-build time (not pinned in the repo) — a build-time cache-bust stamp forces a fresh download on every rebuild so "latest" cannot silently freeze on an old release. Signed in to a dedicated research account; the session is baked into the image (SQLite auth store), not entered by participants. |
| **VS Code** | 1.130.0 | Installed from Microsoft's `stable` apt channel (also not pinned). Used directly for the Mantra condition; both the Mantra and Cursor conditions also get `ms-python.python`, `ms-python.debugpy`, `dbaeumer.vscode-eslint`, `esbenp.prettier-vscode`, and a Java extension pack installed, for the multi-language demo workspace. |
| **Mantra** (VS Code extension) | `mishra7yash.mantra` v0.7.3 | Speech capture: spawns `ffmpeg -f pulse -i <source>` itself (not the browser's `getUserMedia`) — pointed at the system `ffmpeg` via `MANTRA_FFMPEG_PATH`, because the extension's bundled `ffmpeg-static` binary inside its VSIX is a macOS build and fails immediately in the Linux container. LLM backend: not controlled by this image — Mantra calls its own backend service. A small first-party companion extension (`study.mantra-autolayout` v0.0.1) is installed alongside it purely to move Mantra's panel into the VS Code secondary side bar (right) and force it to render there at startup, so the file explorer stays visible on the left. |
| **Serenade** | 2.0.2 | Installed from a pre-extracted AppImage (cross-arch AppImages can't self-execute during the build, so the repo carries the already-extracted contents and re-extracts by running `docker/scripts/extract-serenade.sh` when it needs refreshing). Auth: a pre-authenticated token is baked in so participants skip its login/welcome wizard. |

Approval/permission dialogs (e.g. Mantra's "Sensitive content detected" prompt, seen because a
task file contains a variable literally named `apiKey`) were left at their defaults — participants
are told in the task instructions to click through them.

## 3. Microphone routing (browser → container)

Kasm's own audio pipeline creates a PulseAudio source named `virtual_mic` inside the container the
moment the participant grants microphone access in Kasm's control panel (before that, the only
source is a dummy null-sink monitor). Serenade and Cursor pick this up automatically through the
browser's normal `getUserMedia` path. Mantra does not use that path — it spawns its own `ffmpeg`
process reading `-f pulse -i default`, and "default" can resolve to the dummy sink if the
participant hasn't enabled the mic yet by the time Mantra starts listening — so for the Mantra
condition the startup script explicitly waits for `virtual_mic` to appear, sets it as PulseAudio's
default source, and also passes it to Mantra directly via `MANTRA_AUDIO_INPUT` and a
`mantra.microphoneInput` VS Code setting, rather than relying on "default" resolving correctly.

## 4. Recording setup

- **Screen + audio**: `ffmpeg` captures the X11 display (`x11grab`, 15 fps) and the participant's
  microphone (via `parec` from whichever real PulseAudio source is present, silence otherwise) in
  parallel, muxed into 60-second segmented MKV files (H.264 + AAC) so a crash or a display-size
  change only loses one segment. A monitor loop restarts recording automatically on either.
- **Input events**: a Python logger (`pynput`-based) records every keystroke and mouse
  event to a per-session JSONL file.
- **Semantic markers**: session lifecycle events (`session_start`, `recording_start`,
  `reading_gate_confirmed`, `tool_launched`, mic-availability, task start/stop/done/skip, etc.)
  are appended to the *same* JSONL log as the keystroke/mouse events, sharing one schema
  (timestamp, session id, participant id, event type, detail) — so video, audio, and interaction
  streams can all be aligned on one timeline during analysis.

## 5. Task page server

A local Python HTTP server (`study-control.py`) runs on `127.0.0.1:7800` inside the container and
serves the interactive task page. At image-build time, a separate script (`render-tasks.py`)
converts the three per-condition task Markdown files into that interactive HTML: each task gets a
Start/Stop/Reset stopwatch plus Done/Skip buttons. Clicking any of those buttons both stops the
timer client-side and POSTs to the control server, which appends a `task_event` marker (action:
start/resume/stop/reset/done/skip) to the same JSONL log described above — giving
machine-readable task boundaries alongside the raw input/video/audio data.

## 6. Session time limit and enforcement

Participants are told the coding task itself is capped at **10 minutes** (soft cap — the task
instructions ask them to move on rather than being cut off by software), inside a total session
budget of **20–30 minutes** including the two surveys. **Not resolved in this pass:** the
supervisor's checklist also expects a 30-minute *hard* session/container time limit; no such
fixed cutoff was found in any of the container's own scripts (`init-study-session.sh`,
`study-control.py`, `app-guard.sh`) or in the credential service. It most likely lives in the Kasm
admin panel's per-workspace "Max Session Length" setting on the live server, which was not queried
in this pass — flagged in `supplement/QUESTIONS.md` for the researcher to confirm directly in the Kasm
admin UI.
