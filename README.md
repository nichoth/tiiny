# `tiiny`

Docs and notes about [`tiiny` device](https://tiiny.ai/).

<details><summary><h2>Contents</h2></summary>

<!-- toc -->

- [`tiiny` CLI](#tiiny-cli)
  * [`tiiny top`](#tiiny-top)
- [Use inference](#use-inference)
- [These Docs](#these-docs)
  * [Example](#example)
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

## Use inference

From the models shown by `tiiny top`, choose an Image-Text-to-Text or
Text Generation model, then use its model ID to run inference.

```
tiiny run -m <model_id> -p "Explain what a transformer is in two sentences."
```

## These Docs

See npm script `ll` for formatting markdown help.

### Example

```sh
npm run ll -s -- ./docs/models.md > ./docs/models.new.md
```

This script calls `npx prettier --prose-wrap always --print-width 80`. Output
goes to `stdout`.

## See Also

* [docs/models.md](./docs/models.md) -- which model should I use?




