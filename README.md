# VoiceMatch

Speak (or type) anything; the perfect meme sound effect plays back.

A single-page app that uses the Web Speech API to listen continuously, classifies what you said, and plays a matching sound from [soundbuttonsworld.com](https://soundbuttonsworld.com). The orb keeps listening until you tap it again.

## How it works

1. **Tap the orb** — it stays in listening mode (red) until you tap to stop.
2. **Speak a sentence** — 500 ms after the recognizer finalizes the sentence, the matched sound plays (capped at 4 s).
3. **Or type** — same matcher runs over the text input.

The matcher tries, in order:
- **Laugh detection** — laughter patterns (`haha`, `lol`, `lmao`, …) always fire the laugh sound.
- **Keyword/phrase scoring** — every sound carries keywords + phrases pulled from [`sounds.md`](sounds.md). Direct sound-name mention = 12 pts, phrase = 5 pts, keyword = 1 pt. Stem matching (`crash*` matches *crash/crashed/crashing*).
- **Favorites boost** — 12 hand-picked favorites get +3 to score on any organic hit.
- **Sentiment fallback** — if no keyword scores, route by emotional tone (happy → Ahhhhh, sad → Sad Violin, confused → Wait What, etc.).
- **Trolling random** — last resort, with a witty 1-sentence roast.
- **AI tier** (optional) — calls the Anthropic API with the trigger map; falls back to the layers above if blocked by CORS.

## Run locally

The Web Speech API needs `localhost` or HTTPS — `file://` won't work.

```bash
cd voicematch
python3 -m http.server 8000
```

Open http://localhost:8000 in Chrome, Edge, or Safari and grant mic access on first tap.

## File map

- [`index.html`](index.html) — the whole app (HTML + CSS + JS in one file)
- [`sounds.md`](sounds.md) — sound trigger map (priority, conditions, example phrases)

## Notes

- AI matching uses Claude. Direct browser → `api.anthropic.com` calls are blocked by CORS, so the fallback layers do the actual work today. Add a tiny proxy if you want the AI tier to fire.
- Audio plays cross-origin from `https://soundbuttonsworld.com/sounds/{uuid}.mp3` — works without CORS headers because `<audio>` element playback doesn't require them.
