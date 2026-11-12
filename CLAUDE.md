# Portfolio site — project notes

A hand-coded, single-page portfolio homepage (no build step, no framework). Just
`index.html` + `styles.css`, styled like a stacked case-study feed: nav, serif
hero line, three illustrated project panels with captions, a dark footer.

## Project goal
This is a prototype for fleshing out the interaction model (navigation,
page/section structure, content layout) — not a site being prepped for
publishing. Dead/stub links (`#resume`, `#linkedin`, `#email`, `#x`, `#arena`,
`#youtube`, etc.) are fine as-is and don't need to be wired up unless asked.

## Files
- `index.html` — all markup, plus an inline `<script>` at the bottom that
  powers in-page text editing (see below). Don't move that script into a
  separate file; it depends on being the last thing in `<body>`.
- `styles.css` — all styling. Organized top-to-bottom roughly in page order,
  with a `/* ---------- Responsive ---------- */` block at the end for the
  mobile breakpoint (`max-width: 720px`).
- `serve.py` — local dev server (stdlib only, no dependencies). Serves the
  site AND handles saving in-page text edits back to `index.html`.
- `assets/` — images referenced by the page (e.g. project thumbnails).

## Previewing changes
Run `python3 serve.py` from this directory, then open `http://localhost:8743`.
Plain `python3 -m http.server` also works for just looking at the site, but
won't support the in-page "Edit Text" save feature below.

Opening `index.html` directly (double-click, or `file://`) mostly works in a
real browser, but the in-app Claude Code browser preview renders `file://`
pages as a static snapshot and won't load `styles.css` or fonts — always
preview through the `serve.py` server instead.

**Caching gotcha in the Claude Code browser pane:** it aggressively caches
`styles.css` per session/tab. After editing CSS, a plain reload may show
stale styles. Fix: open a fresh tab, or swap the stylesheet `<link>` via JS
with a cache-busting query string before taking a screenshot.

## In-page text editing ("Edit Text" mode)
There's a lightweight WYSIWYG-lite editor built into the page itself (bottom
of `index.html`, `#edit-toolbar`): click **Edit Text** (top-right of the live
page), click any text to edit it inline, click **Save Changes** to write the
change straight back to `index.html` via `serve.py`'s `/__save` endpoint. A
`.bak` copy of the previous version is kept on each save.

Known quirk: contentEditable's default Enter-key behavior can insert a
`<div>` where a `<br>` was intended, producing invalid nested markup (e.g. a
`<div>` inside a `<p>`). Check the diff after a text-edit session and clean
up any stray `<div>`/empty `class=""` artifacts if present.

## Design decisions worth knowing
- The project `.scene` panels use CSS `aspect-ratio` to avoid content
  clipping; `.scene--audio` (a real photo thumbnail) has its own taller
  ratio — don't reset it to a shared value without re-checking for overflow
  at narrow (~660–720px) viewport widths, which is where clipping tends to
  show up first.
- Real photo/illustration thumbnails (like `.scene--audio`'s image) use
  `object-fit: contain` with an `aspect-ratio` matching the image's native
  pixel dimensions exactly — this guarantees the full image is always shown,
  never cropped, at any viewport width.
- There used to be a third project panel (a file-manager mockup) and a
  fixed floating phone widget in the lower-right corner; both were removed
  since the panel didn't link anywhere and the widget overlapped the
  footer's "Socials" column on mobile.

## Owner
Site belongs to Lise McMillan (footer tagline / name was set via the Edit
Text tool).
