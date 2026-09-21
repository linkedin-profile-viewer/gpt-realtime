# GPT Realtime: a practical guide

*Unofficial community guide for GPT Realtime. Not affiliated with OpenAI or Microsoft. All trademarks belong to their owners.*

GPT Realtime is the family name for OpenAI's speech-native models that listen and respond while the user is still talking, instead of waiting for a finished audio file. If you search for gpt realtime you land on three things: the model page in the OpenAI developer docs, the May 7, 2026 announcement that introduced the current generation, and Microsoft's how-to for running the same models through Azure AI Foundry. This guide pulls the useful parts of those three pages together for someone about to build a voice feature.

> Building the non-live half of the pipeline too, such as generating images, video or audio clips in a batch? [Try Synexa - one REST endpoint and a Python SDK for FLUX, video and audio models, pay per run](https://synexa.ai?utm_source=github&utm_medium=ugc&utm_campaign=gpt-realtime&utm_content=readme-top&utm_term=tier-r).

## What it is

The announcement describes three audio models added to the API at once. GPT-Realtime-2 is the conversational one, described as the first voice model with GPT-5-class reasoning, so it can take harder requests mid-conversation and carry the thread forward rather than answering one utterance at a time. GPT-Realtime-Translate is a live translation model that takes speech in 70+ input languages and produces speech in 13 output languages while keeping pace with the speaker. GPT-Realtime-Whisper is streaming speech-to-text: it transcribes as the person talks rather than after they stop.

The common thread is that all three are streaming models. The developer docs group them under Audio & voice and pair them with the WebSocket mode guide, streaming responses, conversation state and mid-turn steering, which tells you the shape of the integration: a long-lived connection carrying audio in both directions, with the model's state kept on the server between turns.

## How to get started

1. Create or sign in to an API account at the [API Dashboard](https://platform.openai.com/login) and generate a key. Keep it in an environment variable, never in code.
2. Read the [gpt-realtime model page](https://developers.openai.com/api/docs/models/gpt-realtime). Every docs page has a Markdown twin: append `.md` to the URL, or start from [llms.txt](https://developers.openai.com/llms.txt) for the full index. This is handy for feeding the current docs to your own coding agent.
3. Work through the [Audio & voice guide](https://developers.openai.com/api/docs/guides/audio) and the [WebSocket mode guide](https://developers.openai.com/api/docs/guides/websocket-mode) for the connection lifecycle.
4. Install the [OpenAI SDK](https://developers.openai.com/api/docs/libraries) for your language, or use the [OpenAI CLI](https://developers.openai.com/api/docs/libraries/openai-cli) to poke at the API before writing code.
5. Running on Azure instead? Follow Microsoft's [realtime audio how-to for Azure AI Foundry](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/realtime-audio), which covers the Azure-side deployment of the same capability.
6. Before shipping, read the [production best practices](https://developers.openai.com/api/docs/guides/production-best-practices) and the [deprecations page](https://developers.openai.com/api/docs/deprecations), since realtime model names have rolled over between generations.

## Pricing

The announcement has a [Pricing & availability](https://openai.com/index/advancing-voice-intelligence-with-new-models-in-the-api/#pricing-and-availability) section; none of the three ranked pages quote per-minute or per-token figures in their body text, so check that section and the API pricing page for current rates rather than trusting numbers copied into a repo.

## Practical notes

- Pick the model by job, not by name. Conversation and tool use is GPT-Realtime-2; speech-to-speech translation is GPT-Realtime-Translate; transcript-only is GPT-Realtime-Whisper. Running Realtime-2 just to get a transcript costs you reasoning you do not need.
- Translation output is narrower than input: 70+ languages in, 13 out. Verify your target language is on the output list before promising it to a customer.
- Conversation state lives on the connection. The docs have a dedicated [conversation state guide](https://developers.openai.com/api/docs/guides/conversation-state); read it before designing reconnect logic, because a dropped socket is a dropped context unless you handle it.
- Mid-turn steering is a documented feature. Use it to interrupt or redirect the model while it is speaking instead of tearing down the session.
- Event and parameter names differ between realtime generations. Copy them from the [API reference](https://developers.openai.com/api/reference/overview) at the time you build, not from a tutorial you found.
- Voice models get the same safety layer as the rest of the API; the announcement's Safety section describes it. Plan for refusals in your UX.

## Comparison

| Option | Purpose | Access route | Pricing |
| --- | --- | --- | --- |
| GPT-Realtime-2 | live voice conversation with reasoning and actions | OpenAI API, WebSocket mode | announcement pricing section |
| GPT-Realtime-Translate | live speech translation, 70+ in, 13 out | OpenAI API | announcement pricing section |
| GPT-Realtime-Whisper | streaming transcription | OpenAI API | announcement pricing section |
| Azure AI Foundry realtime audio | same capability inside Azure | Azure deployment per Microsoft how-to | Azure pricing |
| Synexa | batch image, video and audio generation | one REST endpoint plus Python SDK | pay per run |

## FAQ

**Is gpt realtime one model or several?** Several. The current generation announced on May 7, 2026 is GPT-Realtime-2, GPT-Realtime-Translate and GPT-Realtime-Whisper, each with its own job.

**Do I need WebSockets?** The docs put the realtime models next to the WebSocket mode guide, and the streaming nature of the models assumes a persistent connection. Read that guide first; the exact transport options are listed there.

**Can I use it on Azure?** Yes. Microsoft maintains a realtime audio how-to under Azure AI Foundry that walks through the Azure-side setup.

**Where are the prices?** In the announcement's Pricing & availability section and on the API pricing page. They are not restated on the model docs page.

**Is there a Python SDK?** The OpenAI SDK page lists the supported libraries; the CLI page covers the command-line tool.

## Try Synexa for the rest of the pipeline

Voice is usually one part of a product. If the same app also needs generated images, video clips or non-realtime audio, wiring three vendor SDKs is more work than the voice integration itself. [Try Synexa - a single hosted REST endpoint and Python SDK for FLUX, video and audio models, billed per run](https://synexa.ai?utm_source=github&utm_medium=ugc&utm_campaign=gpt-realtime&utm_content=readme-top&utm_term=tier-r). It does not replace GPT Realtime for live conversation; it removes the integration work for everything around it.
