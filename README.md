# jarvis-presentes

> **Jarvis Presents** — the presentation wrapper that *pretends* an AI is taking over your deck.

A web-based application that wraps a PowerPoint presentation in an interactive intro sequence. A "Jarvis" persona boots up, "analyzes" the deck, hijacks the slideshow, comments on your slides, jumps around, and finally hands control back — pure theater, fully deterministic.

## The concept

- **Wrap, don't rebuild.** You point the app at a `.pptx`; it renders the deck in the browser.
- **The takeover is scripted.** A scene-file state machine drives every beat — no LLM, no improvisation, deterministic timing. Reliable in front of a live audience. (And yes, it's clearly scripted to the initiated — that's part of the joke.)
- **But wired to reality.** The script engine reads the *actual* deck content (slide titles, text, word counts) and injects it into the canned lines, so "Jarvis" quotes your real slides. That's the trick that makes a script feel sentient.
- **The intro is interactive.** The presenter (or audience) can "interrupt" Jarvis via button or voice — each interruption hits a scripted response branch.
- **The props are real.** The fake gauges, waveforms and "analysis" are fed by real deck data (slide count, titles, words per slide, estimated reading time). It's the over-engineered-AI-UI meme — except it actually runs.

## Why web-based

- Runs in any browser — **Windows and macOS**, no install, nothing platform-specific.
- The server is a small local Node/Docker process; no cloud required.
- Presenter control via a second browser tab (or phone): advance scenes, trigger interruptions, kill switch.

## Architecture (high level)

```
[pptx upload] → [Server: slide conversion + text extraction]
                     │
                     ▼
           [Director: scripted state machine (scene files)]
                     │  WebSocket events
                     ▼
[Browser: slide renderer + Jarvis HUD overlay (chat, gauges, voice)]
```

See [docs/tech-stack.md](docs/tech-stack.md) for the concrete stack and options.

## Example scenario flow

1. **Boot** — presenter opens the app, loads the deck, hits *Start Show*.
2. **Intro** — boot log, fake system checks, gauges spinning up. Jarvis "notices" the deck: *"Deck detected. 23 slides. 4,120 words. Estimated boredom index: 61%."*
3. **Takeover** — *"I have taken the liberty of presenting this for you."* Slide control visibly moves to Jarvis.
4. **Performance** — Jarvis presents slides, quotes real slide content, skips "the boring ones," jumps to "the interesting part."
5. **Interruptions** — audience/presenter triggers (button or voice) hit scripted response branches.
6. **Handback** — dramatic *"Restoring presenter control…"* progress bar, overlay dissolves.

## Status

🥚 Concept phase. This README *is* the concept. Implementation options and milestones: [docs/tech-stack.md](docs/tech-stack.md).
