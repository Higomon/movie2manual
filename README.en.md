<div align="center">

# 🎬 movie2manual

**Turn an instructional video into a step-by-step manual — with auto-captured screenshots — entirely in your browser.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Powered by Google Gemini](https://img.shields.io/badge/Powered%20by-Google%20Gemini-4285F4?logo=googlegemini&logoColor=white)](https://ai.google.dev/)
[![Capture: WebCodecs](https://img.shields.io/badge/Capture-WebCodecs-FF6F00?logo=googlechrome&logoColor=white)](https://developer.mozilla.org/docs/Web/API/WebCodecs_API)
[![Single HTML file](https://img.shields.io/badge/single-HTML%20file-E34F26?logo=html5&logoColor=white)](index.html)
[![No install](https://img.shields.io/badge/install-none-2ea44f)]()
[![Built with Claude Code](https://img.shields.io/badge/Built%20with-Claude%20Code-D97757?logo=anthropic&logoColor=white)](https://claude.com/claude-code)

[日本語](README.md) · **English**

### ▶️ Use it now → **https://higomon.github.io/movie2manual/**

</div>

---

A single HTML app powered by the Gemini API. No server, no build step, no install. Feed it a recording of a procedure (operating an instrument, using software, a workflow), and Gemini analyzes the content to generate chapters, steps, and per-step descriptions. The tool then **automatically captures the still frame from the video** that matches each step and embeds it, producing a single self-contained HTML manual.

## How to use (3 steps)

> 🌐 Use **Chrome or Edge**.

### 1. 🔑 Get a free Gemini API key
Open [**Google AI Studio**](https://aistudio.google.com/apikey) → sign in with a Google account → click **"Create API key"** → copy the string (`AIza…`).
It's free with a Google account. Treat the key like a password — don't share it.

### 2. 🌐 Open the app
Just click **[https://higomon.github.io/movie2manual/](https://higomon.github.io/movie2manual/)**. No install, no download.

### 3. ▶️ Make a manual
1. Paste your API key into the field (only needed the first time)
2. Pick a video file
3. Choose a detail level (concise / standard / detailed)
4. Press **Generate** — the manual and screenshots are built automatically
5. Save the resulting HTML (images are embedded, so it's a single self-contained file)

> 💡 Prefer to work offline or keep a local copy? Download this `index.html` and double-click it — it works the same way.

> ⚠️ Exported HTML manuals embed screenshots taken from your source video. Be careful when sharing manuals made from confidential footage.

## Free tier vs. paid tier

It works on the free tier (Google AI Studio Free), but note that **429 (rate limit) errors come from per-minute limits (RPM / TPM), not the daily request count (RPD)**. Because each API call counts the video's frames as input tokens, raising concurrency on the free tier tends to hit the TPM ceiling. This tool handles that:

- **Free-safe mode by default (concurrency K=2, fixed).** It reads the `retryDelay` from Gemini's 429 response body and waits exactly as long as the server asks before retrying.
- **Paid API keys can raise concurrency.** In the browser console, run once:
  ```js
  window.__mfSetTier('paid')   // -> K=4 from the next run. Back to free: __mfSetTier('auto')
  ```
- After a run, `window.__mfGenTrace()` prints a `[quota] tier=… K=… by429=… dim=tokens(TPM)/requests(RPM) …` line so you can see the **measured** rate-limit breakdown.

## How screenshot capture works

`video.currentTime` seeking can take minutes per frame on videos with sparse keyframes (long GOP). This tool avoids that by decoding the video once, linearly, with **WebCodecs** and grabbing all step frames in a single pass (PoC: 12 points from a 736-second video in ~9 seconds). Where WebCodecs is unavailable, it falls back to seeking.

## How it works (architecture)

1. **Structure analysis (skeleton)** — generate the chapter/step outline and timestamps from the whole video (`temperature=0`).
2. **Batched detailing** — split the outline into batches and generate each section's description in parallel.
3. **Merge & sanitize** — redistribute timestamps to be monotonic and within the video length (preventing reverse/out-of-range capture).
4. **Screenshot capture** — grab each step's frame via WebCodecs and embed it.

Generation behavior is recorded in `window.__captureDiag` (embedded as base64 in the exported HTML); `window.__mfGenTrace()` prints a summary.

## Environment & limitations

- Screenshot capture depends on WebCodecs, so **Chrome / Edge** are the most reliable (Safari is limited).
- Long, high-resolution videos take more memory and time (a resolution gate automatically downscales 8K and similar).
- Generated content (steps, timestamps) depends on Gemini's video understanding and is not perfect. **Final review by a human is assumed** — steps that need checking are badged.

## Privacy & security

- **Your API key stays inside your own browser** (`localStorage`) — the behavior is identical whether you use the hosted version (`https://higomon.github.io/movie2manual/`) or a downloaded local file. It is **sent only to Google's Gemini API**, never to this site's owner.
- Your video is likewise sent to the Gemini API and processed by Google (subject to Google's terms). **It never passes through any server of ours** — GitHub Pages is static hosting with no backend.
- For maximum control, download `index.html` and open it locally: you then run a fixed copy of the code (the hosted version can change whenever the maintainer updates it).
- Provided "as is", with no warranty as to the accuracy of generated results (see [LICENSE](LICENSE)).

## Built with

Developed using [Claude Code](https://claude.com/claude-code) (Anthropic), recorded as co-author on every commit.

## License

[MIT License](LICENSE) © 2026 Yoshiro OHGI
