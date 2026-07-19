---
name: analysis
description: Answer a quantitative question with a Jupyter notebook that is the report — committed with outputs so the conclusion reads without running anything. Use when a question is answered by computing over data (parameter sweeps, tolerance/regression analysis, comparing distributions, performance measurements), when a bug diagnosis turns quantitative (flake rates, parameter sensitivity), or when you're about to write a one-off script whose output — numbers, plots, tables — is the actual deliverable.
---

# Analysis

An analysis answers a quantitative question with a notebook that **is the report**: the question at the top, the computation in the middle, the conclusion at the end — committed **with outputs**, so a reader sees the answer rendered in the repo browser without running anything. The code is how you got there; the results are the deliverable.

## The notebook

- **One notebook per question**, in `notebooks/` at the repo root (match the repo's existing convention if it already keeps notebooks elsewhere), named for the question in kebab-case: `notebooks/regression-gate-sweep.ipynb`.
- **Provenance header** — the first cell, markdown: the question, the date of the last clean run, and the git ref plus data files the analysis ran against. A reader judges staleness from this cell alone.
- **Narrative order**: question → data → computation → conclusion, markdown cells carrying the reasoning between code cells. End on a **Conclusion** cell that answers the question in a sentence or two.
- **Results are graphical** wherever a plot can carry the answer; small tables where exact values matter.
- **Parameter-tuning questions** get interactive controls (ipywidgets) for live exploration — *plus* a static view of the swept results (overlay plot, small multiples), because the committed render shows static outputs only. The static view is the report; the controls are for the live reader.
- **Large generated datasets** are recorded as pandas DataFrames.
- Supporting modules may live beside the notebook in `notebooks/`, but the notebook is the entry point — a reader starts and finishes there.

## Environment

- In a uv-managed repo, notebook dependencies live in a `notebooks` dependency group — `uv add --group notebooks jupyter pandas matplotlib ipywidgets` plus whatever the analysis needs — so they never leak into the package's real dependencies.
- In a repo that isn't Python, `notebooks/` becomes its own self-contained uv project: Python is the analysis lingua franca regardless of the host language.
- Execution is one reproducible command: `uv run --group notebooks jupyter nbconvert --to notebook --execute --inplace notebooks/<name>.ipynb`.

## Maintenance

- **Same question → same notebook.** Work that revisits an analysis — new data, changed code, a follow-up "what if" — extends or updates the existing notebook. A new notebook means a new question.
- **Every commit follows a clean run**: restart and execute all cells top-to-bottom (the command above), so committed outputs always reflect the current code in execution order.
- **The first notebook in a repo plants the consistency rule.** Add to the repo's `CLAUDE.md`:

  > Notebooks in `notebooks/` analyze code in this repo (see `notebooks/README.md` for which modules each imports). When you modify a module a notebook imports, re-run that notebook top-to-bottom, update it if the change altered its results or broke it, and commit the refreshed outputs.

  Create `notebooks/README.md` alongside: one line per notebook — its question and the repo modules it imports. Keep it current as notebooks are added.
- **A conclusion that triggers an ADR cross-references it both ways**: the ADR cites the notebook as its evidence; the notebook's conclusion cell links the ADR. Record the ADR itself via the `/domain-modeling` skill.

## Completion criterion

The analysis is done when the notebook is committed with outputs from a clean run, its Conclusion cell answers the question posed in its header, and — if this was the repo's first notebook — the `CLAUDE.md` rule and `notebooks/README.md` are in place.
