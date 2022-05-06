# poetry-setup-composite-action v1

This allows to install a poetry setup and uses it's cache if available. Also runs a single `poetry run` command.

# What's New

- easier Poetry installation
- uses cached Poetry installations
- run a poetry command

# Usage

see [action.yml](action.yml)

### run pytest (as example)

```yaml
jobs:
  run-pytest-setup:
    runs-on: ubuntu-latest
    name: Run Pytest with Poetry
    steps:
      - uses: actions/checkout@v3
      - id: poetry-setup
        uses: makanu/poetry-setup-composite-action@v1
        with:
          python_version: '3.7'
          hashed_key: "venv-${{ runner.os }}-${{ hashFiles('**/poetry.lock') }}"
          poetry_command: "pytest"
```
Change the value behind `poetry_command` to any other `<command>` which you otherwise want to run with `poetry run <command>`

# Troubleshot

### Broken Cache

It might happen that the cached venv is somehow broken, for example by fooling around with `actions/upload-artifact` and `actions/upload-artifact`. In this case the you need to change the `hashed_key` value to a new version.

either directly in the workflow:

```diff
jobs:
  run-pytest-setup:
    runs-on: ubuntu-latest
    name: Run Pytest with Poetry
    steps:
      - uses: actions/checkout@v3
      - id: poetry-setup
        uses: makanu/poetry-setup-composite-action@v1
        with:
          python_version: '3.7'
-          hashed_key: "venv-${{ runner.os }}-${{ hashFiles('**/poetry.lock') }}"
+          hashed_key: "venv-${{ runner.os }}-V1-${{ hashFiles('**/poetry.lock') }}"
          poetry_command: "pytest"
```

or via a saved secret:

```diff
jobs:
  run-pytest-setup:
    runs-on: ubuntu-latest
    name: Run Pytest with Poetry
    steps:
      - uses: actions/checkout@v3
      - id: poetry-setup
        uses: makanu/poetry-setup-composite-action@v1
        with:
          python_version: '3.7'
-          hashed_key: "venv-${{ runner.os }}-${{ hashFiles('**/poetry.lock') }}"
+          hashed_key: "venv-${{ runner.os }}-${{ secrets.CACHE_VERSION }}-${{ hashFiles('**/poetry.lock') }}"
          poetry_command: "pytest"
```
