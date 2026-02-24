# Ollama + Caido Shift Setup

## Prerequisites

- [Ollama](https://ollama.ai) installed and running
- [Caido](https://caido.io) installed

## Step 1: Create a Modelfile

Create a file called `Modelfile` with the following sections:

- `FROM` — base model (must be on the same line as FROM)
- `TEMPLATE` — chat template with tool support (see below)
- `SYSTEM` — system prompt
- `PARAMETER` — model parameters (temperature, num_ctx, stop tokens)

**Important:** The TEMPLATE must include tool formatting (`{{- if .Tools }}` and `{{- if .ToolCalls }}` blocks). Without this, Ollama will reject tool calls from Shift with a "does not support tools" error.

The attached `Modelfile` is ready to use. Update the `FROM` line to your preferred model and quant.

## Step 2: Build the model in Ollama

```bash
ollama create caido_agent -f ~/Applications/Ollama_model/Modelfile
```

Verify it exists:

```bash
ollama list
```

## Step 3: Install Shift plugin

1. Open Caido
2. Go to **Dashboard > Plugins > Official**
3. Install **Shift**
4. Restart Caido if prompted

## Step 4: Configure OpenAI provider

1. Go to **Settings > AI**
2. Under **OpenAI**, fill in:
   - **Base URL**: `http://localhost:11434/v1`
   - **API Key**: any value (required field but Ollama ignores it, enter `ollama` or `dummy`)

This points Caido to Ollama's OpenAI-compatible endpoint. You are not connecting to OpenAI — just using the same API format.

## Step 5: Add custom model in Shift

1. Go to **Dashboard > Plugins > Shift > Models**
2. Click **Add Custom Model**
3. Fill in:
   - **Provider**: OpenAI
   - **Model Name**: display name of your choice (e.g. `Caido Agent`)
   - **Model ID**: must match exactly what `ollama list` shows (e.g. `caido_agent:latest`)
   - **Reasoning/Thinking**: off

## Troubleshooting

### "does not support tools"

The Modelfile template is missing tool formatting. The TEMPLATE must include `{{- if .Tools }}` and `{{- if .ToolCalls }}` blocks. See the included Modelfile for a working example.

### "does not support thinking"

Set the model as non-reasoning in Shift's custom model settings. Ollama's OpenAI-compatible endpoint does not support the thinking parameter for custom models.

### Model not found

Check the exact model name with `ollama list`. The Model ID in Shift must match exactly. Common mistake: `lastest` vs `latest`.

### Slow/freezing

Model is too large for available RAM and is swapping to disk. Use a smaller quant or a smaller model.

### Tool calls returning XML instead of structured format

Connect directly to Ollama at `http://localhost:11434/v1`. Do not use middleware like LiteLLM between Caido and Ollama.
