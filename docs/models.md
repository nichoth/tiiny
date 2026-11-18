# Tiiny Models for Dev Work: Speed vs Quality

Nov 17, 2026 · @nichoth

Start with Qwen3.6-35B-A3B. On Tiiny, speed depends mostly on
active parameters per token, not the model's headline size. Most
of the good options are mixture-of-experts (MoE) models that use
about 3B parameters per token, so they're fast; dense models like
Qwen3.6-27B are slow. ASR, TTS, image and music models are left
out.

## Main coding options

| Model | Active params | Speed | Quality for dev work |
| --- | --- | --- | --- |
| Qwen3.6-35B-A3B / -turbo | \~3B (MoE) | Fast | Best balance. Newest generation; strong at agentic coding |
| Qwen3.6-27B | 27B (dense) | Slow (roughly 5–9× slower per token, going by active params) | Probably the highest on the list, but you wait for it |
| Qwen3-Coder-Next | \~3B of 80B (MoE) | Fast, holds up at long context | Coding specialist, near the top |
| gpt-oss-120b | \~5B of 120B (MoE) | Medium (\~19 tok/s on Tiiny); slows down a lot at long context | Strong reasoning and general knowledge; behind Qwen3.6 on agentic coding |
| GLM-4.7-Flash | \~3B (MoE) | Fast | Solid, a step below Qwen3.6 |
| Qwen3-Coder-30B-A3B-Instruct / -Turbo | \~3B (MoE) | Very fast, no thinking mode | Older (2025) but good for autocomplete and quick edits |
| gemma-4-26B-A4B | \~4B (MoE) | Fast | Weaker at coding than Qwen3.6 |

Speed figures are measured or reported. Quality rankings rest
mostly on vendor benchmarks.

## Notes

**Qwen3.6-35B-A3B is the default.** It activates 3B of its 35B
parameters per token. Alibaba reports 73.4 vs 52.0 against Gemma
4-31B on SWE-bench Verified, and 51.5 vs 42.9 on Terminal-Bench
2.0. It has thinking and non-thinking modes, so reasoning can be
turned off for quick tasks. It also takes images, which helps with
screenshots of UI bugs.

**The "-turbo" variants are unconfirmed.** My best guess is
speculative decoding with a small draft model. That would explain
the turbo row's higher NPU usage (55% vs 47%). Try both and keep
whichever feels faster.

**For long-context agent loops, Coder-Next beats gpt-oss-120b.**
In community benchmarks on an M2 Max, gpt-oss-120b fell from 54.9
tok/s at 1k context to 14.6 at 32k. Over the same range,
Qwen3-Coder-Next went from 53.4 to 25.5. On the Tiiny itself, a
reviewer measured gpt-oss-120b at 18.86 tok/s, before any
long-context slowdown.

**The NPU Usage column matters.** It reads as roughly the model's
share of device memory. gpt-oss-120b (99%) and Coder-Next (94%)
take over the whole device, leaving no room for an embedding model
or a second LLM. Qwen3.6-35B (47%) leaves room for both.

## Models you can mostly skip

- **Qwen3-30B-A3B-Thinking/Instruct, Qwen3.5-35B-A3B(-Turbo):**
  older versions that Qwen3.6 replaces; Qwen3.6 beats Qwen3.5-35B-A3B
  by a wide margin on nearly every benchmark.
- **Qwen3.5-9B, Qwen3-8B, gpt-oss-20b:** fine for commit messages,
  quick summaries or a cheap sub-agent, not for real coding.
- **Ornith-1.0-35B:** unknown model; open question where it places.

## Embedding and reranker models

The embedding models (Nemotron-3-Embed, R3-Embedding, Qwen3-Embedding)
and rerankers (R3, Qwen3) power semantic search over a codebase
(RAG). The small ones use about 1–2% NPU, so one can stay loaded
beside Qwen3.6.

## Suggested setup

| Use | Model |
| --- | --- |
| Daily driver / agent | Qwen3.6-35B-A3B-turbo |
| Hard problem, willing to wait | Qwen3.6-27B |
| Big-repo agent sessions | Qwen3-Coder-Next (uses the whole device) |
| Editor autocomplete | Qwen3-Coder-30B-A3B-Instruct-Turbo |

Try Qwen3.6-35B and Coder-Next on one of your own repos before
settling.

## Sources

- [The Decoder – Qwen3.6 leads Gemma 4 on agentic coding](https://the-decoder.com/alibabas-open-model-qwen3-6-leads-googles-gemma-4-across-agentic-coding-benchmarks/)
- [Labellerr – Qwen3.6-35B-A3B overview](https://www.labellerr.com/blog/qwen3-6-35b-a3b-open-source-ai-model/amp/)
- [oMLX community benchmarks](https://omlx.ai/benchmarks?page=9025)
- [Tiiny Pocket Lab review notes](https://digifella.github.io/ai-wiki/lab-notes/2026-05-26-Tiiny-AI-Pocket-Lab-Running-Large-Language-Models-Locall)
