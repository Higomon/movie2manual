<div align="center">

# 🎬 movie2manual

**Turn a how-to video into a step-by-step manual — with auto-captured screenshots — entirely in your browser.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Powered by Google Gemini](https://img.shields.io/badge/Powered%20by-Google%20Gemini-4285F4?logo=googlegemini&logoColor=white)](https://ai.google.dev/)
[![Single HTML file](https://img.shields.io/badge/single-HTML%20file-E34F26?logo=html5&logoColor=white)](index.html)
[![Built with Claude Code](https://img.shields.io/badge/Built%20with-Claude%20Code-D97757?logo=anthropic&logoColor=white)](https://claude.com/claude-code)

[日本語](README.md) · **English**

### ▶️ Use it now → **https://higomon.github.io/movie2manual/**

</div>

---

Give it a screen recording and the AI (Gemini) builds a step-by-step manual — chapters, steps, descriptions, plus a screenshot pulled from the video for each step. No install; runs entirely in your browser.

## How to use

> Open it in **Chrome / Edge**.

1. **🔑 Get a key** — create a free Gemini API key at [Google AI Studio](https://aistudio.google.com/apikey) (copy the `AIza…` string)
2. **🌐 Open the app** — **[higomon.github.io/movie2manual](https://higomon.github.io/movie2manual/)**
3. **▶️ Build** — paste the key → pick a video → choose a detail level → press **Generate** → save the HTML

⚠️ **The AI generates it, so it isn't perfect. Always review and edit before relying on it.**
🔑 Your key stays in your browser and is sent only to Google — never to this site's owner.

<details>
<summary><b>More detail (privacy · free/paid tiers · how it works)</b></summary>

<br>

### Privacy & security

- Your API key and uploaded video go **directly from your browser to Google's Gemini API**. **Nothing passes through any server of ours** (GitHub Pages is static hosting).
- The key is stored only in your browser (localStorage); behavior is the same on the hosted and local versions.
- For maximum control, download `index.html` and open it locally — you then run a fixed copy of the code.
- Exported manuals embed screenshots from your source video; be careful sharing ones made from confidential footage.
- Provided **with no warranty** — no guarantee it works or that results are accurate; the author is not liable for damages (legal terms in [LICENSE](LICENSE)).

### Free tier vs. paid

It works on the free tier, but **429 (rate limit) comes from per-minute limits (RPM / TPM), not the daily count (RPD)**. Each call counts the video's frames as input tokens, so raising concurrency on the free tier tends to hit the TPM ceiling. This tool:

- **Defaults to a free-safe mode (concurrency K=2).** It reads `retryDelay` from the 429 body and waits exactly that long before retrying.
- **Paid keys can raise concurrency** — run `window.__mfSetTier('paid')` once in the console (K=4; back to free with `'auto'`).
- After a run, `window.__mfGenTrace()` prints `[quota] tier=… K=… by429=… dim=tokens(TPM)/requests(RPM) …` — the measured breakdown.

### How it works

- **Capture**: `video.currentTime` seeking can take minutes per frame on sparse-keyframe (long-GOP) videos, so it decodes the video once with **WebCodecs** and grabs all frames in one pass (PoC: 12 points from a 736 s video in ~9 s). Falls back to seeking where WebCodecs is unavailable.
- **Pipeline**: structure analysis (skeleton, `temperature=0`) → batched detailing (parallel) → merge & sanitize (timestamps made monotonic and within video length) → screenshot capture. Behavior is logged in `window.__captureDiag`.
- **Environment**: capture depends on WebCodecs, so Chrome/Edge are most reliable (Safari limited); long/high-res videos take more time and memory (8K etc. auto-downscaled).

### Offline / local use

Download `index.html` and double-click it — it works the same way (`file://`).

</details>

## Built with

Developed using [Claude Code](https://claude.com/claude-code) (Anthropic), recorded as co-author on every commit.

## License

[MIT License](LICENSE) © 2026 Yoshiro OHGI
