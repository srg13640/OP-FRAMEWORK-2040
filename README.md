# OP-FRAMEWORK-2040

A single-file, browser-native visualization of U.S. Army corps and division
operations in a 2040 operating environment. The prototype is a conversation
tool for senior-leader briefs: it juxtaposes the current doctrinal framework
— FM 3-0 / FM 3-94 / ATP 3-92, organized around Deep / Close / Rear — with
a proposed 2040 experimental construct organized around sensing,
convergence, and cross-domain effects, and lets a briefer scrub through the
five phases of a notional Indo-Pacific scenario while the graphics respond
in real time.

The whole prototype is one HTML file (~6,400 lines, ~300 KB) with inline
CSS and JavaScript. The only third-party dependency is Three.js, bundled
locally. No build step. No bundler. No package install. No network calls at
runtime. Everything — the 2D SVG views, the state machine, the phase
scrubber, the terrain projection, the 3D scene, the animation loop — is
hand-written.

---

## What it shows

Six views of the same operational environment, any of which can be the
active stage at any time:

- **PLAN** — corps-level overhead map with sensors, effects, sustainment
  nodes, and OPFOR icons. Doctrinal graphics (boundaries, objective
  arrows, phase lines) sit behind the echelon icons.
- **LAYERED** — multi-domain elevation: space, air, surface, subsurface,
  cyber. Each domain is a horizontal lane; links between domains show
  where convergence is happening.
- **NETWORK** — force topology with sensor-to-effector dependency links;
  useful for showing what breaks when a node is lost.
- **3D** — WebGL scene of the same corps problem. Phase scrubber drives
  five sequenced effect arcs between nodes (cyber, EW, strike, fires,
  sustainment), each with a distinct dashed signature and head
  silhouette. Dragable orbit camera; phase autoplay or manual stepping.
- **TERRAIN** — geographic projection (lat/lon) of the same laydown over
  an A2/AD overlay. Briefer can drag icons to declutter; layout
  persists locally.
- **SURVEY** — end-of-brief feedback capture; exports JSON or Markdown
  for handoff.

Three framework lenses can be toggled live from the header without
swapping decks:

- **DOCTRINAL** — current FM 3-0 / FM 3-94 / ATP 3-92 construct.
- **HYBRID** — transitional mix, useful for "where we are today."
- **EXPERIMENTAL** — the proposed 2040 construct.

Every view respects the currently selected framework and updates
immediately when the lens changes.

---

## Running it

Because the Three.js loader uses native ES modules, the prototype **will
not** work when opened directly via `file://`. You need a simple local
HTTP server.

```
cd OP-FRAMEWORK-2040
python3 -m http.server 8000
```

Then open `http://localhost:8000/index.html` in any current Chromium,
Firefox, Edge, or Safari 15+.

Any static web server works — `caddy file-server`, `busybox httpd -p 8000`,
`php -S localhost:8000`, `ruby -run -ehttpd . -p 8000`, or the static-server
extension in your editor. No Node / npm step.

If the local Three.js files are missing, the page falls through to a CDN
copy as a backup. For air-gapped or controlled environments, keep
`lib/three.js/build/` intact and the prototype runs entirely offline.

---

## Keyboard shortcuts

| Key | Action |
|---|---|
| `V` | Cycle to the next view |
| `L` | Toggle the LEGEND panel |
| `F` | Toggle FOCUS mode (hide both side panels) |
| `M` | Toggle MCOO overlay |
| `P` | Toggle PREP mode (PLAN view — drag-to-reposition icons) |
| `T` | Open the guided TOUR / feedback panel |
| `[` and `]` | Step the element stepper (non-3D, non-SURVEY views) |
| `Space` | Play / pause the phase scrubber (3D view) |
| `← / →` | Step one phase (3D view) |

The header has click equivalents for every toggle.

---

## Feedback flow

The in-app **TOUR** walks a new viewer through the prototype one surface
at a time, asks for ratings and free-text notes, and stores responses in
the browser's `localStorage`. At the end the briefer can export
everything as JSON or Markdown from the SURVEY view. No data leaves the
browser.

Local persistence keys used (documented in case you want to wipe state):

```
oe2040-intro-ack-v1           intro overlay dismissal
oe2040-tour-v1                 TOUR answers
oe2040-survey-v1               SURVEY submissions
oe2040-survey-v1-draft         SURVEY in-progress draft
oe2040-terrain-layout-v1       TERRAIN briefer-arranged icon positions
oe2040-view-layouts-v1         per-view icon positions (PLAN active)
oe2040-icon-overrides-v1       user-authored echelon-amplifier text
```

Clearing site data for your local host fully resets the prototype.

---

## Repository layout

```
OP-FRAMEWORK-2040/
├── README.md                  this file
├── LICENSE                    MIT (for index.html)
├── .gitignore
├── index.html                 the entire prototype
└── lib/
    └── three.js/
        ├── LICENSE            Three.js MIT
        └── build/
            ├── three.module.min.js
            └── three.core.min.js
```

That's everything. ~1.1 MB total. No node_modules, no dist/, no build
output, no hidden service dependencies.

---

## Architecture notes

- **State.** One `state` object holds view / framework / phase / selection
  / layout overrides. A single `render()` / `rebuild3D()` function is
  driven off it. Most UI is declarative over that state.
- **2D.** Hand-written SVG. Icons are NATO-style symbols built from
  primitives (rectangles, lines, text) — no external icon library.
- **3D.** Three.js r160, loaded as an ES module. The scene is rebuilt
  per phase; sprites and lines own their own `userData` so the animation
  tick can advance them in-place each frame.
- **Phase scrubber.** Five phases, 8 seconds per phase on autoplay
  (tuned for live-brief pacing). Manual stepping via Space / arrows /
  phase chips remains immediate.
- **No framework.** No React, no Vue, no bundler. Every piece of
  behaviour in the prototype is greppable in `index.html`.

---

## Doctrinal basis

The doctrinal lens follows:

- FM 3-0, *Operations*
- FM 3-94, *Armies, Corps, and Division Operations*
- ATP 3-92, *Corps Operations*
- MIL-STD-2525D / APP-6(D) symbology conventions

The experimental 2040 lens is not doctrinal. It is a working proposal
built to stimulate senior-leader conversation, not to replace current
doctrine.

---

## Status

Prototype. Single author. Built as a conversation tool for senior-leader
briefs, not as a deployable planning product. Feedback is welcome via
the in-app TOUR, via GitHub Issues, or directly.

---

## License

MIT — see `LICENSE`.

Three.js is MIT licensed by its authors — see `lib/three.js/LICENSE`.
