# Dev log — tabs, tuner, and a drone screen

Split the single screen into three tabs and added two of them.

## Structure
`Trainer | Tuner | Drone` across the top, each a `.tabPanel` section. The
trainer tab holds exactly what was there before, unchanged.

## Tuner
A plain chromatic tuner: nearest note, cents needle over ±50 cents, hertz, green
within 10 cents. Names follow the instrument set on the Trainer tab, so on a
transposing horn it reads as you finger it with the concert pitch beneath.

The last reading is held for 600 ms after the sound stops, so it doesn't blink
between phrases.

It deliberately does **not** apply the trainer's drone-bleed filter — on a
tuner, reading the drone is a legitimate thing to want, since tuning the horn to
a drone is normal practice. Said so in the UI rather than silently filtering.

## Drone screen
One button per written note of the horn, low B♭3 to F#6 — 33 of them — each
showing the fingered name with the concert pitch beneath. Tap to sound, tap the
sounding one again to stop. Plus a big toggle and a volume slider.

The trainer keeps its own drone panel, as decided. Both are views of a single
drone: `setDroneNote()` / `setDroneActive()` are the only writers and
`renderDrone()` re-syncs every view, so the two can't drift apart. The grid is
rebuilt when the instrument changes, since that changes what each fingering
sounds.

## Sharing one microphone
The trainer and the tuner both listen, so the mic became shared. `acquireMic()`
opens the stream on first use and `releaseMic()` closes it only once the last
listener stops, tracked through a `micUsers` set. One animation frame loop runs
the detector once per frame and hands the same result to whichever screens are
live — so running both costs no more detection work than running one.

`tick()` became `frame()` plus `trainerFrame()`.

## Verification
Driven through the real UI with a synthetic microphone.

| Check | Result |
| --- | --- |
| Tab switching | correct panel shown, correct button marked active |
| Key grid | 33 buttons, B♭3 → A♭2 concert, G♭6 → E5 concert on tenor |
| Tap a key | written D4 sounded 130.81 / 261.63 / 392.44 Hz (concert C3) |
| Sync, tab → trainer | checkbox ticked, select moved to concert MIDI 48 |
| Sync, trainer → tab | big button, status line and key highlight all followed |
| Volume, either slider | both sliders and the label agreed |
| Tap sounding key again | drone stopped, highlight cleared |
| Instrument → alto | grid rebuilt, B♭3 now sounds D♭3 concert |
| Tuner at 220 Hz | B4 written · A3 concert, −2 cents, green, needle centred |
| Tuner 30 cents sharp | +32 reported, green off, needle right |
| Tuner at 146.8 Hz | E4 written · D3 concert, −1 cent |
| Mic with both running | one stream, live |
| Stop tuner only | mic stays live for the trainer |
| Stop both | track reads `ended` |
| Trainer after refactor | scored a hit with the tuner running concurrently |

## Testing notes
Two artefacts of the environment, not the app, cost time and are worth
remembering:

- The preview pane reported `visibilityState: hidden` and fired **zero**
  animation frames, freezing the whole loop. Shimming `requestAnimationFrame`
  onto `setTimeout` made it testable.
- A fake `getUserMedia` must hand back a **fresh** stream per call, as the real
  one does. Reusing a stream whose tracks were already stopped produced silence
  and looked exactly like a detection failure.

## Follow-up: tuner shows both pitches side by side

Changed after a first look at it. The tuner now prints the concert pitch and
the fingering as two columns, **concert first** — it is the pitch actually in
the air, so it leads — each with a small caption beneath. Both turn green
together.

On a non-transposing instrument the second column is hidden rather than
printing the same note twice.

| Instrument | Concert | As fingered |
| --- | --- | --- |
| Tenor | A3 | B4 |
| Alto | A3 | G♭4 |
| Concert pitch | A3 | column hidden |

The tuner's hint panel described the old stacked layout and was rewritten to
match.

## Layout, finally seen
The preview pane started compositing, so the new screens were checked visually
as well as functionally. The drone grid wraps to six rows of six at desktop
width, written name large with the concert pitch beneath, sounding note filled
green. Still unchecked on a phone, where 33 buttons will wrap to about four
columns.
