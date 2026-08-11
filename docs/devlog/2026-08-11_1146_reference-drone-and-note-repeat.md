# Dev log — 2026-08-11 11:46

## Task
Two additions: a reference drone to tune against, and an option to replay the
target note on an interval instead of once. See
`docs/todo/TODO_DRONE_AND_REPEAT.md`.

## Reference drone
A continuously sounding note held under the exercise, in its own panel: on/off,
note picker, volume. It survives across rounds, keeps sounding under the target
note, and works before Start is pressed.

- **Voice.** Fundamental plus two soft harmonics (1, 0.3, 0.15), all pure sines
  through a lowpass, with 0.4 s fade in and 0.3 s fade out to avoid clicks.
  Deliberately *not* detuned against itself — every beat the player hears should
  be them against the drone and nothing else.
- **Lazy AudioContext.** `ensureCtx()` creates the context on demand, so the
  drone can be switched on before the microphone is ever requested. `start()`
  now reuses whatever exists.
- **Live edits.** Changing the note rebuilds the voice; the volume slider ramps
  the gain with `setTargetAtTime` rather than jumping.

## Note repeat
"Play the note again every N seconds until I match it", 3–20 s, timed
start-to-start.

- Stops on a hit or a skip, and is cleared on page hide
- A manual replay reschedules, so a repeat can't land on top of it
- Won't cut across an attempt: if the mic heard a pitch in the last 700 ms the
  slot is skipped and the next one is used
- Floor is 3 s because the note itself runs 1.6 s; at 2 s there was essentially
  no gap left to answer in, which the first test made obvious

## Drone bleed into the microphone
Without headphones the mic hears the drone, so the detector can lock onto it
while the player is silent — and if the drone matches the target, that is a hit
awarded for playing nothing.

Any detected pitch within 35 cents of the drone is now discarded, *unless* the
drone note is itself the target. Playing the drone pitch when it isn't the
target is a wrong answer regardless, so the guard costs nothing legitimate. The
panel also recommends headphones.

## Verification
Instrumented the page to log every oscillator it creates, then drove the real
loop with a retunable fake microphone.

| Check | Result |
| --- | --- |
| Drone partials for concert B♭2, before Start | 116.54 / 233.08 / 349.62 Hz |
| Rebuild after switching to concert C3 | 130.81 / 261.63 / 392.44 Hz |
| Repeat interval, set to 2 s | 10 repeats, gaps all **2.00 s**, all at 98 Hz |
| Mic on the drone pitch, target elsewhere | **no hit**, answer stayed masked |
| Mic on the target, drone still sounding | hit registered, 1 of 1 |
| Repeats after a hit | **0 over 10 s** |

## Left undone, on purpose
Just intonation. Tuning to a drone by ear pulls toward pure intervals, which sit
off equal temperament — a major third about 14 cents flat of what the meter
calls correct. The meter will therefore disagree with a correctly tuned ear on
some intervals. Recorded in `docs/CHALLENGES.md`; it needs a decision, not a
patch.
