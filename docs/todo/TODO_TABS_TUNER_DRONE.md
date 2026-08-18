# TODO — Tabs, tuner, and a drone screen

Split the app into three tabs and add two new screens.

1. **Trainer** — the existing note-matching exercise, unchanged.
2. **Tuner** — a plain chromatic tuner: play anything, see what it is and how
   far out.
3. **Drone** — a dedicated screen with one tappable button per note of the
   horn, named as you finger it.

## Decisions taken

- Drone note picking is a **grid of fingering buttons**, low B♭3 to F#6, the
  horn's full written range. Each button shows the fingered name with the
  concert pitch beneath.
- The trainer **keeps its own drone panel**. Both it and the drone tab are
  views onto the same drone, so switching tabs never interrupts the sound.

## Plan

- [x] Tab bar and panel switching
- [x] Share one microphone and one detection loop between trainer and tuner,
      acquiring on demand and releasing only when nobody is listening
- [x] Tuner screen: nearest note, cents needle, Hz, in-tune indication
- [x] Drone screen: fingering buttons, big toggle, volume
- [x] Keep every drone view in sync, whichever one is used
- [x] Rebuild the key grid when the instrument changes
- [x] Verify: tabs switch, tuner reads correctly, drone survives tab switches,
      mic is released only when both screens are stopped

## Notes

- The tuner deliberately does **not** apply the trainer's drone-bleed filter.
  On the tuner you may well want to read the drone — tuning the horn to it is a
  legitimate use. Documented in the UI instead.
- Keyboard shortcuts stay scoped to the trainer tab.

All verified through the running UI; the results table is in
`docs/devlog/2026-08-18_1048_tabs-tuner-drone-screen.md`.

## Still to do

- [ ] Play all three tabs on the horn, with headphones, and see whether the
      drone grid is actually quicker to use than the dropdown was
- [ ] Decide whether the tuner should offer a fixed-note mode, where you pick
      the note rather than it tracking the nearest one
