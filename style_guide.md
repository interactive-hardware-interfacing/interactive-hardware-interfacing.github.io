# Interactive Hardware Interfacing — Site Style Guide

Nov 5, 2026 · @L. Linkous

## Purpose

This guide keeps the landing page consistent as tools are added or removed. The site is one file, `index.html`, in the [interactive-hardware-interfacing.github.io](https://github.com/interactive-hardware-interfacing/interactive-hardware-interfacing.github.io) repo, served by GitHub Pages at [interactive-hardware-interfacing.github.io](https://interactive-hardware-interfacing.github.io/). No build step: edit the file, commit, done.

The companion org profile README lives in the `.github` repo at `profile/README.md`. When a tool is added or removed, update both.

## Color rules

The page is grayscale with four accents, and each accent has exactly one meaning. The pops work because they are scarce; a new element gets a color only if it fits one of these jobs.

| Accent | Meaning | Where it appears |
| --- | --- | --- |
| Cyan | The **Wireless communication** track, and the site's lead accent | Hero eyebrow and the word *browser*, primary buttons, wireless section swatch, wireless hardware chips, wireless Open buttons, wireless card hover border, footer link hover, the *students* tag |
| Yellow | The **Electronics lab** track | Lab section swatch, lab hardware chips, lab Open buttons, lab card hover border, the *instructors* tag |
| Magenta | **The live terminal** (anything that reads as an interactive session) | Terminal prompt `>`, blinking cursor, terminal glow, first window dot, active session indicator, the *tinkerers* tag |
| Green | **Works now** (ready, passing, no hardware needed) | `demo mode` badge on every card, `OK` and `CRC OK` in terminal output, ok/win counts |

Rules of thumb:

- One accent per track. A new track gets its own accent; it does not share cyan or yellow.
- Green is a status, not a track. Never use it for a section.
- Magenta is the terminal, not a track. If you add a second code-like block, it uses magenta too.
- Inside the terminal, numbers are yellow and hex bytes are cyan. That is syntax coloring and does not change the meaning of those accents elsewhere.
- Everything else (text, borders, card backgrounds, chips with no track) stays in the gray scale. If something looks bland, fix the hierarchy or spacing before reaching for a color.
- Accent-on-dark buttons use dark text (`#04141a` on cyan, `#1a1500` on yellow), never white, for contrast.
- Background is near-black, never pure `#000`. Cards sit one step lighter with a 1px border, no drop shadows (the terminal glow is the single exception).

## Palette

All colors are CSS custom properties in the `:root` block at the top of `index.html`. Change a value there and it updates everywhere.

| Variable | Hex | Role |
| --- | --- | --- |
| `--bg` | `#0b0d10` | Page background |
| `--bg-2` | `#12151a` | Tiles and "How" panels |
| `--card` | `#14181e` | Tool cards |
| `--card-hover` | `#191e26` | Tool card on hover |
| `--line` | `#262c36` | Default borders, hero graticule |
| `--line-strong` | `#353d4a` | Button and chip borders |
| `--text` | `#e8ebf0` | Body text |
| `--muted` | `#9aa3b2` | Secondary text, card descriptions |
| `--faint` | `#6b7483` | Fine print, card numbers, terminal comments |
| `--cyan` | `#22d3ee` | Wireless track / lead accent |
| `--magenta` | `#f472b6` | Terminal |
| `--green` | `#4ade80` | Works now / demo mode |
| `--yellow` | `#facc15` | Electronics lab track |

Each accent also has a `-dim` variant at 12% opacity (`--cyan-dim`, `--magenta-dim`, `--green-dim`, `--yellow-dim`) used as the fill behind chips and tags. Chips use the dim fill with the full accent as text and no border.

Terminal-only colors (background `#07090c`, text `#cbd3de`) are hard-coded in `.term` and `.term-body` because nothing else uses them.

## Adding or editing a tool card

Cards are not written by hand. They are generated from two JavaScript arrays near the bottom of `index.html`: `wireless` and `lab`. To add a tool, add one object to the right array and nothing else.

```javascript
{ slug: "telemetry-demo",          // repo name; builds both the Open and Source links
  name: "Telemetry",               // short display name, 1–3 words
  teaches: "<b>Sensor data over the air.</b> An ultrasonic sensor streams distance to a second board; see the reading beside the raw bytes that carried it.",
  hw: "2 × XIAO ESP32-C3 + HC-SR04" }
```

Field rules:

- `slug` must match the repo name exactly. Open goes to `https://interactive-hardware-interfacing.github.io/<slug>/` and Source to `https://github.com/interactive-hardware-interfacing/<slug>`.
- `teaches` starts with a bold 2–5 word phrase naming the concept, then one or two plain sentences. Keep it under 30 words so cards stay the same height.
- `hw` is the count and board, using `×` (not `x`) and an en dash for ranges: `1–2 × XIAO ESP32-C3`. Add a sensor with `+`.
- Order in the array is display order. Wireless cards are numbered automatically (`01`, `02`...), so keep them in teaching order.
- Every card gets a `demo mode` badge automatically. If a tool has no simulator, add a `nodemo: true` field and a small `if` in `card()` to skip the badge; do not just leave a misleading badge.
- To remove a tool, delete its object. Do not comment it out and leave it; the repo list is the source of truth.
- A track with one card renders it as a wide card (the `lab` array passes `wide = true`). Once a second lab tool exists, drop the `true` argument in the `lab.map(...)` line so it becomes a normal grid.

## Adding a new track

A track is a section of tools with its own accent. Adding one takes five edits, all in `index.html`:

1. Pick an unused accent. Cyan and yellow are taken; green and magenta are reserved for status and terminal. Add a new pair to `:root`, for example `--orange: #fb923c;` and `--orange-dim: rgba(251, 146, 60, .12);`. Choose something that stays legible as text on `#14181e`.
2. Add the track's CSS by copying the four `.track-yellow` rules and renaming them `.track-orange`: section swatch, `.chip.hw`, `.btn.open` (with a dark text color), and `.card:hover` border.
3. Add a `<section id="..." class="track-orange">` after the last track, with a `.sec-head` (h2 with swatch, one-sentence description) and an empty `<div class="grid" id="...-grid">`.
4. Add a new array in the script and a matching `document.getElementById(...).innerHTML = ...` line.
5. Add the section to the top nav and, if the track changes the pitch, to the hero lede.

If a new track also needs a tile in the "Start here" strip, keep it to three tiles; replace one rather than adding a fourth so the row stays even.

## Terminal block

The hero terminal cycles through the `sessions` array in the script, one every 6 seconds, with a dot per session underneath. Each entry has a `title` (shown in the window bar) and `lines` (HTML with span classes for color).

| Span class | Color | Use for |
| --- | --- | --- |
| `p` | magenta | the prompt `>` |
| `c` | white, bold | a command the user typed |
| `ok` | green | `OK`, `CRC OK`, passing counts |
| `num` | yellow | numbers and measurements |
| `hex` | cyan | raw bytes |
| `dim` | gray | comments, headers, `# ...` lines, lost-packet notes |

Tips:

- Keep every session to 8 or 9 lines so the block does not change height as it cycles. The `min-height` on `.term-body` is tuned for that.
- End each session with `<span class="p">&gt;</span> <span class="cursor"></span>` so the cursor blinks on the last line.
- Use real commands from the tool's `PROTOCOL.md`. Values can be representative, but the format should be the one the board actually prints.
- Three sessions is a good number: one per track plus one that shows raw bytes. Four is the most the dots can hold without looking cluttered.
- The rotation stops automatically for visitors with reduced-motion enabled; the dots still work as manual tabs.
- `&gt;` must stay escaped inside the template strings or the browser will read it as a tag.

## Writing tips

The voice is plain and a little dry. Say what the thing does, not how exciting it is.

- **Card names** are nouns, 1–3 words, no "demo" suffix: *Packet Inspector*, not *Packet Inspector Demo*. The repo slug keeps its `-demo`.
- **Card descriptions** open with the bold concept (*Unicast vs. broadcast.*) then one or two sentences in second person or imperative: *Walk one board away and watch RSSI fall.* No marketing adjectives.
- **Section descriptions** are one or two sentences and say what the track teaches and on which hardware.
- **Hardware chips** are the only place a board name appears on a card. Do not repeat the board in the description.
- **Headings** are sentence case: *Wireless communication*, not *Wireless Communication*.
- **Buttons** are verbs: *Open ▶*, *Source*, *Browse the tools*. Keep the ▶ on Open only.
- Use the real characters: en dash for ranges (`1–2`), multiplication sign for counts (`2 ×`), curly apostrophes come from the font automatically.
- Mention Chrome/Edge and Web Serial once, in the hero fine print. Individual cards should not repeat the browser requirement.
- The hero has a commented-out line for course or institution context. Keep the page institution-neutral unless there is a reason to name one, so other instructors feel free to fork.

## Layout and typography

- **Fonts:** Atkinson Hyperlegible for text, Spline Sans Mono for code, chips, tags, labels and the terminal. Both load from Google Fonts in the `<head>`. Mono is used for anything that reads as a label or a value; sans for anything that reads as a sentence.
- **Width:** content caps at `--max: 1080px` with 24px side padding.
- **Radius:** `--radius: 12px` on cards, panels and the terminal; 9px on buttons; pills on chips.
- **Card grid:** `auto-fill, minmax(300px, 1fr)`, so three across on desktop, two on tablet, one on phone.
- **Breakpoint:** at 820px the hero stacks, the Start-here strip stacks, wide cards stack, and the top nav hides (section links are still reachable by scrolling).
- **Hero graticule:** a 48px grid at 28% opacity, masked to fade out toward the right and bottom. It is on the hero only; do not extend it to the rest of the page.
- **Motion:** card lift on hover, cursor blink, terminal rotation. All three are disabled under `prefers-reduced-motion`. Keep any new animation behind the same media query.
- **Dark only.** The old light theme was removed. Do not reintroduce a `prefers-color-scheme: light` block; the accent system was tuned for the dark background.

## Checklist before publishing

- [ ] Every card's `slug` matches a public repo and its GitHub Pages site is live
- [ ] The org README (`.github/profile/README.md`) lists the same tools as the page
- [ ] New colors, if any, are in `:root` and have a `-dim` variant
- [ ] Card descriptions are under 30 words and start with a bold concept
- [ ] Terminal sessions are 8–9 lines and end with the blinking cursor
- [ ] Page checked at desktop width and on a phone, and with reduced motion on
- [ ] Open the file over `http://localhost:8000`, not `file://`, so fonts and links behave as they will on Pages
