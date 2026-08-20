# TODO — Metronome tab

A fourth tab. Scope for this pass: the basics, done properly.

## Why it earns its place
A plain metronome adds nothing over the one already on a phone. The value is
that it sits with the trainer, the tuner and the drone — one thing on the music
stand — and that it composes: metronome plus drone is long tones, in time,
against a reference pitch, which is most of a wind player's daily routine.

## Plan

- [x] Fourth tab in the bar
- [x] Lookahead scheduler: wake every ~25ms, schedule clicks at exact
      AudioContext times ~100ms ahead
- [x] Tempo 30–250, slider plus minus/plus buttons
- [x] Tap tempo
- [x] Beats per bar, 1–12, with beat 1 accented — and a toggle to level it
- [x] Big glanceable beat indicator, readable from a stand
- [x] Volume, independent of the drone
- [x] Start/stop, and space bar while the tab is open
- [x] Verify: beat intervals accurate under load, tempo changes take effect
      mid-bar without drift, accent lands on beat 1

## The thing that must not be got wrong

Timing. Driving clicks from `setInterval` or `requestAnimationFrame` drifts and
jitters, and this app has already been observed getting **zero** animation
frames when its tab is hidden. Audio must be scheduled ahead on the
AudioContext clock, which is sample-accurate and unaffected by timer jitter.
The wake-up timer only decides *when to schedule*, never when a click sounds.

The beat indicator is driven from the same 25ms wake-up rather than animation
frames, so the display keeps working when the page is backgrounded.

## Known interaction

The microphone hears the click. On speakers, a broadband transient lands in the
middle of a held note while the trainer is trying to read it. YIN should reject
it as non-periodic, but this needs testing rather than assuming. Headphones
remain the answer, as with the drone.

## Deliberately not in this pass

- Subdivisions (eighths, triplets)
- Silent-bars mode — mute every other bar to test internal time. The feature
  that would make this better than a stock phone metronome; worth doing next.
- Pacing the trainer from the beat, a note per bar. See `TODO_FUTURE_WORK.md`.
