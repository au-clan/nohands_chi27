# NOTE (added for anonymous review): three COPY instructions below that baked live
# authentication material (a signed-in Cursor session and a Serenade auth token) into
# the image have been replaced with single-line comments. Nothing else in this file was
# changed. See supplement/QUESTIONS.md.

FROM kasmweb/core-ubuntu-jammy:1.16.0

# Kasm core image: Ubuntu 22.04 + VNC/noVNC session infrastructure only.
# No pre-installed desktop apps — we add exactly what the study needs.
# Home user: kasm-user (/home/kasm-user)

USER root

ENV DEBIAN_FRONTEND=noninteractive \
    HOME=/home/kasm-user

# URL of the study server (proxied through Kasm's nginx).
# The container uses this to resolve PARTICIPANT_ID at startup via /participant.
ARG STUDY_SERVER_URL=https://167.233.84.165
ENV STUDY_SERVER_URL=${STUDY_SERVER_URL}

# ── Desktop environment + system dependencies ─────────────────────────────────
RUN apt-get update -o Acquire::Retries=3 \
    && apt-get install -y --no-install-recommends \
        xfce4 xfce4-terminal \
        python3 python3-dev python3-pip python3-venv \
        curl wget ca-certificates gnupg \
        openjdk-17-jdk \
        build-essential git cmake \
        pulseaudio pulseaudio-utils \
        ffmpeg \
        x11-utils \
        xdotool wmctrl xclip \
        zenity \
        mousepad \
        evince \
        libcairo2-dev libgirepository1.0-dev gir1.2-appindicator3-0.1 \
        unzip zip jq vim nano \
    && rm -rf /var/lib/apt/lists/* \
    && pip3 install --no-cache-dir pynput

# ── Firefox (REAL .deb from Mozilla apt repo — NOT the snap) ──────────────────
# On Ubuntu 22.04, `apt install firefox` installs a transitional stub that pulls
# in the Firefox *snap*. Snaps cannot run inside a Kasm/Docker container (no
# snapd/squashfs), so launching it fails with "Input/output error". We install
# Mozilla's genuine .deb instead and pin it above the Ubuntu snap stub so the
# real /usr/bin/firefox always wins. Everything downstream (helpers.rc, the
# xdg-open shim, init-study-session.sh, complete-study.sh) keeps using `firefox`.
RUN install -d -m 0755 /etc/apt/keyrings \
    && wget -q https://packages.mozilla.org/apt/repo-signing-key.gpg \
        -O /etc/apt/keyrings/packages.mozilla.org.asc \
    && echo "deb [signed-by=/etc/apt/keyrings/packages.mozilla.org.asc] https://packages.mozilla.org/apt mozilla main" \
        > /etc/apt/sources.list.d/mozilla.list \
    && printf 'Package: *\nPin: origin packages.mozilla.org\nPin-Priority: 1000\n' \
        > /etc/apt/preferences.d/mozilla \
    && apt-get update -o Acquire::Retries=3 \
    && apt-get install -y firefox \
    && rm -rf /var/lib/apt/lists/* \
    && test -x /usr/bin/firefox

# ── Node.js 24 (required by demo-ts's npm install below) ──────────────────────
RUN curl -fsSL https://deb.nodesource.com/setup_24.x | bash - \
    && apt-get install -y --no-install-recommends nodejs \
    && rm -rf /var/lib/apt/lists/* \
    && node --version && npm --version

# ── VS Code ────────────────────────────────────────────────────────────────────
RUN wget -qO- https://packages.microsoft.com/keys/microsoft.asc \
        | gpg --dearmor > /usr/share/keyrings/microsoft.gpg \
    && echo "deb [arch=amd64 signed-by=/usr/share/keyrings/microsoft.gpg] \
        https://packages.microsoft.com/repos/code stable main" \
        > /etc/apt/sources.list.d/vscode.list \
    && apt-get update && apt-get install -y code \
    && rm -rf /var/lib/apt/lists/* \
    && sed -i 's|^Exec=/usr/share/code/code|Exec=/usr/share/code/code --no-sandbox --user-data-dir /home/kasm-user/.config/Code|g' \
        /usr/share/applications/code.desktop \
    && sed -i 's|^Exec=/usr/share/code/code|Exec=/usr/share/code/code --no-sandbox --user-data-dir /home/kasm-user/.config/Code|g' \
        /usr/share/applications/code-url-handler.desktop 2>/dev/null || true

# ── Cursor ─────────────────────────────────────────────────────────────────────
# Installs the CURRENT release at build time — no pinned .deb in the repo.
# CURSOR_BUILD_ID exists solely to bust Docker's layer cache: without it this RUN
# is reused forever and "latest" silently freezes at whatever was current on the
# first build. deploy.sh and rebuild.sh pass a fresh stamp on every invocation.
# The installed version is recorded to /opt/study/cursor-version so a session can
# be attributed to an exact Cursor build during analysis — worth having, since
# Cursor's agent behaviour changes materially between releases.
ARG CURSOR_BUILD_ID=unset
RUN curl -fsSL "https://api2.cursor.sh/updates/download/golden/linux-x64-deb/cursor/latest" \
        -o /tmp/cursor.deb \
    && mkdir -p /opt/study \
    && dpkg-deb -f /tmp/cursor.deb Version > /opt/study/cursor-version \
    && printf '%s\n' "$CURSOR_BUILD_ID" > /opt/study/cursor-build-id \
    && echo "installing Cursor $(cat /opt/study/cursor-version)" \
    && dpkg -i /tmp/cursor.deb && rm /tmp/cursor.deb \
    && printf '#!/bin/bash\n[ -f /home/kasm-user/.study-session-env ] && source /home/kasm-user/.study-session-env\nexport XAUTHORITY=${XAUTHORITY:-/home/kasm-user/.Xauthority}\nexec cursor --no-sandbox --disable-gpu --disable-dev-shm-usage --no-zygote "$@"\n' > /usr/local/bin/cursor-launch \
    && chmod +x /usr/local/bin/cursor-launch \
    && find /usr/share/applications -name "cursor*.desktop" \
        -exec sed -i 's|^Exec=cursor|Exec=cursor-launch|g' {} \;

# ── Serenade ───────────────────────────────────────────────────────────────────
# Pre-extracted from the AppImage (AppImage can't self-execute during cross-arch builds).
# To refresh: run docker/scripts/extract-serenade.sh to regenerate docker/serenade-extracted/
COPY docker/serenade-extracted /opt/serenade/serenade-extracted
RUN chmod -R a+rX /opt/serenade/serenade-extracted \
    && printf '#!/bin/bash\n[ -f /home/kasm-user/.study-session-env ] && source /home/kasm-user/.study-session-env\nexport APPDIR=/opt/serenade/serenade-extracted\nexec "$APPDIR/AppRun" --no-sandbox --disable-gpu --disable-dev-shm-usage "$@" 2>>/tmp/serenade.log\n' \
        > /usr/local/bin/serenade \
    && chmod +x /usr/local/bin/serenade \
    && printf '[Desktop Entry]\nVersion=1.0\nType=Application\nName=Serenade\nComment=Voice coding assistant\nExec=/usr/local/bin/serenade\nIcon=/opt/serenade/serenade-extracted/resources/app/build/icon.png\nTerminal=false\nCategories=Accessibility;Development;\n' \
        > /usr/share/applications/Serenade.desktop

# ── VS Code extensions ─────────────────────────────────────────────────────────
COPY docker/scripts/install-extensions.sh /tmp/install-extensions.sh
RUN chmod +x /tmp/install-extensions.sh \
    && su kasm-user -c "HOME=/home/kasm-user /tmp/install-extensions.sh"

# ── Mantra: point it at the system ffmpeg ──────────────────────────────────────
# Mantra captures the microphone by spawning ffmpeg (`ffmpeg -f pulse -i default`),
# NOT the browser getUserMedia path that Serenade/Cursor use. It ships a bundled
# `ffmpeg-static` binary inside a *universal* VSIX (targetPlatform: none), so the
# linux/amd64 container receives the author's macOS build — a Mach-O arm64 binary
# that dies with "Exec format error". Mantra's resolver prefers that bundled
# binary (it exists on disk) over the system ffmpeg, so mic capture silently fails
# while Serenade/Cursor keep working. MANTRA_FFMPEG_PATH is the first thing its
# resolver checks — point it at the real ELF ffmpeg (apt-installed above, with
# `DE pulse` input support) so it never touches the broken bundled binary.
ENV MANTRA_FFMPEG_PATH=/usr/bin/ffmpeg

# ── Companion extension: reveal Mantra in the secondary side bar at startup ─────
# Mantra's webview view activates lazily (onView:mantra.sidebar) and never renders
# on its own once relocated to the aux bar. This tiny extension activates on
# startup and calls `mantra.sidebar.focus`, which opens the aux bar (right side)
# AND triggers Mantra's activation, so the panel shows on the right with the file
# explorer still on the left. Packaged into a .vsix at build time and installed.
COPY docker/config/code/companion /tmp/companion
RUN cd /tmp/companion && zip -r -q -X /tmp/mantra-autolayout.vsix '[Content_Types].xml' extension.vsixmanifest extension \
    && su kasm-user -c "HOME=/home/kasm-user code --install-extension /tmp/mantra-autolayout.vsix --force --no-sandbox --user-data-dir /home/kasm-user/.config/Code" \
    && rm -rf /tmp/companion /tmp/mantra-autolayout.vsix

# ── Study logging: input event logger + screen recorder ───────────────────────
COPY docker/scripts/input-logger.py /usr/local/bin/input-logger.py
COPY docker/scripts/start-recording.sh /usr/local/bin/start-recording.sh
COPY docker/scripts/study-control.py /usr/local/bin/study-control.py
COPY docker/scripts/tile-study-windows.sh /usr/local/bin/tile-study-windows.sh
COPY docker/scripts/app-guard.sh /usr/local/bin/app-guard.sh
RUN chmod +x /usr/local/bin/input-logger.py /usr/local/bin/start-recording.sh /usr/local/bin/study-control.py /usr/local/bin/tile-study-windows.sh /usr/local/bin/app-guard.sh

# ── VS Code user settings ──────────────────────────────────────────────────────
RUN mkdir -p /home/kasm-user/.config/Code/User
COPY docker/config/vscode-settings.json /home/kasm-user/.config/Code/User/settings.json

# ── Mantra: pre-seed VS Code layout (Mantra panel → secondary / right side bar) ─
# Relocates Mantra's view container out of the LEFT activity bar into the aux bar
# (right) so opening it no longer hides the file explorer. Captured from a real
# "Move To → Secondary Side Bar" action on this VS Code build. The companion
# extension above focuses it at startup so it actually renders. init-study-session.sh
# copies this into place for the mantra condition only. Under /opt so a Kasm
# home-dir restore can't wipe it before the session-init copy runs.
RUN mkdir -p /opt/study
# [redacted: COPY of docker/config/code/state.vscdb — a VS Code layout snapshot (Mantra panel
# moved to the secondary side bar), not a credential, but excluded here since it was captured
# from a real build and not worth reproducing byte-for-byte for a reviewer.]

# ── Cursor user settings (update suppression + study config) ──────────────────
RUN mkdir -p /home/kasm-user/.config/Cursor/User \
    && mkdir -p /home/kasm-default-profile/.config/Cursor/User
COPY docker/config/cursor-settings.json /home/kasm-user/.config/Cursor/User/settings.json
COPY docker/config/cursor-settings.json /home/kasm-default-profile/.config/Cursor/User/settings.json

# ── Cursor pre-auth (research account — participants never see login) ──────────
# Cursor stores auth in state.vscdb (SQLite ItemTable), not storage.json.
# To refresh: sign in to Cursor on any machine (Google OAuth is fine — only the
# resulting session is copied, never the login flow), then graft the tokens in:
#   python3 docker/config/cursor/graft-auth.py            # --dry-run to preview
# That copies ONLY the cursorAuth/* keys, so the workstation's paths, layout and
# telemetry identity stay out of the image.
# Current session: Pro, access token expires 2026-09-27, and scope includes
# offline_access so Cursor renews it silently from the refresh token.
# Stored in kasm-default-profile so Kasm's home-dir restore preserves it.
# init-study-session.sh also copies as belt-and-suspenders, and verifies the
# session is live before launching Cursor (see check-cursor-auth.py).
RUN mkdir -p /home/kasm-user/.config/Cursor/User/globalStorage \
    && mkdir -p /home/kasm-default-profile/.config/Cursor/User/globalStorage
# [redacted: COPY of docker/config/cursor/state.vscdb — bakes in a live, signed-in Cursor
# research-account session (SQLite auth store). Not reproducible without the original login.]

# ── Benchmark repos ────────────────────────────────────────────────────────────
# Copy only the demo projects, not the surrounding benchmark repo (README,
# workflows/, analysis/, .git, .venv, etc.) — those cluttered the participant's
# file explorer with irrelevant files.
COPY demo-workspace-source/demo-py /home/kasm-user/benchmark/demo-py/
COPY demo-workspace-source/demo-ts /home/kasm-user/benchmark/demo-ts/
COPY demo-workspace-source/demo-java /home/kasm-user/benchmark/demo-java/
# Empty file in the workspace root, used when filming the onboarding videos: it
# gives a trivially dictatable target ("open test dot js") to demonstrate that
# opening files by voice works, without touching any task file. Not referenced by
# any task, and outside all three git repos (setup-git.sh only inits the demo-*
# projects), so it cannot affect the version-control task.
COPY demo-workspace-source/test.js /home/kasm-user/benchmark/test.js

RUN pip3 install pytest black \
    && cd /home/kasm-user/benchmark/demo-py && pip3 install -e . 2>/dev/null || true

RUN cd /home/kasm-user/benchmark/demo-ts && npm install

COPY docker/scripts/setup-git.sh /tmp/setup-git.sh
RUN chmod +x /tmp/setup-git.sh && bash /tmp/setup-git.sh

# ── Desktop shortcuts + task sheet ────────────────────────────────────────────
RUN mkdir -p /home/kasm-user/Desktop
COPY docker/config/desktop/ /home/kasm-user/Desktop/
COPY docker/config/videos/ /opt/study/videos/
COPY docker/tasks-serenade.md /home/kasm-user/Desktop/tasks-serenade.md
COPY docker/tasks-mantra.md /home/kasm-user/Desktop/tasks-mantra.md
COPY docker/tasks-cursor.md /home/kasm-user/Desktop/tasks-cursor.md
RUN chmod +x /home/kasm-user/Desktop/*.desktop \
    && mv /home/kasm-user/Desktop/StopAutoRestart.desktop /usr/share/applications/StopAutoRestart.desktop

# ── Autostart ─────────────────────────────────────────────────────────────────
RUN mkdir -p /home/kasm-user/.config/autostart
COPY docker/config/autostart/ /home/kasm-user/.config/autostart/

# ── Study session init script ─────────────────────────────────────────────────
# Generates a unique SESSION_ID at desktop startup, starts the input logger
# and screen recorder, and exports env vars via ~/.study-session-env.
COPY docker/scripts/init-study-session.sh /usr/local/bin/init-study-session.sh
COPY docker/scripts/check-cursor-auth.py /usr/local/bin/check-cursor-auth.py
RUN chmod +x /usr/local/bin/init-study-session.sh /usr/local/bin/check-cursor-auth.py

# ── Results directory (Kasm Persistent Profile mount point) ───────────────────
# In Kasm admin: set Persistent Profile Path to /home/kasm-user/results
# so this directory survives workspace resets.
RUN mkdir -p /home/kasm-user/results

# ── Serenade: pre-auth token (skips the NUX/welcome wizard) ───────────────────
# The NUX shows when getToken() returns "" (no logged-in account). Fix: put a
# real token in serenade.json and keep it alive across Kasm's home-dir wipe by
# staging it in /home/kasm-default-profile/ — Kasm copies that tree to
# /home/kasm-user/ on every session start, so the token is always present.
# init-study-session.sh also copies it as belt-and-suspenders.
#
# ONE-TIME TOKEN SETUP: run a container, complete Serenade login, then:
#   docker cp <container>:/home/kasm-user/.serenade/serenade.json \
#             docker/config/serenade/serenade.json
# Add the resulting "token" value (keep nux_completed/plugins fields), then rebuild.
RUN mkdir -p /home/kasm-default-profile/.serenade
# [redacted: COPY of docker/config/serenade/serenade.json — bakes in a live Serenade auth
# token. Not reproducible without the original login.]

# ── Task sheet: render markdown → HTML, open in Firefox ──────────────────────
COPY docker/scripts/render-tasks.py /tmp/render-tasks.py
RUN pip3 install --no-cache-dir markdown \
    && python3 /tmp/render-tasks.py \
    && rm /tmp/render-tasks.py

# ── Browser wiring ────────────────────────────────────────────────────────────
# 1. XFCE "Web Browser" menu entry: set Firefox in helpers.rc so
#    exo-open --launch WebBrowser works without D-Bus.
# 2. xdg-open override: D-Bus is unavailable in containerized XFCE, so the
#    real exo-open chain fails. Route to Firefox directly.
RUN mkdir -p /home/kasm-user/.config/xfce4 \
    && printf '[Default Applications]\nWebBrowser=firefox\nFileManager=Thunar\n' \
        > /home/kasm-user/.config/xfce4/helpers.rc \
    && printf '#!/bin/bash\nfirefox "$@" >/dev/null 2>&1 &\nexit 0\n' \
        > /usr/local/bin/xdg-open \
    && chmod +x /usr/local/bin/xdg-open

# ── Firefox policy + autoconfig (suppress updates, onboarding, Terms-of-Use) ──
# Placed late so it does not invalidate the expensive Node/VSCode/Cursor layers.
# policies.json → enterprise policies (updates/onboarding/telemetry off); copied
#   to BOTH known lookup paths so it works regardless of build.
# autoconfig.js + mozilla.cfg → prefs policy can't reach (the ToU gate).
# The Mozilla .deb installs Firefox under /usr/lib/firefox.
RUN mkdir -p /etc/firefox/policies /usr/lib/firefox/distribution /usr/lib/firefox/defaults/pref
COPY docker/config/firefox/policies.json  /usr/lib/firefox/distribution/policies.json
COPY docker/config/firefox/policies.json  /etc/firefox/policies/policies.json
COPY docker/config/firefox/autoconfig.js  /usr/lib/firefox/defaults/pref/autoconfig.js
COPY docker/config/firefox/mozilla.cfg    /usr/lib/firefox/mozilla.cfg

# ── Ownership ──────────────────────────────────────────────────────────────────
RUN chown -R kasm-user:kasm-user /home/kasm-user
