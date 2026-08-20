# Dev log — trainer answer readout matches the tuner

The revealed answer showed one big fingered name with a wordy line beneath —
`written A3 · sounding G2 concert · 98.0 Hz`. The tuner had already moved to two
columns, concert first. Now they are the same component.

## Change
`.noteRow` / `.notePart` / `.bigNote` / `.noteTag` replace the tuner-only class
names, and both readouts use them. One set of styles, one visual language, and
the next display that needs a note gets it for free.

The trainer's answer has a third state the tuner doesn't: unknown. Rather than
show two question marks, both note columns give way to a single `?`, so the
layout can't leak whether the instrument transposes before the answer is given.
`maskAnswer()` owns that switch and is called from `refreshAnswer()` and from
`stop()`.

Knock-ons, both for consistency now that concert leads:
- The status line dropped its note name — `Got it — A3.` became `Got it!`, since
  both names sit directly above it.
- The live heard readout reordered to concert first:
  `103.8 Hz · A♭2 · B♭3 fingered · +99 cents`.

## Verification
| State | Result |
| --- | --- |
| Before start | single `?`, both note columns hidden |
| Note playing, unsolved | single `?` |
| After a hit | `G2` concert, `A3` fingered, `98.0 Hz`, "Got it!" |
| Switched to concert pitch | fingered column dropped, `G2` alone |
| Switched back to tenor | both columns again |
| After Stop | back to a single `?` |

Confirmed visually too: the two-column readout on the green hit panel reads the
same as the tuner's.
