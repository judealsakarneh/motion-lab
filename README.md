# Motion Lab

**Brand films and product demos where every frame is drawn in code.**

No stock footage, no image assets, no generated video. Every pixel here is React,
SVG, CSS and frame-driven maths, rendered to MP4 with [Remotion](https://remotion.dev).

![Remotion](https://img.shields.io/badge/Remotion-4-0B84F3?logo=remotion&logoColor=white)
![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)

> This repo also contains a **web** project: [`ctrl-room/`](./ctrl-room), the
> Next.js landing site for the CTRL Room loyalty card. It has its own README and
> its own `package.json`; nothing below applies to it.

---

## Start here: `RevorvaFilm`

`1920×1080 · 60fps · 600 frames · 10.0s`

A ten-second film about a failed payment being recovered. One continuous camera
move, no cuts, running from a daylit interface into a dark one. It's the piece
that best shows what this repo is for, and the three problems it solves are the
ones that separate motion that reads as *designed* from motion that reads as
*animated*:

**The camera never jerks.** Per-segment easing on a camera path is the default
and it is wrong: an ease-out curve *starts fast*, so every waypoint is a
discontinuity in acceleration that the eye reads as a bump. Using
`cubic-bezier(0.4, 0, 0.6, 1)` — zero velocity at both ends — means each segment
meets the next at matching velocity. Measured frame by frame, that took peak
acceleration from **45.27 px/f² across 4 visible spikes to 0.206 with none**.

**Motion blur is measured, not faked.** The transform is sampled at frame *n*
and frame *n−1*, and the blur is derived from how far a point at the corner of
frame actually travelled between them. That captures zoom as well as pan, which
matters because most of this film's movement *is* zoom — a pan-only measure
would report almost nothing.

**The light-to-dark transition wipes; it never blends.** Interpolating a light
palette to a dark one passes through ~1.00:1 contrast at the midpoint — the
text and its background become the same grey. That's not a tuning problem, it's
topological: any continuous path from ink-on-white to white-on-ink crosses the
point where they meet. So the film runs two independently contrast-verified
palettes and wipes a soft-edged band between them. Both ends pass WCAG AA;
there is no midpoint to fail.

---

## Every composition

| ID | Format | Length | What it is |
|---|---|---|---|
| `RevorvaFilm` | 1920×1080 @ 60fps | 10.0s | **Payment recovery film** — one unbroken move, day into night |
| `CtrlFlow` | 1920×1080 @ 30fps | 12.0s | CTRL Room product flow |
| `CtrlReel` | 1920×1080 @ 30fps | 5.0s | CTRL Room cut-down reel |
| `CtrlPromo` | 1080×1920 @ 30fps | 24.0s | **CTRL Room brand film** — "Jordan, switched on." Vertical, for Reels |
| `ZambleDemo` | 1920×1080 → **4K at `--scale=2`** @ 30fps | 18.0s | SaaS product demo, built to a millisecond spec |
| `ZambleQuad` | 1080×1920 @ 30fps | 12.0s | Four locked panels at once — **seamless loop** |
| `ZambleStory` | 1920×1080 @ 30fps | 23.0s | Night/paper worlds, shape-driven transitions |
| `ScreenCut` | 1920×1080 @ 30fps | 16.0s | Five transition mechanics inside one app shell |
| `LongTake` | 1080×1920 @ 30fps | 30.0s | Product film in one unbroken camera move |
| `ZambleTeaser` | 1080×1920 @ 30fps | 12.0s | Cyberpunk glitch teaser |
| `RambleAd` | 1080×1920 @ 30fps | 20.0s | Product film for Reels / TikTok |
| `AEDemoReel` | 1080×1080 @ 30fps | 15.0s | Six After Effects style animations, square |

---

## The house curve

Everything eases on `cubic-bezier(0.22, 1, 0.36, 1)` unless it has a reason not
to — fast out, long settle. In the After Effects Graph Editor that is
**out-influence 1, in-influence 88**, which is what [`ae/`](./ae) exists to
apply: ExtendScript that sets the same curve on real AE keyframes, so work that
starts here can finish there without the timing drifting.

Camera paths are the documented exception, for the C1-continuity reason above.

---

## Running it

```bash
npm install
npm start          # Remotion Studio - scrub the whole timeline
```

```bash
npm run build:revorva   # -> out/revorva.mp4
npm run build:demo      # ZambleDemo -> out/zamble-demo-4k.mp4 (3840x2160)
npm run build:quad      # ZambleQuad -> out/zamble-quad.mp4
npm run build:story     # ZambleStory -> out/zamble-story.mp4
npm run build:screen    # ScreenCut  -> out/zamble-screencut.mp4
```

Fonts are vendored and loaded through `FontFace` behind `delayRender`, so a
render never depends on the network being up or a CDN being reachable.

---

## Layout

```
src/
├── RevorvaFilm.tsx        the payment-recovery film
├── revorva/
│   ├── lib/theme.ts       DAY and NIGHT as two complete palettes, never mixed
│   ├── lib/timeline.ts    beats in milliseconds, easing curves, snap/anticipate
│   ├── lib/camera.ts      camera path, transform, velocity-derived blur
│   └── components/        the dashboard, rows, morphing email
├── ctrl/ · demo/ · flow/  CTRL Room and Zamble scene libraries
└── Root.tsx               composition registry

ae/                        ExtendScript: applies the house curve to AE keyframes
ctrl-room/                 Next.js site (separate project, own README)
```
