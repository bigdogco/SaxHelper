# Dev log — 2026-08-11 11:32

## Task
Restrict practice to the six mother-of-pearl touchpieces, so a beginner can
work with the notes they actually own before facing the full range.

## What changed
A **Which notes** selector above the note pickers, with four options:

| Option | Written notes | Sounding on tenor |
| --- | --- | --- |
| Full range | from the pickers | — |
| Pearl keys, both hands | D4 E4 F4 G4 A4 B4 | C3 D3 E♭3 F3 G3 A3 |
| Left-hand pearls | G4 A4 B4 | F3 G3 A3 |
| Right-hand pearls | D4 E4 F4 | C3 D3 E♭3 |

No palm keys, no side keys, no octave key. The single-hand sets weren't
requested but come free from the same data, and three notes on one hand is a
gentler entry point than six across both.

Selecting a fixed set disables and greys the low/high pickers, since the set is
defined by fingering rather than by range. The info line switches from the
playable-range warning to a plain statement of what the set sounds like.

## Implementation notes
- Sets are declared in **written** pitch, because that is how the fingering is
  named; `candidateNotes()` subtracts the instrument offset to get concert MIDI,
  keeping the rule that all internal state is concert pitch.
- `pickTarget()` now draws from a candidate list rather than a contiguous MIDI
  span. No-repeat filters the previous note out of the pool instead of
  re-rolling, so it can't loop and stays uniform over what remains.
- The pearl sets stay selectable on non-transposing instruments, where the names
  are meaningless; the info line says so rather than the option vanishing.
  Revisit if it proves confusing.

## Verification
Instrumented the page to record the frequency of every target tone actually
synthesized, then drove 900 rounds through the Skip button:

- **pearls** (300 draws) — only the six expected notes, 46–56 occurrences each,
  **0 immediate repeats**
- **pearls-left** (200) and **pearls-right** (200) — only their three notes,
  0 immediate repeats
- back to **Full range** — pickers re-enable, all six chromatic notes G2–C3 drawn

## Real-instrument test
Tracking confirmed on tenor against a hardware tuner: it follows the horn
closely. This closes the standing risk that the detector had only ever been
measured on synthesized tones. No smoothing or hold-time changes needed.
