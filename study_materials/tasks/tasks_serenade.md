# Hands-Free Programming Study — Serenade

## Your tool: Serenade

This is your tool.

Serenade is a voice coding assistant. It listens continuously and executes spoken commands directly in VS Code — no button to hold.

<div class="tutorial">
<p class="tutorial-required">⚠ WATCHING THIS 2-MINUTE VIDEO IS MANDATORY — SUBMISSIONS THAT SKIP IT MAY BE REJECTED</p>
<p class="tutorial-label"><b>Getting started with Serenade.</b> Nothing is timed while you watch, and you can replay it at any time.</p>
<video class="tutorial-video" controls preload="metadata" playsinline>
<source src="videos/serenade-start.webm" type="video/webm">
</video>
</div>

### Starting it

1. **Serenade launches when you click OK on the "Ready to start?" box** — together with VS Code. It is not running yet while you are reading this page.
2. Once it has launched, a **Serenade icon appears in the system tray** — the small row of icons at the **bottom-right corner of the desktop inside your browser window** (not your own computer's taskbar). A **green dot** means it is listening; a **red dot** means it cannot hear you.
3. **Speak a command** clearly and at a natural pace. Serenade executes it immediately. Say **"undo"** if anything goes wrong.

### How Serenade behaves

Serenade sends its commands to whichever application currently has focus. Clicking this task list in the browser gives the browser focus, so commands spoken after that go to the browser rather than to VS Code. Clicking the VS Code window moves focus back, and so does the command `focus code` (`focus firefox` moves it the other way).

`undo` reverses the last change. `open` takes a file name — for example, `open main dot py` (the docs' own example is `open index dot js`).

### If Serenade (or VS Code) disappears

It restarts by itself within a few seconds — you do not need to do anything. If it does not come back, double-click the **Serenade** icon on the desktop.

---

## Your goal

Complete as many tasks as you can within **10 minutes** using Serenade's voice input.

Work through tasks in order. If you are stuck on a task for more than a minute, **use the mouse and keyboard to move on to the next task**.

**A few things to know:**
- The tool may understand what you say but still apply the wrong change, and not every task is necessarily doable with it — that's expected, not something you're doing wrong.
- Please keep background noise to a minimum so your voice is heard clearly.

### Where the tasks live

All of your tasks are in **`demo-py/`** — `demo-ts/` and `demo-java/` are there only so the workspace looks like a real multi-project repository.

Each task names a file plus, in brackets, its folder. Open files by name — `open main dot py`. Only `test.js` is open when you start; it belongs to no task, so you can leave it alone. Opening each task's own file is part of the task — by voice if you can, by keyboard if the tool won't cooperate.

---

## Command cheat sheet

Every command below is from Serenade's official documentation (<https://serenade.ai/docs>). A command is an **action** plus a **selector**, e.g. `delete next function`.

| Goal | Say |
|---|---|
| Open a file | `open main dot py` — the file's name on its own is enough |
| Insert text at the cursor | `insert foo equals one` |
| Copy / cut / paste | `copy line` · `cut previous two words` · `paste` |
| Indent / comment | `indent block` · `comment function` · `uncomment lines two to three` |
| Switch editor tabs | `next tab` · `previous tab` · `first tab` |
| Go to a line | `go to line two hundred` |
| Go to a name in the file | `go to parse user` |
| Select something | `select line` · `select block` · `select previous word` |
| Move a line up / down | `shift line up` · `shift line down` |
| Repeat a command N times | `shift line up ten times` |
| Duplicate | `duplicate block` · `duplicate function` |
| Delete | `delete line` · `delete line four` · `delete lines three to four` |
| Add a new line of code | `add all caps dup mark equal true` |
| Change existing text | `change api key to api token` |
| Repeat the last change | `repeat change` |
| Jump to next / previous thing | `next function` · `previous class` |
| Undo / redo | `undo` · `redo` |
| Save | `save` |
| Format the file | `format` · `style file` |
| Breakpoint | `toggle breakpoint` |
| Debug | `start debugging` · `step over` · `step into` · `continue` · `stop debugging` |
| Terminal | `focus terminal`, then `run pytest` |
| Press a key | `press enter` · `press control tab` |
| **Point Serenade at an app** | `focus code` · `focus firefox` |
| Stop listening | `pause` · `stop listening` |

**Tips**

- Say the extension as a word, not letter by letter: `main dot py` → `main.py` (`py` rhymes with "pie"), `app underscore debug dot py` → `app_debug.py`, `test dot js` → `test.js`.
- If Serenade lists several possible transcripts and picks the wrong one, just say the number of the option you wanted — e.g. `two` — and it will undo the first and use that one instead.
- You can chain commands without pausing: `save focus terminal`.
- You can repeat any command: `indent three times`, `shift line up ten times`.
- For capitals and underscores, name the style: `all caps dup mark` → `DUP_MARK`, `underscores normalize user` → `normalize_user`.
- If Serenade mishears you, say **`undo`** and try slightly different phrasing.
- You may use the keyboard/mouse to click the Serenade tray icon, dismiss a prompt, or **move on to the next task if you are stuck** — all code edits should be by voice.

---

## Tasks

### 1. Open a file and navigate to a line <!-- ref: W01 -->
Open **`main.py`** (it lives in `demo-py/src/utils/`) and scroll until approximately **line 200** is visible.

---

### 2. Select a block and move it <!-- ref: W03 -->
In `main.py`, find the marked block (between the `# --- BEGIN BLOCK` and `# --- END BLOCK` comments), select it, and **move it up by 10 lines**.

---

### 3. Edit a block <!-- ref: W04 -->
In the same file:
1. **Duplicate** the marked block, so there are two copies of it.
2. Add the line `DUP_MARK = True` **directly below the duplicated copy**.
3. **Delete** the `BLOCK_CONST = 42` line in the **first** copy, so only one `BLOCK_CONST = 42` is left in the file.

---

### 4. Symbol navigation <!-- ref: W06 -->
From a use of `parseUser`, **jump to its definition**. Then navigate to `DEFAULT_ROLE`. Then return to the original location.

---

### 5. Run a program <!-- ref: W08 -->
**Run** `app.py` (in `demo-py/src/`). Confirm the expected output appears in the terminal.

---

### 6. Debug <!-- ref: W09 -->
Open `app_debug.py` (in `demo-py/src/`). **Set a breakpoint**, start the debugger, step through execution, and inspect the variable `total`.

---

### 7. Search and replace <!-- ref: W13 -->
In `main.py`, replace `apiKey` with `api_token` in **exactly 3 places**.

---

### 8. Run tests and navigate to a failure <!-- ref: W17 -->
Run `pytest` in `demo-py`. Open the **first failing test** in `test_failing.py` (in `demo-py/tests/`).

---

### 9. Refactor <!-- ref: W05 -->
In `service.py` (in `demo-py/src/`), extract the duplicated normalization logic into a function called `normalize_user(user)` and call it from both places.

---

### 10. Version control <!-- ref: W16 -->
Open the git diff, review the changes, **stage** all modified files, **commit** with the message `updates`, and **push** to the remote.

---

### 11. Format <!-- ref: W19 -->
**Format** `main.py` using the project's configured formatter.

---

*When you are done or your 10 minutes are up, return to the survey. Please do not delete your session.*
