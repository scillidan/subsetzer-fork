# Subsetzer-llamacpp

![Python](https://img.shields.io/badge/python-3.9%2B-informational?style=flat-square)

Translate subtitle files (SRT/VTT/TSV) using a local llama.cpp server or DeepSeek. CLI-first, no external dependencies.

Forked and substantially rewritten from the original [subsetzer](https://github.com/githabideri/subsetzer) by Martin Fellner.
The core translation pipeline (chunking, batch translation, tag protection, cleanup) follows the original design.

Authors: GLM-5.1🧙‍♂️, DeepSeek-V4-Pro🧙‍♂️, scillidan🤡

## Install

```bash
uv tool install -U git+https://github.com/scillidan/subsetzer-fork
```

## Quickstart

```bash
subsetzer --model gemma-3-12b-it --target "Chinese" --input "Movie.en.srt"
subsetzer --model gemma-3-12b-it --format srt --no-punc --target "Chinese" --input "Movie.en.srt"
DEEPSEEK_API_KEY=... subsetzer --model deepseek-flash --llm-mode deepseek --no-stream --target "Chinese" --input "Movie.en.srt"
```

## Usage Details

### Input / Output

- `--input PATH` — input subtitle file (`.srt` / `.vtt` / `.tsv`)
- `--output PATH` — output file path (optional; defaults to `{input_stem}.{src}2{dst}.{ext}` next to input)
  - Absolute path: used directly
  - Relative path: resolved from current working directory
  - Existing directory: auto-named file placed inside it
- `--force` — overwrite without prompting
- `--format srt|vtt|tsv` — output format (default: `srt`)

### Server & Model

- `--host URL` — llama.cpp or DeepSeek base URL (default: selected backend)
- `--model NAME` — model tag, required (e.g. `gemma-3-12b-it`, `deepseek-flash`)
- `--llm-mode auto|chat|generate|deepseek` — API endpoint mode (default: `auto`)
  - DeepSeek mode reads `DEEPSEEK_API_KEY`, disables thinking, and retries 429/5xx responses
  - Optional `DEEPSEEK_BASE_URL` is supported by the context-menu wrapper; direct CLI users can pass `--host`

### Language

- `--source LANG` — source language (default: `auto`). Accepts ISO codes (`en`, `zh-cn`), English names (`Chinese`).
- `--target LANG` — target language, required. Same format as `--source`.

### Chunking & Batching

- `--max-chars N` — max characters per chunk (default: `4000`)
- `--cues-per-request N` — cues per LLM request (default: `1`)

### Options

- `--no-translate-bracketed` — preserve `[MUSIC]` and similar tags
- `--no-punc` — remove punctuation (！？。，""《》… etc.) and replace with spaces; keep `-` for dialogue markers
- `--stream` / `--no-stream` — toggle streaming (default: on)
- `--timeout SECS` — HTTP timeout (default: `60`)
- `--no-llm` — dry-run, reuse source text as output
- `--debug` — verbose logging
- `--capture-raw` — save raw LLM payloads to `{output}_raw.txt`
- `--version` — print version and exit

### Model Notes

Choose a model with strong translation ability at 12B+ parameters. Smaller checkpoints often echo source text despite automatic retries. The system prompt explicitly instructs the model to translate and never echo; if output remains untranslated, try a different model or adjust `--llm-mode`.

### License

GPL-3.0-or-later
