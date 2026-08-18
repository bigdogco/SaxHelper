# Sax Helper

### ▶ [Try it — bigdogco.github.io/SaxHelper](https://bigdogco.github.io/SaxHelper/)

A small ear trainer for saxophone and voice. It plays a note without telling you
what it is; you play or sing it back; when you hold the right pitch steady it
reveals the note and rings a little arpeggio at you.

One self-contained HTML file. No dependencies, no build step, no server, no
accounts, nothing stored anywhere. Open it and play.

## Running it

Use the link above — it's served over HTTPS, so the microphone works in every
browser, phones included. On a phone, add it to your home screen and it opens
full-screen like an app.

To run it locally, open `index.html` in Chrome, Edge or Firefox and allow the
microphone. **Safari won't work that way** — it refuses microphone access to
local files. Serve the folder instead and open `http://localhost:8000`:

```
python3 -m http.server 8000
```

## Three tabs

- **Trainer** — the note-matching exercise below.
- **Tuner** — a plain chromatic tuner. Play anything and see what it is and how
  far out, in concert pitch with the fingering beside it. Green within 10 cents.
  Below the needle is a vertical ruler of every semitone from C2 to C6, with a
  marker at your actual pitch — the needle tells you how far off a note you are,
  the ruler tells you which note you're on, so octave errors are obvious.
- **Drone** — one button per note of the horn, low B♭ to high F#, named as you
  finger them. Tap to sound, tap again to stop.

The trainer keeps its own drone controls, and both are views of the same drone,
so switching tabs never interrupts it. The trainer and tuner share one
microphone: it's opened when either starts listening and released only once both
have stopped.

## How to use it

Press **Start**, listen to the note, then play or sing it back. Hold it inside
the tolerance for the hold time and it locks in. **Space** replays the note,
**N** skips to the next one. Start becomes **Stop**, which releases the mic.

Two settings do most of the work:

- **Tolerance** — how close counts, in cents. Start around 50 and tighten toward
  15–20 as your ear sharpens. (100 cents is one semitone.)
- **Show tuner meter** — leave it on at first to confirm it's tracking you, then
  turn it off. With the meter showing you'll train your eyes; the point is to
  train your ear.

### Which notes

Either a range you choose, or one of the fixed sets built around the six
mother-of-pearl touchpieces — left hand B A G, right hand F E D, written D4 to
B4. No palm keys, no side keys, no octave key. There's a set for each hand
alone, which is a gentler start than all six.

### Transposition

Pick your instrument and every note is shown as you finger it, with the concert
pitch alongside — `A3 · G2 concert`. Tenor, alto, soprano/clarinet/trumpet,
baritone, and straight concert pitch for voice or piano. Note names use flat
spellings, since B♭ and E♭ horns live in flat keys. If you pick a range your
horn can't actually finger, it says so.

### Reference drone

A note held continuously underneath — in the trainer's own panel, or on the
Drone tab where every note of the horn is a button. Play against it and
listen for the wobble — that pulsing *is* how far out you are, and its speed is
the difference in hertz. When it slows to nothing and the two notes fuse, you're
in tune. It's the fastest way to build real intonation rather than needle-
watching, and it's much closer to how tuning works when you play with other
people.

**Use headphones with it.** On speakers the microphone hears the drone as well
as you, and the app has to discard anything at that pitch to avoid scoring the
drone as your answer.

Be aware that tuning by ear to a drone pulls you toward *pure* intervals, which
sit slightly off the equal temperament the cents meter measures against — a
major third about 14 cents flat of it. So on some intervals the meter and a
correctly tuned ear will disagree. That's two definitions of "in tune", not a
bug, and the app currently only knows the one.

## How it works

The target note is synthesized with Web Audio — a sawtooth through a moving
lowpass, for something reedier than a bare sine. Microphone input goes to an
`AnalyserNode` and through a **YIN** pitch tracker on every animation frame.

YIN was the third attempt. Plain autocorrelation with parabolic peak
interpolation read a consistent 5–9 cents flat on harmonic-rich tones, which
would have quietly trained the player to blow sharp; refining it at fractional
lags was worse still. YIN's difference function has a sharp minimum, so
interpolation behaves, and refining across the highest usable multiple of the
period recovers the resolution otherwise lost up high.

Measured against synthesized tones across MIDI 40–84 at both 44.1 and 48 kHz:
**worst case 3.9 cents**, about 2.6 ms per frame, with silence and noise
correctly rejected. Confirmed since against a hardware tuner on tenor and on
voice.

Microphone input is muted while the target note sounds, so the speakers can't
answer the question for you. Echo cancellation, noise suppression and automatic
gain control are all disabled on the mic — they mangle a sustained tone.

## Browser support

Chrome, Edge and Firefox on any OS. Safari 14.1 or newer, served over
`http://localhost` or HTTPS rather than opened as a file. Everything hinges on
`getFloatTimeDomainData`, which Safari gained in 14.1.

## Repository

```
index.html                  the whole app
docs/devlog/                a dated entry per change
docs/todo/                  task lists, plus TODO_FUTURE_WORK.md
docs/CHALLENGES.md          open problems and resolved ones, with reasoning
```

`CLAUDE.md` holds the working conventions for this project.
