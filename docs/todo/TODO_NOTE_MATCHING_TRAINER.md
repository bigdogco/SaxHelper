# TODO — Note matching trainer

First version of the core exercise: hear a hidden note, play or sing it back,
get confirmation when it locks in.

## Done

- [x] Single-file app skeleton (`index.html`), no build step, no dependencies
- [x] Synthesize the target note with a reed-like tone
- [x] Random note selection from a configurable range
- [x] Microphone capture with browser processing (AGC, noise suppression, echo
      cancellation) disabled
- [x] Real-time pitch detection — YIN, worst case 3.9 cents over MIDI 40–84
- [x] Keep the answer masked until it is matched
- [x] Gate the mic while the target note plays, so the speakers can't answer
- [x] Tolerance in cents, adjustable 10–100
- [x] Hold-steady timer with a live countdown
- [x] Success state: green panel, revealed note, victory arpeggio
- [x] Tuner meter with a tolerance zone, hideable for pure ear training
- [x] Live readout of heard frequency, note name and cents deviation
- [x] Accept-any-octave option, for answering by voice
- [x] Auto-advance after a hit, and no-immediate-repeat
- [x] Streak / accuracy counter
- [x] Replay and skip, with `Space` and `N` keyboard shortcuts
- [x] Transposition: tenor, alto, soprano/clarinet/trumpet, bari, concert
- [x] Show written and concert pitch together throughout
- [x] Flat note spellings, for B♭ and E♭ instruments
- [x] Warn when the selected range falls outside the horn's playable range
- [x] Graceful message when mic permission is denied
- [x] Verify the full loop end-to-end with a synthetic microphone
- [x] Restrict practice to the six pearl keys, plus a set for each hand
- [x] Verify set selection over 900 recorded rounds
- [x] Play through it with the actual tenor and see how the tracker holds up —
      confirmed against a hardware tuner, tracks closely, no changes needed

## Next up

- [ ] Decide whether the default low end should move to concert A♭2, so the
      default range is fully playable on tenor
- [ ] Try it with the voice, which is still untested
- [ ] Decide whether the pearl sets should hide when the instrument is set to
      concert pitch, where the fingering names mean nothing

Anything beyond that goes in `TODO_FUTURE_WORK.md`.
