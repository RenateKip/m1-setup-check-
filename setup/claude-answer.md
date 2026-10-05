# What `make test` does

`make test` runs one command, from the `Makefile`:

```make
test:
	pytest -q
```

It runs pytest in quiet mode (`-q`), which prints dots and a short summary instead of every test name. The repo has no pytest config (`pytest.ini`, `pyproject.toml`, `setup.cfg` or `conftest.py`), so pytest uses its defaults and searches from the repo root. The only test file it finds is `tests/test_smoke.py`, which has two smoke tests:

1. **`test_openapi_document_can_be_loaded`**: loads `docs/openapi.yaml` with PyYAML and checks that:
   - the `openapi` version starts with `3.`
   - `paths` is not empty

   This only proves the API contract is valid YAML with the basic structure. It doesn't validate the contract in depth. `make lint-contract` (`tools/lint_contract.py`) does that.

2. **`test_participant_files_are_present`**: checks that these files exist:
   - `.claude/settings.json`
   - `.devcontainer/devcontainer.json`
   - `CLAUDE.md`
   - `Makefile`
   - `tracker/CR-2.md`
   - `tracker/README.md`

   If any are missing, the failure message lists them (in Latvian: "Trūkst faili: …").

## How it relates to the other targets

- `make verify-setup` runs these same smoke tests. It also checks the Python version, the required packages (FastAPI, Pydantic v2, httpx, pytest, schemathesis), the `claude` CLI, and `setup/claude-answer.md`.
- If you add more `test_*.py` files later, `make test` picks them up automatically.

Per `CLAUDE.md`, run `make test` after every change, and also run `make lint-contract` if you edit `docs/openapi.yaml`.
