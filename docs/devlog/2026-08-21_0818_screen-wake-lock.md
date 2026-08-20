# Dev log — keeping the screen awake

The screen sleeping mid-practice is a real annoyance on a music stand. Solved
with the Screen Wake Lock API.

## Behaviour
A small line under the tab bar shows the state — a green dot and "Screen will
stay awake" while held, an amber warning when it can't be. Hidden entirely when
nothing is running, so it isn't noise.

**Held only while something is actually running.** Trainer listening, tuner
listening, drone sounding, or metronome ticking each register a reason in a set;
the lock is taken when the set becomes non-empty and released when it empties.
Holding it merely because the page is open would flatten a phone battery while
the app sits idle between exercises.

The drone's reason is derived inside `renderDrone()` from whether a drone
actually exists, rather than being set at each call site. Every path that
changes the drone already ends there, so the two can't fall out of step —
`startDrone()` calls `stopDrone()` internally, which would have caused churn had
the reasons been added and removed by hand.

## The part implementations usually miss
The browser releases the lock whenever the page stops being visible, and does
**not** restore it. Without a `visibilitychange` handler, switching tabs once
silently kills it for the rest of the session. Handled: returning to the page
re-requests, and while it is gone the indicator says so rather than continuing
to claim the screen is protected.

## Verification
Reference counting and the failure paths, with a stubbed lock so the lifecycle
could be watched.

| Situation | Result |
| --- | --- |
| Nothing running | indicator hidden, no lock requested |
| Metronome started | one `request("screen")`, "Screen will stay awake" |
| Drone started as well | **no second request** — one lock, two reasons |
| Metronome stopped, drone still on | no release |
| Both stopped | exactly one `release` |
| System drops the lock while hidden | amber "Screen lock dropped — come back to this tab to restore it." |
| Returning to the tab | re-requests, indicator back to normal |
| Request refused (`NotAllowedError`) | amber "Couldn't keep the screen awake (NotAllowedError)" |
| No API at all | amber "This browser won't keep the screen awake — set your screen timeout by hand." |

Also confirmed against the real API rather than the stub. The dev preview
serves the page from a `data:` URL, which is not a secure context, so
`navigator.wakeLock` is absent — and the app fell back to the advisory message
while the metronome carried on running. Failing to hold the lock never breaks
anything.

The live site was checked separately: `isSecureContext: true` and
`navigator.wakeLock` present, with a request refused only because the pane was
hidden — which is the API working as specified.

## Limits
- Keeps the **screen** awake. It won't stop a closing lid or a forced OS sleep.
- Needs HTTPS. The GitHub Pages copy qualifies; a local `file://` copy may not,
  depending on the browser.
- iOS Low Power Mode can refuse the lock outright. The indicator will say so
  rather than leaving the player to wonder.
- Untested on a real phone — the one case that matters most. Worth a look.
