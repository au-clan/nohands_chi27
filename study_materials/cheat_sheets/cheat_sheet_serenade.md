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
