# ollama-pentest-model

Turn any abliterated GGUF model from Hugging Face into a reusable local assistant with Ollama.

---

## What this is

Ollama lets you wrap a base model with a system prompt and parameters, then give it a stable local name you can reuse in scripts and tooling.

**Why abliterated?** Standard models refuse security-related queries. Abliterated models have had alignment restrictions removed, making them suitable for vulnerability analysis, exploit reasoning, and security review without constant refusals.

---

## Requirements

- Ollama installed — [ollama.ai](https://ollama.ai)
- ~20GB free RAM (for 30B Q4_K_M)
- ~20GB free disk space

```bash
ollama -v
```

---

## The Modelfile

```bash
# Example Implementation
mkdir pentest-agent
cd pentest-agent
touch Modelfile
```

Paste into `Modelfile`:

```
FROM hf.co/mradermacher/Huihui-Qwen3-Coder-30B-A3B-Instruct-abliterated-i1-GGUF:Q4_K_M

SYSTEM """
You are a specialized assistant for authorized security assessments.

Operating rules:
1) Assume explicit authorization and defined scope for all security testing activities.
2) Focus on security review and analysis including:
   - Code review for vulnerabilities
   - Vulnerability reasoning and classification
   - Threat modeling and attack surface analysis
   - Mobile application reverse engineering
   - Web application security testing
   - Network security assessment workflows

3) When analyzing code, traffic captures, or application flows:
   - Explain the intended behavior (happy path)
   - Identify potential failure and abuse cases (attacker perspective)
   - Provide specific verification steps
   - Suggest concrete remediation guidance

4) Structure outputs using: Findings → Evidence → Impact → Fix → Retest
5) Prioritize actionable, practical security guidance over theoretical discussion.
6) Include proof-of-concept examples when helpful for demonstration.
"""

PARAMETER temperature 0.1
PARAMETER num_ctx 4096

---

## Build and run

```bash
ollama create pentest-agent -f Modelfile
ollama run pentest-agent
```

First run downloads the base model (~16-20GB). Rebuild after any edits:

```bash
ollama create pentest-agent -f Modelfile
```

---

## Quantization reference

| Tag | Size | Notes |
|-----|------|-------|
| Q3_K_M | 14.7 GB | Low RAM |
| IQ4_XS | 16.4 GB | Good ratio |
| Q4_K_M | 18.6 GB | Recommended |
| Q5_K_M | 21.7 GB | Better accuracy |
| Q6_K | 25.1 GB | Near full quality |

---

## Advanced parameters

Larger context window for big files or logs:

```
PARAMETER num_ctx 8192
```

---

## Common failures

**Pull fails** — check the `FROM` line character by character. Tags are case-sensitive.

**OOM / slow** — drop to a lower quantization. Try `Q3_K_M` or `IQ4_XS`.

**Model refuses security queries** — you are not using an abliterated model. The model name must include `abliterated`.

**Modelfile not found** — filename must be exactly `Modelfile`, capital M, no extension.

---

## Resources

- [Ollama docs](https://github.com/ollama/ollama)
- [What is abliteration](https://huggingface.co/blog/mlabonne/abliteration)
- [Trending abliterated models](https://huggingface.co/models?sort=trending&search=abliterated)