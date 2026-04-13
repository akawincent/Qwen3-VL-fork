# AGENTS.md

## Purpose

This repository is a mixed Qwen3-VL workspace: demo app, local utility package, fine-tuning code, evaluation pipelines, and notebooks. Make focused, minimal changes and keep them inside the correct submodule.

## Repo Map

- `web_demo_mm.py`: main Gradio demo entrypoint.
- `qwen-vl-utils/src/qwen_vl_utils/`: reusable image/video preprocessing utilities.
- `qwen-vl-finetune/qwenvl/`: training code and argument definitions.
- `qwen-vl-finetune/scripts/`: training launch scripts and DeepSpeed configs.
- `evaluation/*/`: benchmark-specific inference/eval pipelines. Treat each benchmark folder as self-contained.
- `cookbooks/`: examples and notebooks. Avoid editing notebooks unless the task is explicitly about documentation or demos.

## Working Rules

- Prefer the smallest possible patch. Do not refactor across modules unless required.
- Keep changes consistent with the existing code style; this repo is mostly simple Python scripts, not a heavily abstracted framework.
- When changing reusable preprocessing logic, modify `qwen-vl-utils` rather than duplicating code in demos or eval scripts.
- When changing training behavior, keep CLI arguments and shell scripts aligned.
- Do not trigger large model downloads, long training jobs, or full benchmark runs unless the user asks for them.
- Preserve compatibility with the current stack: Python 3.10+, `transformers>=4.57.0`, PyTorch-based workflows, optional vLLM/DeepSpeed paths.

## Setup

Use the root project for general development:

```bash
pip install -e ".[dev]"
```

If you modify the local utility package, install it in editable mode too:

```bash
pip install -e ./qwen-vl-utils
```

For fine-tuning work:

```bash
pip install -e ".[finetune]"
```

## Validation

Prefer cheap, targeted checks:

- Python syntax check: `python -m compileall web_demo_mm.py qwen-vl-finetune/qwenvl qwen-vl-utils/src/qwen_vl_utils evaluation`
- Lint changed files with `ruff check ...`
- For demo changes, verify CLI args and import path without launching a heavy model.
- For `evaluation/*`, run only the affected script with a small/local sample if possible.
- For fine-tuning changes, validate argument parsing or script composition before any real training run.

## Notes For Codex

- Read the nearest `README.md` inside the subdirectory you are editing before making non-trivial changes.
- Prefer script or library changes over notebook edits.
- Call out GPU, API key, dataset, or checkpoint assumptions explicitly in the final message.
- Ignore unrelated local artifacts such as `.DS_Store` unless the user asks to clean them up.


## Remote execution policy (user-specific)
- For this workspace, run all development commands on the remote server via `ssh qwenvl-dev` in `/run/determined/workdir/home/Qwen3-VL-fork`.
- Do not run project development commands directly on the local macOS host.
- Before any project command, explicitly run `source /run/determined/workdir/home/.bashrc`.
- Do not rely on `~/.bashrc` path resolution in remote shells; use the absolute path `/run/determined/workdir/home/.bashrc`.
- Assume `$HOME` is `/run/determined/workdir/home/`.
- Treat the Hugging Face cache root as the remote-server path `/run/determined/workdir/home/.cache/huggingface`.
- When running model or dataset commands remotely, explicitly export `HF_HOME=/run/determined/workdir/home/.cache/huggingface`.
- Do not use quoted `~/.cache/huggingface`, and do not use the local macOS host cache path.
- Use the project virtual environment under `.venv` (and `uv` from `/run/determined/workdir/home/.local/bin/uv` when needed).

## Way of doing things
Please use first principles thinking. Don't always assume that I know exactly what I want and how to get there. Please remain prudent, start from the original needs and questions, and if the motivation and goals are unclear, stop and discuss with me. If the goals are clear but the path is not the shortest, let me know and suggest a better approach.

## Programming requirements
- Do not overthink during implementation; prioritize direct, pragmatic solutions.
- Do not overuse defensive programming; avoid unnecessary guards and abstractions when normal control flow is clear.