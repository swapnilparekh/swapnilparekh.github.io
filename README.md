# swapnilparekh.github.io

Personal website for Swapnil Parekh — AI Scientist at Intuit, researcher in mechanistic interpretability, adversarial robustness, and agentic systems.

---

## Site Structure

The site is a **Jekyll blog** deployed via GitHub Pages. It has two layers:

1. **Jekyll site** — standard blog/research/about pages, compiled from Markdown
2. **`concert.html`** — a standalone interactive experience (described in detail below)

### Jekyll Pages

| File | Description |
|------|-------------|
| `index.html` / `_posts/` | Blog post listing and 7 technical posts |
| `about.md` | Bio, research themes, music table |
| `research.md` | Full paper list with abstracts |
| `style.css` | Global stylesheet (Inter + JetBrains Mono) |
| `_layouts/default.html` | Shared layout with nav (includes 🎹 link to concert) |
| `_config.yml` | Site metadata |

---

## concert.html — The Concert Hall

`concert.html` is a self-contained ~3000-line interactive portfolio page styled as a **concert programme**. No framework, no build step — it's a single HTML file with embedded CSS and vanilla JS.

### Concept

Every section maps to a musical metaphor:

| Section | Musical Metaphor | Content |
|---------|-----------------|---------|
| FOYER | Lobby | Bio, quick stats |
| PRELUDE | Programme notes | Research areas, Chopin quote |
| PERFORMANCES | Live recital | Work experience |
| COMPOSITIONS | Published works | Research papers |
| LINER NOTES | Album liner notes | Blog posts |
| REPERTOIRE | Sheet music | Technical skills |
| ACCOLADES | Standing ovation | Awards & recognition |
| CONSERVATORY | Training | Education |

### Pixel Art Pianist Sprite

A **16×16 pixel sprite sheet** (`data:image/png` embedded) animates a pianist:

- **Idle**: rocking left-right at the keyboard
- **Walking**: cycles through walk frames as you scroll down the page
- **Sitting at piano**: when reaching the PERFORMANCES section
- **Bowing**: at the final "Say Hi" CTA — sprite walks to the button and bows

The sprite follows a `scrollY → position` mapping, rendered on a `<canvas>` element pinned to the bottom of the viewport.

### Web Audio Engine

Each section triggers a different piece of classical music, synthesized in real-time using the **Web Audio API** (no audio files):

| Section | Piece |
|---------|-------|
| FOYER | Beethoven — Moonlight Sonata Op.27 No.2 |
| PRELUDE | Chopin — Nocturne Op.9 No.2 |
| PERFORMANCES | Debussy — Clair de Lune |
| COMPOSITIONS | Satie — Gymnopédie No.1 |
| LINER NOTES | Bach — Goldberg Variations BWV 988 |
| REPERTOIRE | Liszt — Consolation No.3 |
| ACCOLADES | Schubert — Ave Maria |
| CONSERVATORY | Mozart — Piano Sonata K.331 |

Pieces use oscillator banks with envelope shaping (attack/sustain/release), harmonic overtones, and smooth crossfades between sections. Music continues uninterrupted if you scroll back to a previous section.

### Programme Map

A fixed sidebar on the right edge shows a **vertical map** of all sections. The current section is highlighted. Clicking a label smooth-scrolls to that section. On mobile it collapses to a minimal indicator.

### Design System

Built on a **brutalist concert programme** aesthetic:

- **Typography**: `JetBrains Mono` for labels/metadata, `Inter` for body text
- **Colors**:
  - `--ivory` `#f5f0e8` — background
  - `--ebony` `#1a1a2e` — foreground/borders
  - `--gold` `#c9a84c` — accent (Chopin quote border, highlights)
  - `--velvet` `#4a0e2d` — LINER NOTES section accent
  - `--crimson` `#8b1a2e` — ACCOLADES section accent
  - `--slate` `#6b7280` — secondary text
  - `--mist` `#e8e4dd` — card backgrounds
- **Brutalist cards** (`.brut-card`): solid `4px` borders + `8px` hard box-shadow, no border-radius
- **Reveal animations**: `data-reveal` / `data-revealed` attributes + `IntersectionObserver`; elements fade+slide up as they enter the viewport

### Key CSS Classes

```
.brut-card          — Base card: solid borders + hard box-shadow
.brut-btn           — CTA button: uppercase mono, filled on hover
.perf__body         — Employment card: 2-column grid (company | description)
.liner              — Blog post card with velvet header bar
.research-area      — Research theme card (3-column grid in PRELUDE)
.chopin-quote       — Gold-bordered blockquote
.programme-map      — Fixed right sidebar with section labels
.sprite-canvas      — Pinned canvas for the walking pianist
```

### Sections in Detail

#### FOYER
- Animated entrance title with staggered letter reveal
- Bio paragraph (AI Scientist, IBM Research, NYU MS, MCP at Intuit)
- Research keyword subtitle

#### PRELUDE
- Chopin quote: *"Simplicity is the final achievement…"*
- 3-column research areas grid (Mechanistic Interpretability · Adversarial Robustness · Agentic & Latent Reasoning)
- Location/role metadata line

#### PERFORMANCES (Work Experience)
- Intuit — AI Scientist (2024–present)
- IBM Research — Research Scientist (2022–2024)
- NYU — Graduate Researcher (2020–2022)
- Each card: full-width 2-column layout with company/role/date on left, achievements on right

#### COMPOSITIONS (Research Papers)
- 12 papers rendered as brutalist cards with venue badges
- Sorted newest-first
- Each: title, venue, year, abstract excerpt, DOI/arXiv link

#### LINER NOTES (Blog Posts)
- 7 posts from `_posts/` rendered as cards
- Velvet-colored header bars with date + topic tag
- Excerpt + "↗ READ" link to Jekyll post

#### REPERTOIRE (Skills)
- Languages, frameworks, tools in grouped rows
- Proficiency levels indicated visually

#### ACCOLADES (Awards)
- Kaggle Expert, citations count, open-source contributions

#### CONSERVATORY (Education)
- NYU MS, undergrad — with GPA, thesis topics

#### FINALE (Say Hi)
- Final CTA with email link
- Sprite bows next to the button

---

## Local Development

```bash
# Install dependencies
bundle install

# Serve Jekyll site (port 4000)
bundle exec jekyll serve

# Or serve concert.html directly (port 7654 used during development)
python3 -m http.server 7654
```

No build step needed for `concert.html` — open it directly in a browser or serve statically.

---

## Deployment

Deployed automatically via **GitHub Pages** on push to `master`. Jekyll build runs server-side; `concert.html` is served as a static file alongside the compiled Jekyll output.

---

## Browser Support

`concert.html` requires:
- **Web Audio API** — Chrome 35+, Firefox 25+, Safari 14.1+
- **CSS Grid** — all modern browsers
- **IntersectionObserver** — Chrome 58+, Firefox 55+, Safari 12.1+
- **Canvas 2D** — universal

No polyfills included. The page degrades gracefully if audio isn't available (no AudioContext error surfaced to the user).
