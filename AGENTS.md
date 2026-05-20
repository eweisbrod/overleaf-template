# AGENTS.md - AI Assistant Context for overleaf-template

> **What is this file?** AGENTS.md is an emerging open standard for
> providing AI coding assistants with project context. It is supported
> by Claude Code, Cursor, Windsurf, GitHub Copilot, and other AI tools.
> Think of it as a README for AI — it tells any AI assistant how this
> project is structured and what conventions to follow.

## Repo role

This repository is the **LaTeX paper template** that accompanies the
[`project-template`](https://github.com/eweisbrod/project-template)
code repository. It is one half of a two-repo workflow:

- `project-template` produces `.tex` files (regression tables,
  descriptive stats) and `.pdf` files (figures) in its `OUTPUT_DIR`.
- This repo's `main.tex` consumes those files via `\input{}` and
  `\includegraphics{}` to build the final manuscript.

The repo is set up as a GitHub **template repository**, and it is
also linked to Overleaf via Git so the LaTeX source can be edited
either from a local clone or from the Overleaf web editor. The two
views stay in sync via `git push` / `git pull`.

The example paper bundled here is the same earnings-announcement
event study used in `project-template`, so the tables and figures
referenced in the manuscript text correspond to the artifacts that
template produces out of the box.

## Repo structure

```
overleaf-template/
├── .gitignore         # LaTeX build artifacts + .DS_Store
├── AGENTS.md          # This file
├── CLAUDE.md          # Claude Code config (imports AGENTS.md)
├── Bibliography.bib   # Bibliography database
├── main.tex           # The paper itself
├── figures/           # PDF / PNG figures (produced by project-template)
└── tables/            # .tex tables (produced by project-template)
```

Users typically clone this repo, then either:
- Copy the .tex / .pdf outputs from `project-template`'s `OUTPUT_DIR`
  into `tables/` and `figures/` here, then build with `pdflatex` /
  `latexmk`; or
- Sync this repo to Overleaf and upload the same files via Overleaf's
  web UI.

## Key conventions

### Citations and bibliography

- **biblatex-chicago** (author-year style, configured in the preamble)
  is the citation package. Loaded with `maxnames=2` — papers with 3+
  authors render as `Author et al.` after their first citation, with
  the `\AtEveryCitekey{\ifciteseen{}{\defcounter{maxnames}{99}}}` hack
  expanding the first occurrence in full.
- Citation commands available: `\citet{key}` (in-text),
  `\citep{key}` (parenthetical), `\citep[see][p.~37]{key}` (prefixed),
  `\citepos{key}` (possessive, e.g. "Bernard's").
- **Bibliography keys must exist in `Bibliography.bib`.** Never insert
  a citation you have not verified is present — `biber` will silently
  drop the citation and print a question-mark placeholder, which is
  easy to miss in the PDF.
- The Bibliography.bib database is Eric's accumulated personal
  library; ~450 entries. When adding a new reference, paste the
  full BibTeX entry into the file.

### Hypotheses and research questions

The preamble defines two auto-numbered environments via `ntheorem`:

```latex
\begin{hyp}[H\ref{hyp:name}] \label{hyp:name}
...
\end{hyp}

\begin{researchq}[RQ\ref{researchq:name}] \label{researchq:name}
...
\end{researchq}
```

Both auto-number on first use. Reference them elsewhere with
`H\ref{hyp:name}` / `RQ\ref{researchq:name}`.

### Table inputs

Tables are NOT typed by hand here. They come from
`project-template`'s `OUTPUT_DIR`:

- R outputs: `regression-r.tex`, `descrip-r.tex`, etc.
- Python outputs: `regression-py.tex`, etc.
- Stata outputs: `regression-stata.tex`, etc.

The `-r` / `-py` / `-stata` suffix lets parallel implementations
coexist without filename collision. `main.tex` uses `\input{...}` to
pull whichever variant the user prefers.

### Document class

`IEEEtran` (12pt) is the document class. Section formatting is
overridden in the preamble (`titlesec`) to match the typical
Accounting/Finance journal style: bold section headings, numbered,
flush left. Tables and figures use `TABLE` / `FIGURE` capitalized
labels on their own lines via `caption` package config.

### Math environments

Equations are numbered and labeled (`\label{eq:...}`). The `amsmath`
package is loaded; `\begin{aligned}` is the standard multi-line
equation environment.

## Common pitfalls

- **Compiling locally requires `biber` and `biblatex-chicago`.** Both
  ship with full TeX Live and MiKTeX distributions. Overleaf has them
  preinstalled. A bare-bones `pdflatex` install will fail at the
  bibliography step.
- **Citation keys are case-sensitive.** `bochkay2022roles` and
  `Bochkay2022Roles` are different keys; biber will fail to match.
- **`tabularray` + `tinytable` preamble bits** (lines ~30-39 of
  `main.tex`) are required for `modelsummary` output from R to render
  correctly. If you remove them, R tables will compile but lose their
  formatting.
- **Don't edit the .tex tables directly here.** Edit the analysis
  code in `project-template`, re-run script 4, and copy the regenerated
  .tex file into this repo. Direct hand-edits get overwritten on the
  next pipeline run.
- **`maxnames=2` + the `\AtEveryCitekey` hook**: a 3-author paper's
  FIRST citation expands fully ("Bochkay, Markov, and Subasi"); the
  second and later use "Bochkay et al." Two-author papers always show
  both names (the `maxnames=2` threshold). Do not write "et al." for
  two-author papers — that's incorrect and biblatex won't render it
  that way.
