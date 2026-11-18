# `tiiny`

Docs and notes about [`tiiny` device](https://tiiny.ai/).

<details><summary><h2>Contents</h2></summary>

<!-- toc -->

- [`tiiny` CLI](#tiiny-cli)
  * [`tiiny top`](#tiiny-top)
  * [Use inference](#use-inference)
  * [Download a new model](#download-a-new-model)
- [Tiiny + Pi](#tiiny--pi)
  * [Add Tiiny Models to Pi](#add-tiiny-models-to-pi)
    + [Example `models.json`](#example-modelsjson)
- [Models](#models)
  * [`Qwen/Qwen3.5-9B-Turbo`](#qwenqwen35-9b-turbo)
  * [`Qwen/Qwen3.6-35B-A3B`](#qwenqwen36-35b-a3b)
  * ["Thinking"](#thinking)
    + [Fine without thinking](#fine-without-thinking)
    + [Better with thinking](#better-with-thinking)
  * [`Qwen/Qwen3.6-35B-A3B-turbo`](#qwenqwen36-35b-a3b-turbo)
    + [Turbo Speed](#turbo-speed)
    + [Non-turbo Speed](#non-turbo-speed)
- [These Docs](#these-docs)
  * [Line Length Example](#line-length-example)
- [Notes](#notes)
  * [Check the context window](#check-the-context-window)
- [See Also](#see-also)

<!-- tocstop -->

</details>

## `tiiny` CLI

* [docs for the `tiiny` CLI tool](https://dev.tiiny.ai/#/quickstart)

### `tiiny top`

View the currently loaded models and their NPU usage.

```sh
tiiny top
```

### Use inference

From the models shown by `tiiny top`, choose an Image-Text-to-Text or
Text Generation model, then use its model ID to run inference.

```sh
tiiny run -m <model_id> -p "Explain what a transformer is in two sentences."
```

### Download a new model

```sh
tiiny download Qwen/Qwen3-Coder-Next
```

---

## Tiiny + Pi

Use `tiiny` with `pi` harness.

### Add Tiiny Models to Pi

`pi` needs to know that the `tiiny` models exist. Add entries to a file,
`~/.pi/agent/models.json`. Create the file if it does not exist.


#### Example `models.json`

```json
{
  "providers": {
    "tiiny": {
      "baseUrl": "http://openai.api.tiiny/v1",
      "api": "openai-completions",
      "apiKey": "$TIINY_API_KEY",
      "compat": {
        "supportsDeveloperRole": false,
        "supportsReasoningEffort": false,
        "supportsUsageInStreaming": false,
        "maxTokensField": "max_tokens"
      },
      "models": [
        {
          "id": "Qwen/Qwen3.6-35B-A3B",
          "name": "Tiiny Qwen 3.6 35B A3B",
          "contextWindow": 131072,
          "maxTokens": 16384
        },
        {
          "id": "Qwen/Qwen3.6-35B-A3B-turbo",
          "name": "Tiiny Qwen 3.6 Turbo",
          "contextWindow": 262144,
          "maxTokens": 16384
        },
        {
          "id": "Qwen/Qwen3.5-9B-Turbo",
          "name": "Tiiny Qwen 3.5 9B Turbo",
          "contextWindow": 131072,
          "maxTokens": 16384
        }
      ]
    }
  }
}
```

## Models

### `Qwen/Qwen3.5-9B-Turbo`

```sh
curl -s http://openai.api.tiiny/v1/chat/completions \
  -H "Authorization: Bearer $TIINY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"Qwen/Qwen3.5-9B-Turbo","messages":[{"role":"user","content":"Write a TypeScript function that debounces another function."}]}' \
  | jq '{finish_reason: .choices[0].finish_reason, completion_tokens: .usage.completion_tokens, tokens_per_sec: .timings.predicted_per_second}'
```

```json
{
  "finish_reason": "stop",
  "completion_tokens": 1079,
  "tokens_per_sec": 35.4586239139604
}
```

### `Qwen/Qwen3.6-35B-A3B`

With "thinking":

```sh
curl -s http://openai.api.tiiny/v1/chat/completions \
  -H "Authorization: Bearer $TIINY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"Qwen/Qwen3.6-35B-A3B","messages":[{"role":"user","content":"Write a TypeScript function that debounces another function."}]}' \
  | jq '{finish_reason: .choices[0].finish_reason, completion_tokens: .usage.completion_tokens, tokens_per_sec: .timings.predicted_per_second}'
``` 

```json
{
  "finish_reason": "stop",
  "completion_tokens": 7655,
  "tokens_per_sec": 24.321334758409986
}
```

---

With "thinking" turned off:

```sh
% curl -s http://openai.api.tiiny/v1/chat/completions \
  -H "Authorization: Bearer $TIINY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"Qwen/Qwen3.6-35B-A3B","chat_template_kwargs":{"enable_thinking":false},"messages":[{"role":"user","content":"Write a TypeScript function that debounces another function."}]}' \
  | jq '{finish_reason: .choices[0].finish_reason, completion_tokens: .usage.completion_tokens, tokens_per_sec: .timings.predicted_per_second}'
{
  "finish_reason": "stop",
  "completion_tokens": 117,
  "tokens_per_sec": 26.019022796666896
}
```

### "Thinking"

Qwen models are built to work with or without "thinking", and with thinking off
they behave like a normal instruct model. The answer went from
about 5 minutes to under 5 seconds.

#### Fine without thinking

* Chatting and explaining things
* Writing small, well-defined functions like the debounce
* Refactors and simple edits
* Summarizing, and answering questions about code you paste in

#### Better with thinking

* Multi-step logic and tricky bugs
* Math
* Planning a change that touches several files

---

### `Qwen/Qwen3.6-35B-A3B-turbo`

__Turbo speed vs non-turbo speed__

Turbo is about 40% faster: 36 tokens per second vs 26 for the regular model.

#### Turbo Speed

```sh
curl -s http://openai.api.tiiny/v1/chat/completions \
  -H "Authorization: Bearer $TIINY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"Qwen/Qwen3.6-35B-A3B-turbo","chat_template_kwargs":{"enable_thinking":false},"messages":[{"role":"user","content":"Write a TypeScript function that debounces another function."}]}' \
  | jq -c '{tokens: .usage.completion_tokens, tok_per_sec: .timings.predicted_per_second}'
```

```js
{ "tokens": 122, "tok_per_sec": 36.259809627009425 }
```

#### Non-turbo Speed

```sh
curl -s http://openai.api.tiiny/v1/chat/completions \
  -H "Authorization: Bearer $TIINY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"Qwen/Qwen3.6-35B-A3B","chat_template_kwargs":{"enable_thinking":false},"messages":[{"role":"user","content":"Write a TypeScript function that debounces another function."}]}' \ 
  | jq -c '{tokens: .usage.completion_tokens, tok_per_sec: .timings.predicted_per_second}'
```

```js
{ "tokens": 117, "tok_per_sec": 26.47888535208431 }
```

---

## These Docs

Use npm script `lines` for formatting markdown.

### Line Length Example

```sh
npm run lines -s -- ./docs/models.md > ./docs/models.new.md
```

This script calls `npx prettier --prose-wrap always --print-width 80`. Output
goes to `stdout`.

## Notes

### Check the context window

```sh
curl -s http://openai.api.tiiny/v1/models -H "Authorization: Bearer $TIINY_API_KEY" \
  | jq '.data[] | select(.id=="Qwen/Qwen3.6-35B-A3B-turbo") | .context_window'
```

## See Also

* [docs/models.md](./docs/models.md) -- which model should I use?




