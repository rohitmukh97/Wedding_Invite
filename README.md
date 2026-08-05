# Susmita & Rohit - Wedding Invite

4 & 5 December 2026 - Kolkata

One self-contained HTML file. No build step, no npm, no frameworks, no paid services. Open `index.html` and it works. Built fresh - the engagement invite in `../Examples/` was reference only, nothing was inherited from it.

---

## How it works

**Intro screen.** The `#SuRo` monogram is itself the button. It has no hard border: a radial halo behind it fades to nothing at the edges, with a thin gold ring that dissolves at left and right. Under it: who is invited, the dates, and the four control buttons. Tapping the monogram runs a 2-second alpona line-draw, then the invitation appears.

**Four looks.** Two palettes and two modes, all four combinations valid:

| | Light | Dark |
|---|---|---|
| **Bengali Wedding** | garad ivory, alta red, temple gold | maroon-black, gold, soft red |
| **Premium Mauve** | lilac mist, mauve, champagne | aubergine-black, champagne, lilac |

Palette and mode are separate buttons, so dark mode works on top of whichever palette is active. **Both persist across a refresh** (`localStorage`). Language does not - every visit starts in English by design, so a guest never lands on a script they cannot read.

**Every switch is gradual, never a snap.** Changing palette or mode adds a `theming` class that gives every element on the page a 1.05s eased transition on colour, background, border, shadow, fill and stroke, so the accent literally walks from alta red to mauve rather than jumping. A soft radial wash rises to 40% over the moment of change and clears again, and the falling petals are retinted underneath it where the swap is hidden. Changing language dissolves the page to 4% with a 3px blur, swaps the script while it is invisible, then reforms it - about 1.5s end to end, and the guest never sees the text itself change. All of it collapses to an instant swap under `prefers-reduced-motion`.

**Section order:** hero → portrait → blessing → our story → film 1 → save the dates → venues → photo pair → the four celebrations → film 2 → gallery → closing.

**Media placement.** Spread through the scroll *and* collected at the end, so nobody can skip past everything:

- Photo 3 as an arch-framed portrait under the names
- Film 1 right after the story ends on "we are getting married"
- Photos 2 and 1 as a pair between the venues and the events
- Film 2 just before the closing
- All five again in the gallery near the end

---

## The videos are silent, permanently

Three layers, so it cannot fail:

1. **The mp4 files have no audio track at all.** Stripped with `ffmpeg -an`. Not muted - absent. There is nothing to unmute.
2. `muted loop playsinline disableremoteplayback`, no controls, and the `muted` property is set in JavaScript too.
3. A `volumechange` listener that forces mute back on if anything ever changes it.

They autoplay when scrolled into view and pause when they leave, so nothing plays off-screen or burns battery. The `<source>` is only attached when a video approaches the viewport, so the page costs almost nothing until then.

---

## Files

```
index.html                  the entire invite
assets/img/photo1-3.jpg     your photos, web-sized
assets/img/venue-qr.svg     QR to the wedding venue (not currently used in the page)
assets/video/film1-2.mp4    your clips, re-encoded and silent
assets/video/*-poster.jpg   still frames shown before autoplay starts
.nojekyll                   stops GitHub Pages running Jekyll
```

Total about 2.9 MB, almost all of it the two videos.

---

## Publish free on GitHub Pages

1. Create a **public** repo, for example `wedding`.
2. Upload the *contents* of this folder to the repo root. `index.html` must sit at the top level, not inside a subfolder.
3. **Settings → Pages → Source: Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
4. A minute later it is live at `https://<your-username>.github.io/<repo-name>/`

```bash
cd "Wedding invite html files"
git init && git add -A && git commit -m "Wedding invite"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

### Fix this after publishing

`og:image` is a relative path. WhatsApp and iMessage need an absolute URL for the link preview to show a photo. Once you know the live URL, change that one line:

```html
<meta property="og:image" content="https://<your-username>.github.io/<repo-name>/assets/img/photo3.jpg">
```

Send yourself the link and check the preview before sending it to anyone else.

---

## Editing

Every translated string is a pair of sibling spans:

```html
<span class="en">9:00 AM onwards</span><span class="bn">সকাল ৯টা থেকে</span>
```

Change both or the two languages drift apart.

Colours are all CSS variables at the top of the `<style>` block, four sets: `:root` (Bengali light), `[data-theme="dark"]`, `[data-palette="mauve"]`, and `[data-palette="mauve"][data-theme="dark"]`. Change a palette in one place and the whole page follows.

## Replacing the clips later

The current ones came off WhatsApp at 848x478, which is soft, which is why they sit in a contained frame rather than full-bleed. If you get the originals off the phone:

```bash
ffmpeg -i input.mov -an -c:v libx264 -crf 26 -preset slow -pix_fmt yuv420p \
  -movflags +faststart assets/video/film1.mp4
ffmpeg -ss 1 -i input.mov -frames:v 1 -q:v 5 assets/video/film1-poster.jpg
```

Keep `-an`. That is what guarantees silence.
