# Challenges

Running list of problems hit, and where they stand.

## Open

### The meter is equal-tempered, but a drone teaches just intonation
Tuning to a drone by ear pulls toward *pure* intervals — the ones where the
beating stops. Those don't line up with equal temperament, which is what the
cents meter measures against. A major third tuned pure sits about 14 cents flat
of the equal-tempered one; a minor third about 16 cents sharp. Fifths are close
enough not to matter, 2 cents.

So on some intervals the meter will call the player flat while their ear is
right. This is not a bug to patch, it's two definitions of "in tune", and the
app currently only knows one of them.

**Options when it matters:** show the pure-interval target alongside the
equal-tempered one when the drone is on; widen or offset the tolerance zone for
intervals with a large temperament difference; or simply document it and let
the ear win. Needs a decision before the app claims to be right about intervals
played against a drone.

### Drone bleed without headphones
On speakers the microphone hears the drone. Mitigated by discarding any pitch
within 35 cents of the drone, unless the drone note is the target, and by
recommending headphones in the UI.

The guard has one hole by construction: when the drone note *is* the target it
switches itself off, since otherwise the correct answer could never be accepted
— and that is exactly the case where bleed would score a hit for playing
nothing. It also cannot help if the room is loud enough that the drone swamps
the horn in the microphone. Headphones remain the real fix.

### Note names can differ from other apps by an octave, twice over
A phone tuner called a note F3 where we called it F5, on an identical 311 Hz
reading. Two independent one-octave effects, stacking:

**1. Which B♭ transposition.** There are two, and they differ by an octave:

| Instrument | Written is | Concert E♭4 reads as |
| --- | --- | --- |
| B♭ tenor sax | concert + 14, a major 9th | **F5** — ours |
| B♭ clarinet, trumpet, soprano sax | concert + 2, a whole tone | F4 |

An app offering a generic "B♭" almost certainly means the +2 kind. That is
correct for a trumpet and an octave wrong for a tenor, whose part is written a
major ninth above what sounds.

**2. Octave numbering.** We use scientific pitch notation — middle C is C4, A4 is
440 Hz — as tuners and acousticians do. Yamaha and Roland style calls middle C
C3, one lower. That turns the F4 above into F3.

Nothing is wrong, and both of our choices are the right defaults: tenor is a
+14 instrument, and scientific notation is the tuner convention.

**Diagnostic worth remembering:** compare hertz before believing a note-name
disagreement between apps. Matching frequencies mean the argument is only about
labels.

**If it becomes annoying:** an octave-numbering setting — scientific, Yamaha, or
sax-relative — affecting note labels only, leaving detection and ruler positions
untouched.

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

### Detection was unverified against a real instrument — *resolved 2026-08-11*
Everything had been measured with synthesized tones, which have none of a real
horn's breath noise, slow attack, or spectral richness. The worry was meter
jitter, hits landing on the attack transient instead of a settled note, or the
octave being reported wrong down low.

Tested on tenor against a hardware tuner: it tracks the horn closely, with none
of those failure modes appearing. Voice was then checked the same way and also
tracks accurately, despite vibrato and moving formants being the harder case.
No smoothing or hold-time change needed for either.

Both input paths are therefore confirmed against a reference. The planned
mitigations — median filtering across frames, or requiring low frame-to-frame
variance rather than mere tolerance — stay unimplemented, and are worth
remembering if heavy vibrato or subtone playing later proves troublesome.

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
