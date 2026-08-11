# Dev log — 2026-08-11 11:13

## Task
Build the first version of the ear trainer: play a hidden note, listen to the
player answer it on sax or voice, confirm the hit.

## What was built
`index.html` — a single self-contained page, no build step and no dependencies.

- **Target playback.** Web Audio sawtooth through a lowpass with a moving cutoff
  and an ADSR-ish gain envelope, for a reed-like tone rather than a bare sine.
- **Pitch detection.** Mic → `AnalyserNode` (fftSize 4096, ~85 ms at 48 kHz) →
  YIN pitch tracker running on `requestAnimationFrame`.
- **Round logic.** Random note from the selected range, answer masked as `?`,
  success requires staying inside the tolerance for a configurable hold time.
  Green panel + revealed note + victory arpeggio on a hit, then auto-advance.
- **Transposition.** All internal state is concert MIDI; written pitch is a
  display-time offset per instrument. Tenor (+14) is the default, with alto
  (+9), soprano/clarinet/trumpet (+2), bari (+21) and straight concert pitch.
  Note names use flat spellings throughout.
- **Settings.** Range, tolerance (10–100 cents), hold time, tuner meter on/off,
  accept-any-octave, auto-advance, no-immediate-repeat.

## Pitch detection: three attempts
The first two were measured against synthesized tones of known frequency and
rejected on the numbers.

1. **Normalized autocorrelation + parabolic peak interpolation.** Read a
   consistent **5–9 cents flat** on harmonic-rich tones (accurate on pure
   sines). A tuner that reads flat trains the player to blow sharp, so this was
   not shippable. Cause: the correlation peak is broad and slightly asymmetric,
   so a 3-point parabola over unit-spaced samples lands well off the true max.
2. **Fixed-length correlation window + fine fractional-lag grid search.** The
   fixed window removed the window-length bias but the fractional search made
   things far worse — **20–90 cents sharp**. Linear interpolation of the shifted
   signal attenuates its high frequencies, which shifts the normalized ratio and
   drags the maximum toward the low edge of the search bracket.
3. **YIN — cumulative mean normalized difference function.** Adopted. Its
   minimum is sharp, so parabolic interpolation is well conditioned, and the
   first-dip-below-threshold rule rejects the octave-too-low answer that plain
   autocorrelation favours. Refining on the highest usable multiple of the
   period recovers the resolution lost at high notes, where one sample of period
   error is ~11 cents at C5.

### Measured
Synthetic tones (6 harmonics + noise), MIDI 40–84, at 44.1 kHz and 48 kHz:

| metric | result |
| --- | --- |
| worst-case error, harmonic tones | **3.9 cents** |
| worst-case error, pure sines | 2.9 cents |
| cost per frame | 2.6 ms |
| silence / white noise | correctly rejected (no pitch) |

Then end-to-end in the live page, feeding a synthetic sawtooth in as a fake
`MediaStream` in place of the microphone:

- 98.0 Hz in → **97.9 Hz reported, −1 cent**, needle centred, hit registered
- detuned to +80 cents → **+78 reported**, needle outside the zone, no false hit
- reveal, victory arpeggio, streak counter and auto-advance all confirmed

## Notes
- Mic input is gated off while the target note is sounding, so the speakers
  can't answer the question for the player.
- `getUserMedia` is requested with `echoCancellation`, `noiseSuppression` and
  `autoGainControl` all disabled — they mangle a sustained tone.
- Detection is unverified against a real horn; only synthetic input was
  available in this environment. See `docs/CHALLENGES.md`.
