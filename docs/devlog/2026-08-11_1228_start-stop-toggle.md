# Dev log — 2026-08-11 12:28

## Task
Make Start a toggle, so a session can be stopped without reloading the page.
Previously the button became an inert "Running" label and the microphone stayed
open for as long as the tab did.

## What changed
`stop()` alongside `start()`, with the button switching between the two.

- Button text goes **Start → Stop**, and drops the `primary` accent while
  running so it stops reading as the main action
- Releases the microphone by stopping every track on the stream, so the
  browser's recording indicator goes out — the point of the whole change
- Cancels the animation frame loop, clears any pending repeat, drops the
  analyser
- Resets the display: answer re-masked, meter hidden, needle and readout
  cleared, Replay and Skip disabled, green hit state removed
- `start()` re-acquires the microphone and begins a fresh round

## Decisions
- **The drone keeps playing through a stop.** It is an independent control, and
  holding a reference pitch while not being scored is a reasonable thing to
  want. Untick it to silence it.
- **The score survives a stop**, making it a running session total rather than
  a per-run one. Easy to reset in `start()` if that turns out to be wrong.

## Verification
Driven through the real UI with a fake microphone stream.

| Check | Result |
| --- | --- |
| Button while running | "Stop", accent removed |
| Mic track after Stop | **ended** — genuinely released |
| Replay / Skip after Stop | both disabled |
| Meter, answer, hit state after Stop | hidden, re-masked, cleared |
| Status line | "Stopped — the mic is off. Press Start to go again." |
| Restart | new round ran, detection worked, 2 of 2 across the cycle |

Note for future UI testing: clicking by screenshot coordinates is unreliable
here, because revealing an answer grows the panel and shifts every button below
it. Two clicks missed for that reason before I re-screenshotted between steps.
