# Dev log — 2026-08-11 14:29

## Task
The app is live on GitHub Pages. Link it from the README.

## Live
https://bigdogco.github.io/SaxHelper/ — served from `master` at the repo root,
so every push updates it. Checked before linking: the page loads and is serving
the current build (pearl sets and the transposition selector both present).

## Changed
- `README.md` — "Try it" link at the top, and the running instructions reworked
  to lead with the hosted version rather than the local file. The Safari
  `file://` limitation now reads as a local-development footnote, which is what
  it is once a hosted copy exists.
- `docs/todo/TODO_FUTURE_WORK.md` — "publish it" struck off.

## Why it matters beyond convenience
Microphone access needs a secure context. HTTPS means Safari works, and phones
work — so the trainer can sit on a music stand instead of requiring a laptop.
Added to the home screen it opens full-screen like an app.
