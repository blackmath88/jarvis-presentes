# Handoff Prompt — Sparring Partner: Architecture Check + Build

> Paste this into a fresh chat with your strongest model (e.g. Mistral Large 4) or a coding agent (Vibe Code / Claude Code / Cursor). It is self-contained.

---

Act as my **sparring partner and senior architect** on this project, then build it. Repo: https://github.com/blackmath88/jarvis-presentes (read README.md and docs/tech-stack.md first if you can clone/fetch; everything you need is summarized below).

## The project

**jarvis-presentes** — a web-based app that wraps a PowerPoint presentation in an interactive intro where a "Jarvis" persona *pretends* to hijack the deck. It analyzes the deck (fake gauges, boot log), takes over slide control, comments on slides, reacts to interruptions with scripted branches, and hands control back. Pure theater, fully deterministic.

The core illusion: every line is **scripted** (scene files, state machine, no LLM in v1) — but the scripted lines are filled with **real data extracted from the actual uploaded .pptx** (slide count, titles, word counts, "most text-heavy slide", estimated reading time). The audience thinks it's sentient AI; it's a YAML file with placeholders.

## Constraints (non-negotiable)

- **Web-based**: runs in the browser on Windows and macOS; no platform-specific install.
- **Local**: small local server (Node or Python), no cloud required, must work offline on stage.
- **Deterministic director**: scripted beats with fixed timing; reliable in front of a live audience; kill switch at all times.
- v1 scope: upload pptx → intro/takeover sequence → present slides with overlay → handback.

## Planned stack (challenge this)

| Layer | Pick |
|---|---|
| Frontend | React + Vite + Tailwind (HUD overlay: chat stream, gauges, boot log, glitch FX) |
| Slide rendering | LibreOffice headless pptx→PDF/PNG, rendered via PDF.js or <img> |
| Text extraction | python-pptx and/or markitdown → slide titles, text, word counts |
| Director | own scene engine, YAML scenes: beats (speak/hud/slide/wait) + branches |
| Realtime | WebSocket (director → overlay + slide renderer) |
| Voice v1 | Web Speech API TTS (robotic = on-brand); optional upgrade: Kokoro/ElevenLabs |
| Voice input | presenter buttons v1; Web Speech Recognition later |

Decided defaults (unless you argue me out of them): English lines first (locale-switchable), presenter-triggered interruptions via a control tab (phone-friendly), Web Speech TTS, no auth, single-user tool.

## Phase 1 — Architecture check (do this first, before any code)

Be a critical sparring partner. Specifically stress-test:

1. **Slide rendering fidelity**: LibreOffice conversion loses fonts/animations/SmartArt. Acceptable for the joke? Alternatives (PDF via Office export, PPTXjs, screenshots)? Recommend one.
2. **Two-process problem**: Node server + Python sidecar for pptx parsing vs. all-Python (FastAPI) vs. all-Node (JS pptx parsers). What's the leanest?
3. **Scene engine**: is YAML scenes + placeholder injection ({slide_count}, {most_words_slide_title}) the right abstraction, or overkill for v1? Propose the minimal scene schema.
4. **Realtime**: is a WebSocket needed, or are state polling / simple events enough?
5. **Presenter control**: control tab vs. keyboard shortcuts vs. both? How to keep the audience view and presenter view in sync?
6. **Failure modes**: conversion errors, weird decks (no text, 200 slides, images only), TTS unavailable in browser — what must degrade gracefully before a live show?

Deliverable: a short verdict table (keep / change / why) + your recommended final architecture in one diagram.

## Phase 2 — Build

After the check, build in this order (M1 first, each milestone runnable):

- **M1 — Pipeline**: upload .pptx → server converts slides to images/PDF → renders in browser; extract and show real deck stats (slide count, words, longest slide).
- **M2 — Director**: scene engine; presenter control tab (start/next/kill); slide jumps.
- **M3 — HUD**: Jarvis overlay — boot log, chat stream, gauges, handback sequence.
- **M4 — Voice**: Web Speech TTS on the scripted lines.
- **M5 — Interruptions**: scripted response branches via control buttons.

Write real, runnable code. Repo layout: /web (frontend), /server, /scenes, /docs. Include README setup instructions (npm/docker, LibreOffice dependency) and a demo scene file that works with any uploaded deck.

Start with Phase 1. Be direct — if something in this plan is silly, say so.
