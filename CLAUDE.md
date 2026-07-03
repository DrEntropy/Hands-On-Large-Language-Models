# CLAUDE.md

Personal fork of **Hands-On Large Language Models** (Alammar & Grootendorst). The book's
chapters are Jupyter notebooks under `chapter01/` … `chapter12/` (plus `bonus/`).

## Environment

- This fork is **uv-managed** (upstream uses conda). Use `uv` for everything:
  - `uv sync` — create/update the environment from `pyproject.toml`.
  - `uv run jupyter lab` — launch notebooks.
  - `uv run python ...` / `uv run pytest ...` — run any Python.
- Never use system Python or a conda env.
- Deps are in `pyproject.toml` (mirrors upstream `requirements.txt` pins). The upstream
  `requirements.txt`, `requirements_min.txt`, and `environment.yml` are kept for reference only.
- Targets Python 3.10 (`requires-python = ">=3.10,<3.12"`), matching the book's pinned deps.

## Fork-specific changes vs. upstream

- Added **`ollama`** for experimenting with local models.
- `bitsandbytes` is gated to non-macOS (`sys_platform != 'darwin'`) — no macOS wheels exist;
  it's only needed for CUDA 4-bit quantization in the ch. 12 fine-tuning notebook.
- `llama-cpp-python` drops the conda `-C cmake.args=...BLAS` build flag (not expressible as a
  dep); on macOS it builds with Metal automatically.

## Known issues / gotchas

- **openai + httpx conflict (hits ch. 4 OpenAI client creation).** `openai==1.34.0` passes
 `proxies=` to `httpx.Client()`, which httpx 0.28 removed (`TypeError: unexpected keyword
 argument 'proxies'`). Fixed by pinning `httpx==0.27.2` (matches upstream `environment.yml`).
- **gensim + scipy conflict (hits the ch. 5 clustering/topic-modeling area).** The pinned
 `gensim==4.3.2` calls `scipy.linalg.triu`, which was removed in scipy 1.13; our `scipy>=1.15`
 will trigger an `ImportError` at runtime when gensim is used. uv installs both without
 complaint — the break only shows up when the gensim code actually runs.
 **Fix when we get there:** bump to `gensim==4.3.3` (adds scipy 1.13+ compatibility) in
 `pyproject.toml`, then `uv sync`.
