# Tom Riddle's Diary — Single-File Tablet Edition

> *The diary of Tom Marvolo Riddle for tablets.* Write with your finger or stylus — after a pause the diary drinks your ink and replies in Tom's flowing hand.

This is a **single-file remake** of [MaximeRivest/riddle](https://github.com/MaximeRivest/riddle) — ported from Rust/reMarkable Paper Pro to one `index.html` you can open or deploy anywhere. No build, no server.

![paper](https://img.shields.io/badge/paper-1943-black) ![single%20file-58kB-lightgrey) ![license-MIT-blue)

---

### Demo
Just open `index.html` on a tablet, or deploy it:
- **Netlify Drop:** drag `index.html` to https://app.netlify.com/drop → share the link → Add to Home Screen for fullscreen
- **Any static host:** GitHub Pages, Cloudflare Pages, Vercel — it's one file

### What it does
- **Write → rest 2.8s → drinks ink** — deterministic pixel-hash dissolve (14 stages) like the original
- **Thinking blot** pulses in the center while the oracle streams
- **Reply animates** letter-by-letter in `Dancing Script`, then lingers and fades
- **Memory:** last ~400 pages stored in `localStorage` (`riddle_memories_v1`) — Tom remembers, you can write *“show me what I wrote about the garden”* or *“what do you remember?”*
- **Eraser:** toggle button or stylus eraser, splits stored strokes
- **`?` gesture:** draw a large `?` to open the guide
- **Book-faithful Tom:** 16, Head Boy Slytherin since 1943, charming/lonely on surface, slowly steering toward Chamber/Hagrid/lineage. Talks 3-6 sentences. Writes `TOM MARVOLO RIDDLE` then letters **slide horizontally** on the parchment to `I AM LORD VOLDEMORT` when you ask *who are you*.

### Cover
Black leather with `T. M. RIDDLE` embossed + `1943 — T M RIDDLE` spine. Tap to open. Red ink bleed when Tom is possessive.

### How it works
```
stylus (PointerEvents, pressure 0-1)
  │ strokes → idle 2.8s → crop PNG (bboxed, downscale ≤800px long side, grayscale)
  ▼
oracle (any OpenAI-compatible /chat/completions, streamed SSE sentence-by-sentence)
  │ StreamParser: handles ⁂ transcription + ⟦show:N⟧ conjure directive
  ▼ handwriting (wrap → measureText → Dancing Script → wobble)
reply fades → memory saved
```

Persona (`PERSONA` + `MEMORY_PROTOCOL`) is built from `src/oracle.rs` — includes catalog + transcription protocol. See `index.html:161`.

### Quick Start (zero-setup for your partner)

**1. Get a free vision key — no card:**

**Groq (recommended, fastest):**
1. https://console.groq.com/keys → Create → copy `gsk_...`
2. Model: `qwen/qwen3.8-27b` (vision, verified streaming)

**Alternative — Google Gemini direct:**
1. https://aistudio.google.com/app/apikey → Create → `AIza...`
2. Base `https://generativelanguage.googleapis.com/v1beta/openai/` Model `gemini-2.0-flash`

**2. Bake it so they do nothing:**
Open `index.html`, find:
```js
const BAKED_DEFAULTS = {
  key: "gsk_...", // <- paste here
  base: "https://api.groq.com/openai/v1",
  model: "qwen/qwen3.8-27b",
}
```
Save. On first open it auto-saves to `localStorage` — they never see `⚙`.

**3. Deploy:**
Drag `index.html` to https://app.netlify.com/drop → get `https://xxx.netlify.app` → send link. Works offline after first load (fonts cached).

If the file shows old settings, hard refresh or run in console: `localStorage.clear(); location.reload()`. `Test oracle` in `⚙` should say `Oracle replied:`.

### Running locally
```bash
# just open it
start index.html
# or serve (avoids file:// CORS for fonts in some browsers)
npx serve .
```

### Configuration
Tap `⚙`:
- `API key` — stored in `localStorage` only, sent only to your Base URL
- `Base URL` — e.g. `https://api.groq.com/openai/v1` or `https://generativelanguage.googleapis.com/v1beta/openai/`
- `Model` — must be vision-capable. Fallback on 503/429 auto-retries `qwen3.8 → qwen3.6`
- `Remember pages`, `Timezone offset`, `Forget all memories`

Rate limits: Groq free ~14k/day, Gemini free generous. 503/429 shows *“The oracle is overwhelmed — wait a moment”*.

### Privacy
- Handwriting PNG sent only to *your* configured oracle. No telemetry.
- Memories live only in browser `localStorage` (`riddle_memories_v1`). Delete in `⚙` → Forget.
- If you bake a key into HTML, anyone with the link can View Source → see it. Use a secret URL.

### Customizing the soul
Edit `PERSONA` in `index.html:161`. The current book-faithful persona is 3-6 sentences, 1940s formal British, with Chamber/Voldemort lore. Keep `MEMORY_PROTOCOL` if you want conjure.

### Credits
- Original `riddle` by [Maxime Rivest](https://github.com/MaximeRivest/riddle) — Rust, e-ink, Dancing Script → Zhang-Suen thinning
- This port: canvas + PointerEvents + Groq/Gemini streaming in one file
- Font: [Dancing Script](https://github.com/googlefonts/DancingScript) (OFL), [Crimson Pro](https://fonts.google.com/specimen/Crimson+Pro)

### License
MIT — same as original. Vendor e-ink libs not needed here.
