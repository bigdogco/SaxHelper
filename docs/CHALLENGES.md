# Challenges

Running list of problems hit, and where they stand.

## Open

### Detection is unverified against a real instrument
Everything so far was measured with synthesized tones. A real saxophone has
breath noise, a slow and unstable attack, and a much richer harmonic spectrum;
a sung note has vibrato and drifting formants. The tracker may behave
differently on both.

**Watch for:** jitter on the meter, hits registering during the attack transient
rather than on a settled note, or the octave being reported wrong on low notes.
**Mitigation if it bites:** raise the hold time, add median smoothing across
frames, or require the pitch to be stable (low frame-to-frame variance) rather
than merely inside the tolerance band.

### Range default sits one semitone below the horn
The default range is concert G2–C3, which on tenor is written A3–D4 — and
written A3 is a semitone below the tenor's lowest note, low B♭3. The app warns
about this in orange rather than silently clamping, since it is still a valid
target if answered with the voice. Decide later whether the default should just
move up to concert A♭2.

### Low notes need real speakers
The selector reaches down to concert F1 (~44 Hz) so that transposing players can
set a range in written pitch. Laptop speakers will not reproduce the fundamental
down there; the player would be matching a harmonic instead of the note.

### Microphone access on `file://`
Chrome treats `file://` as a secure context so `getUserMedia` should prompt
normally, but this is not guaranteed across browsers. Fallback is to serve the
folder over `http://localhost`. Could not be verified in the dev environment —
see below.

## Resolved

### Pitch tracker read consistently flat — *resolved 2026-08-11*
The first autocorrelation implementation was 5–9 cents flat on harmonic-rich
tones, which would have trained the player to blow sharp. A fractional-lag
refinement made it much worse (20–90 cents sharp). Replaced with YIN plus
period-multiple refinement: worst case 3.9 cents across MIDI 40–84. Full
account in `docs/devlog/2026-08-11_1113_note-matching-trainer.md`.

### Speakers answering the question — *resolved 2026-08-11*
With the mic live while the target note sounded, the app heard its own output
and scored an instant hit. Input is now gated off until playback finishes.

### No microphone in the dev environment — *worked around 2026-08-11*
The preview pane blocks device capture, so the game loop could not be driven by
a real mic. Worked around by overriding `navigator.mediaDevices.getUserMedia` to
return a `MediaStreamDestination` fed by an oscillator, which exercises the
entire pipeline — analyser, detector, tolerance, hold timer, reveal — for real.
This remains the way to test the loop without playing into a mic, but it does
not substitute for a real horn.
