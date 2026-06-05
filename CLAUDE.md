# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A set of LaTeX CVs/résumés for Emanuele Mason. There is no application code — each top-level folder is one independent document that compiles to a single PDF.

## Repository layout

- `europass-eng/main.tex` — full CV in English, `europecv` document class.
- `europass-ita/main.tex` — full CV in Italian, `europecv` document class.
- `one-page-ita/main.tex` — one-page résumé in Italian, uses the bundled `twentysecondcv.cls` (a vendored LaTeXTemplates class) found alongside it.
- `images/` — shared photos referenced from documents via `../images/...`.
- `publications.bib` — shared bibliography, referenced from the europass docs via `../publications` (note: no `.bib` extension in `\nobibliography`/`\bibliography`).
- `docs/overall_branding.md` — personal-brand/positioning notes (voice, taglines, bio copy). Reference for tone when editing CV prose; not part of any build.

## Build

Local build of a single document (run from inside that document's folder so relative `../images` and `../publications` paths resolve):

```sh
cd europass-eng
latexmk -pdf main.tex      # or: pdflatex main.tex; bibtex main; pdflatex main; pdflatex main
```

The europass documents use BibTeX (`\nobibliography{../publications}` + `bibentry`), so they need the bib pass. `one-page-ita` has no bibliography.

## Release pipeline

`.github/workflows/build-latex.yml` is the source of truth for "official" builds:

- Triggers on pushing a git tag matching `*.*.*` (or manual `workflow_dispatch`).
- Finds **every** `main.tex` via `find . -name main.tex`, compiles each with `xu-cheng/latex-action` using `work_in_root_file_dir` (so each compiles in its own folder).
- Renames each output PDF to its containing folder name (e.g. `europass-eng.pdf`) and publishes them all as assets on a GitHub Release.

Implications when working here:
- A new CV variant = a new folder containing `main.tex`. It will be picked up automatically by the release build — no workflow edit needed.
- The folder name becomes the released PDF's filename, so name folders deliberately.

## Conventions

- All generated LaTeX artifacts (`*.pdf`, `*.aux`, `*.bbl`, `*.log`, etc.) are gitignored — never commit build output.
- Shared assets (images, bibliography) live at the repo root and are referenced with `../` from each document; keep them shared rather than duplicating per folder.
