# Tutorial videos

Each is the ~1-2 minute "Getting started" video shown at the top of the participant's task page
(marked as mandatory to watch before starting).

- `tutorial_cursor.mp4` — demonstrates starting a Cursor voice session: opening the chat panel,
  using the microphone icon, and speaking an edit request. (1280x720, ~81s.) This replaces the
  video originally baked into the Kasm image (`docker/config/videos/cursor-start.webm`), which
  turned out not to match the intended tutorial content — re-sourced from the original recording
  on YouTube instead, re-encoded, and stripped of container metadata (no title/uploader/channel
  tags were embedded in the media file itself — those lived only on the YouTube page).
- `tutorial_mantra.mp4` — demonstrates the Mantra VS Code extension: the panel docked on the
  right, Hands-Free Mode / Push-to-Talk, and speaking a command. (Transcoded from
  `docker/config/videos/mantra-start.webm`, unchanged content.)
- `tutorial_serenade.mp4` — demonstrates Serenade: the system-tray listening indicator, focus
  switching between the browser and the editor, and a spoken command. (Transcoded from
  `docker/config/videos/serenade-start.webm`, unchanged content.)

**Reviewed.** All three have been watched/listened to by the researcher and confirmed to contain
no spoken names, identifying on-screen window titles/content, or institution branding. See
`supplement/QUESTIONS.md` for the one remaining residual note on `tutorial_cursor.mp4`'s source
page.
