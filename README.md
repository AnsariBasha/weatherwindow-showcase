# WeatherWindow — Voice Weather & World Clocks

A personal voice-AI prototype for asking about weather and city time through spoken or typed questions.

Built with **Python, FastAPI, Deepgram speech APIs, Gemini function calling, Open-Meteo and browser JavaScript**.

## What it does

- Accepts short push-to-talk recordings or typed questions.
- Answers current-weather, today/tomorrow forecast and current city-time questions.
- Resolves real places and asks for clarification when the location is ambiguous.
- Calculates city time using IANA timezone and daylight-saving rules.
- Displays world clocks, forecast source data and request diagnostics.
- Returns text before speech finishes and plays a progressive MP3 response.
- Reuses completed speech for replay without another synthesis request.

## Architecture

```text
Browser recording
    -> Deepgram speech-to-text
    -> Gemini selects a Python city tool
    -> Open-Meteo geocoding + forecast, or timezone-based city time
    -> Python formats returned facts
    -> Text answer + progressive Deepgram speech playback
```

Typed input skips speech recognition. Time-only questions skip the forecast request. World-clock refreshes run locally in the browser without model calls.

The normal successful lookup uses one Gemini request. Python validates tool arguments, uses coordinates from real geocoding results and formats the returned facts. One additional model-directed refinement is allowed when a place search returns no results.

## Engineering work demonstrated

- Direct REST integrations with speech, language and weather providers.
- Function calling with an allowlisted Python dispatcher and checked location identifiers.
- Async HTTP connection reuse and incremental audio forwarding.
- Short-lived audio tickets, replay caching and range responses.
- Explicit measurements for text readiness, first audio bytes and browser playback events.
- Offline regression tests for weather contracts, ambiguity, local dates, DST, tool execution and audio behaviour.

## Verification and scope

The project has a 22-test offline suite. Earlier live checks exercised real STT, Gemini, geocoding, forecast and progressive TTS using a synthetic recorded question, plus a time-only request.

This is a locally run personal prototype. Input is a completed recording; output streams progressively over HTTP. Continuous hands-free input, automatic interruption handling, production deployment and load testing are future work. Current browser microphone/playback behaviour and layout require manual checks.

## Repository contents

This public repository contains the project overview only. Application source, credentials and local technical documentation are maintained separately and are not included here. It is not a runnable source-code release.

Weather/geocoding data: [Open-Meteo](https://open-meteo.com/) and [GeoNames through Open-Meteo geocoding](https://open-meteo.com/en/docs/geocoding-api). Provider documentation: [Deepgram](https://developers.deepgram.com/) and [Gemini](https://ai.google.dev/).
