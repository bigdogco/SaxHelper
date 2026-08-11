# Future work

Ideas parked for later. Not committed to, and not in any particular order —
pick from here when starting a new task.

## Exercises

- **Reference drone.** Hold a continuous tonic underneath so notes can be tuned
  against a harmonic context instead of in isolation. Closest thing to how
  intonation actually works in a band.
- **Intervals.** Play two notes, play both back. The natural progression once
  single notes are reliable.
- **Call and response phrases.** Three or four notes in sequence, matched in
  order. Trains ear and fingers together.
- **Long-tone steadiness.** Score how far the pitch drifts over a sustained
  note, rather than whether it was hit at all.
- **Name the note.** Hear it, then say or click what it is, without playing —
  separates interval recognition from instrument technique.

## Feedback and progress

- **Per-note statistics.** Track which notes are consistently missed, and which
  direction they are missed in. Likely the single most useful addition.
- **Weight selection toward weak notes** instead of picking uniformly.
- **Attack analysis.** Report how long it takes to settle into the note, and
  whether the entry is from above or below. Scooping up to pitch is a habit
  worth seeing.
- **Session history.** Persist results in `localStorage` and show a trend.

## App

- Preset ranges per horn ("tenor low register", "the break", "full range")
- Configurable reference pitch (A=440 / 442)
- Choice of target timbre, including a real sampled sax note
- Metronome / pacing, and an auto-advance mode with a fixed time per note
- Mobile layout and screen-wake handling
- Offline install (service worker, PWA manifest)
