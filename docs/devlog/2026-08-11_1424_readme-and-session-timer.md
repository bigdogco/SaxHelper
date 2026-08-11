# Dev log — 2026-08-11 14:24

## Task
A README, since the repo may go public, and a session timer.

## README
`README.md` covering what the app is, how to run it, how to use it, the pearl
sets, transposition, the drone (including why headphones matter and the just
intonation caveat), how the detection works and what it measures, browser
support, and the repository layout.

Deliberately no "Try it" link yet — GitHub Pages isn't enabled, and a URL that
404s is worse than none. Add one at the top once it is.

## Session timer
Runs from Start to Stop, resets on the next Start, sits under the streak line.

- Reads `Practising for 3:07` while running, `Practised for 3:07` once stopped,
  so a frozen number can't be mistaken for a live one
- `m:ss`, switching to `h:mm:ss` past an hour
- Updated from the existing animation frame loop, but only touches the DOM when
  the whole second changes

**Elapsed time is derived from a timestamp, not accumulated per frame.** That
matters more than it looks: browsers throttle `requestAnimationFrame` hard when
a tab isn't visible, so a frame-counting timer would silently lose time
whenever the player switched away. This one repaints slowly in that state but
stays accurate, and catches up the moment the tab is visible again.

## Verification
Driven through the real UI with a fake microphone.

| Check | Result |
| --- | --- |
| Counts while running | 0:06 → 0:10 across a 6 s sample |
| On Stop | froze at 0:17 and stayed there 4 s later |
| Wording after Stop | switched to past tense |
| On restart | reset — read 0:05 five seconds in, not 0:22 |
| Timer text while running | shown under the streak, `Practising for 0:14` |

A throttling artefact showed up mid-test: the display appeared stuck, and
`document.visibilityState` was `hidden` with only 4 animation frames in 5
seconds. The values were correct throughout, which is exactly the behaviour the
timestamp-based approach is meant to give.
