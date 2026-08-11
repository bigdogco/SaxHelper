# TODO — Reference drone and note repeat

Two additions to the trainer:

1. **Reference drone** — a continuously sounding note held underneath the
   exercise, so intonation can be tuned against something present rather than
   remembered. Tuning by beating is the goal: when the wobble stops, you're on.
2. **Note repeat** — replay the target every few seconds until it's matched,
   instead of playing it once.

## Plan

- [x] Drone voice: fundamental plus soft harmonics, fade in/out to avoid clicks
- [x] Drone note picker, in written and concert pitch like the other selectors
- [x] Drone volume control, independent of the target note
- [x] Drone survives across rounds, and keeps sounding under the target note
- [x] Create the AudioContext lazily, so the drone works before Start is pressed
- [x] Repeat toggle plus an interval control
- [x] Repeat stops on a hit or a skip, and never overlaps itself
- [x] Don't interrupt the player mid-attempt with a repeat
- [x] Handle the drone bleeding into the microphone (see below)
- [x] Verify: drone audible and stable, repeat fires on schedule, no false hits

All verified against the running app with a retunable fake microphone; the
numbers are in `docs/devlog/2026-08-11_1146_reference-drone-and-note-repeat.md`.

## Also done

- [x] Start button doubles as Stop, releasing the microphone rather than
      leaving it open for the life of the tab
      (`docs/devlog/2026-08-11_1228_start-stop-toggle.md`)

## Still to do

- [ ] Play it on the horn with headphones and see whether the drone is actually
      useful in practice, and whether the default volume sits right
- [ ] Decide what to do about just intonation vs the equal-tempered meter

## Known problem: drone bleed

Without headphones the microphone hears the drone, so the detector may lock onto
it while the player is silent — and if the drone matches the target, that is a
false hit awarded for playing nothing.

Approach: recommend headphones in the UI, and ignore any detected pitch sitting
within ~35 cents of the drone, unless the drone note *is* the target. Playing
the drone pitch when it isn't the target is a wrong answer anyway, so the guard
costs nothing.

## Deliberately not doing yet

Just intonation. Tuning to a drone by ear pulls toward pure intervals, which sit
off equal temperament — a major third about 14 cents flat. The cents meter will
therefore disagree with a correctly tuned ear on some intervals. Noted in
`docs/CHALLENGES.md`; needs a real decision before the meter can claim to be
right about intervals against a drone.
