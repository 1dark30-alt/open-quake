# Charter — Time Zone Converter app (`tzconvert`)

**1. What is the one thing this must do?**
When someone says "can you meet at 4:30 my time," tap/type that time into their
zone's column and instantly read the equivalent in the other three. Four columns,
west→east: **PDT/PST | MDT/MST | CDT/CST | EDT/EST**, with the currently-active
abbreviation bold (per today's DST rules) and an editable time under each.

**2. What would be wrong if we shipped "working" software without it?**
- Editing one column must update the other three automatically — 250 ms after the
  last change, or instantly on Enter. No apply button required.
- Times are **sticky**: they hold whatever was last set, across page switches and
  app restarts. The app never snaps to the current time — the World Clock owns "now."
- The box being edited is never rewritten mid-keystroke; only the other three
  update. The edited box canonicalizes on Enter/blur.

**3. What is explicitly off-limits as a workaround?**
- No mandatory apply/convert button (Enter is optional muscle-memory, not required).
- No reset-to-now behavior of any kind — no button, no idle timeout.
- No dropping the DST-bold header: the bold half must reflect what each zone
  actually uses today.

**4. Deployment target and backup location?**
- Target: bundled static app `apps/tzconvert.html` + entry in `apps/apps.json`
  (id `tzconvert`, name "Time Zone Converter"), same pattern as the World Clock.
- Backup: this git repo — commits are the backup.

**5. How will we verify it is done?**
- Fresh install defaults to 1:00 PM / 2:00 PM / 3:00 PM / 4:00 PM.
- Tap MDT's box → stepper row `−1h −30m +30m +1h` appears; `+30m` from 2:00 gives
  2:30 and the other columns read 1:30 / 3:30 / 4:30 with no further interaction.
- Typing `4:30p` into EDT updates the rest 250 ms after the last keystroke; Enter
  commits immediately. Flexible parsing: `2`, `2p`, `230`, `14:30`, `2:30 pm`.
- Restart the app → the same times come back (localStorage, like the Flip Clock).
- 24-hour option (in the app's options in the editor) renders 13:00 etc.
- In July the DT halves are bold; in January the ST halves.

## Decisions (signed off 2026-07-23)

- **Touch UI**: per-column steppers (option 1 of the discussed alternatives).
  Tap a column's time to activate it; big `−1h −30m +30m +1h` buttons appear under
  it. If the value is off the half-hour grid (from a typed odd time), the first
  tap snaps to the grid in the tap's direction — that snap is the whole tap.
- **Wheel freebie**: a wheel listener nudges a column ±30 m (hovered column, else
  the active one) — covers desktop scroll wheels and the knob when its turn mode
  is "scroll" (the host forwards wheel events to the page in that mode).
- **Typed entry**: kept for desktop use; `inputmode="none"` suppresses any OS
  touch keyboard on the panel while hardware keyboards still work.
- Minutes granularity (no seconds). 12-hour default; 12/24 option for our
  European brothers (rendered in the app's normal options list — the editor's
  collapsible "Advanced" section is host-owned and not available to app options).
- Rejected alternatives: shared timeline scrubber, drum pickers, on-screen keypad.
