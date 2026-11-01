# Tech Stack — jarvis-presentes

Constraint: **web-based**, runs on **both Windows and macOS**, no platform-specific install. The deck is rendered in the browser; a small local server does conversion and directs the show.

## Core stack

| Layer | Pick | Why |
|---|---|---|
| **Frontend** | React + Vite (+ Tailwind) | Fast, standard; overlay HUD (chat, gauges, waveform, glitch effects) is just components |
| **Slide rendering** | LibreOffice headless (`soffice --convert-to pdf`) → PDF.js or PNGs in browser | Cross-platform, no Microsoft Office needed on the server; deck shows as native-feeling slides |
| **Deck text extraction** | [python-pptx](https://github.com/scanny/python-pptx) and/or [markitdown](https://github.com/microsoft/markitdown) | Pull slide titles/text/counts → injected into scripted lines so Jarvis quotes the *real* deck |
| **Director (the brain)** | Own scripted state engine — scene files as JSON/YAML (beats, delays, response branches) | Deterministic theater: no LLM, reliable timing, fully reviewable before a live show |
| **Realtime events** | WebSocket ([ws](https://github.com/websockets/ws) or socket.io) | Director → overlay: text beats, slide jumps, gauge values, TTS triggers |
| **Voice out** | Web Speech API (free, local, both platforms); character voice via [Kokoro TTS](https://github.com/nazdridoy/kokoro-tts) or [ElevenLabs SDK](https://github.com/elevenlabs/elevenlabs-python) | Start free/local, upgrade for a proper Jarvis voice later |
| **Voice in (interactive intro)** | Web Speech Recognition (Chrome) or a small [Whisper](https://github.com/openai/whisper) endpoint | Keyword triggers map to scripted response branches |
| **Server** | Node/TypeScript (Fastify) + Python sidecar for pptx parsing; or all-Python (FastAPI) | Local process, Docker optional |

## Optional "pro mode" (native, not required)

- [ppt-mcp](https://github.com/ykuwai/ppt-mcp) — real-time control of a *live* PowerPoint (Windows via COM, macOS via Apple Events), 156 tools. If the joke ever needs to hijack the *actual* PowerPoint window instead of the browser render.
- [obs-websocket](https://github.com/obsproject/obs-websocket) ([py client](https://github.com/Elektordi/obs-websocket-py)) — if staging the takeover as a screen-shared/streamed event.
- [Presenton](https://github.com/presenton/presenton) (11.6k★) — open-source AI presentation agent; study it for the clichés the parody should hit.
- [PptxGenJS](https://github.com/gitbrent/PptxGenJS) — generate demo/test decks programmatically.

## Scene file format (draft)

```yaml
scene: takeover
beats:
  - type: speak
    text: "Deck detected. {slide_count} slides. {word_count} words."
    tts: true
  - type: hud
    gauge: boredom_index
    value: 61
  - type: slide
    action: jump
    target: "{most_words_slide}"
  - type: wait
    ms: 1200
branches:
  audience_interrupt:
    trigger: voice("boring")
    goto: scene:snarky_response
```

Placeholders like `{slide_count}` are filled from the real deck at load time — that's the whole illusion.

## Milestones

1. **M1 — Pipeline**: upload pptx → server → slides render in browser; extract text/stats.
2. **M2 — Director**: scene engine with beats, waits, slide jumps; presenter control tab.
3. **M3 — HUD**: Jarvis overlay (chat stream, gauges, boot log, glitch FX).
4. **M4 — Voice**: TTS for Jarvis lines + voice-triggered interruptions.
5. **M5 — Interactive branches**: audience interruption branches, handback sequence, kill switch.

## Open questions

- Voice: Web Speech (robotic, on-brand) vs. character TTS (Kokoro/ElevenLabs)?
- Language of the scripted lines: German, English, or both (scene files per locale)?
- Audience interaction channel: shared screen buttons only, or voice?
- Fully local (offline) requirement — or is a tiny cloud TTS acceptable?
