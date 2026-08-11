# Dev log — 2026-08-11 11:35

## Task
Record the voice-input test result. Docs only, no code changes.

## Outcome
Voice was checked against a hardware tuner, the same way the tenor was earlier
today, and tracks accurately. This was the case I was least confident about:
a sung note carries vibrato and shifting formants, neither of which a horn
throws at the detector.

Both input paths — saxophone and voice — are now confirmed against a reference
instrument. Pitch detection is no longer an open risk on this project, and the
mitigations sketched out for it (median filtering across frames, or gating
success on frame-to-frame stability rather than tolerance alone) remain
deliberately unimplemented.

## Changed
- `docs/CHALLENGES.md` — folded the voice result into the resolved detection
  entry and dropped the "voice still untested" caveat
- `docs/todo/TODO_NOTE_MATCHING_TRAINER.md` — ticked the voice item

## State
The first version of the trainer is functionally complete and verified. What
remains in its todo are two judgement calls, not work:

- whether the default low end moves to concert A♭2, so the default range is
  fully playable on tenor
- whether the pearl sets hide on non-transposing instruments, where sax
  fingering names carry no meaning

Next feature likely comes from `docs/todo/TODO_FUTURE_WORK.md`. The reference
drone is the strongest candidate: small to build, and tuning against a held
tonic is much closer to real intonation than matching isolated notes.
