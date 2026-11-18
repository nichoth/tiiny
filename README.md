# `tiiny`

Docs and notes about [`tiiny` device](https://tiiny.ai/).

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

```
tiiny run -m <model_id> -p "Explain what a transformer is in two sentences."
```








