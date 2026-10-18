# Hands-Free Programming Study — Cursor

## Your tool: Cursor

This is your tool.

Cursor is an AI code editor with built-in voice input. You speak your intent and Cursor's AI makes the change for you — this can be a navigation request (e.g. "open main.py and scroll to line 200") or a code edit (e.g. "duplicate this block").

<div class="tutorial">
<p class="tutorial-required">⚠ WATCHING THIS 2-MINUTE VIDEO IS MANDATORY — SUBMISSIONS THAT SKIP IT MAY BE REJECTED</p>
<p class="tutorial-label"><b>Getting started with Cursor.</b> Nothing is timed while you watch, and you can replay it at any time.</p>
<video class="tutorial-video" controls preload="metadata" playsinline>
<source src="videos/cursor-start.webm" type="video/webm">
</video>
</div>

### Starting it

**Cursor launches when you click OK on the "Ready to start?" box.** It is not running yet while you are reading this page — everything below happens *after* the Cursor window has opened.

1. **Open** the Cursor chat panel — press **`Ctrl+L`** or click the chat icon in the sidebar.
2. **Click** the **microphone icon** in the message input box.
3. **Speak** your intent clearly — describe what you want done in plain English.
4. **Submit** — press **`Enter`** or click the send button. Cursor's AI applies the change.

### If Cursor disappears

It restarts by itself within a few seconds — you do not need to do anything. If it does not come back, double-click the **Cursor** icon on the desktop.

---

## Your goal

Complete as many tasks as you can within **10 minutes** using Cursor's voice input.

Work through tasks in order. If you are stuck on a task for more than a minute, **use the mouse and keyboard to move on to the next task**.

**A few things to know:**
- The tool may understand what you say but still apply the wrong change, and not every task is necessarily doable with it — that's expected, not something you're doing wrong.
- Clicking the microphone icon with your mouse is allowed — that's the one exception to "no mouse."
- Please keep background noise to a minimum so your voice is heard clearly.

### Where the tasks live

All of your tasks are in **`demo-py/`** — `demo-ts/` and `demo-java/` are there only so the workspace looks like a real multi-project repository.

Each task names a file plus, in brackets, its folder. Open files by **name** (`main.py`), never by path. Nothing is open when you start, so opening the file is part of the task — by voice if you can, by keyboard if the tool won't cooperate.

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
