# Repository Guidelines

## Project Structure & Module Organization

This repository stores Software Architecture course materials and exercises.

- `README.md` contains the repository title.
- `Slides/` contains lecture PDFs and Markdown exports, such as `Microservices.md` and `Event-driven.md`.
- `sa-note/markdown/` contains written notes; `sa-note/pdf/` contains PDF note versions.
- `sa-ex/ex01/`, `sa-ex/ex02/` contain exercise prompts, submitted answers, and related PDFs.

Keep new exercise work inside the matching `sa-ex/exNN/` folder. Prefer editing Markdown source files when both Markdown and PDF versions exist.

## Build, Test, and Development Commands

There is no application build pipeline in this repository. Useful local checks:

- `rtk rg --files` lists tracked-style project files quickly.
- `rtk sed -n '1,120p' path/to/file.md` previews Markdown content.
- `rtk git status --short` checks changed and untracked files.
- `rtk git diff -- path/to/file.md` reviews edits before commit.

Use `rtk` before shell commands in this workspace.

## Coding Style & Naming Conventions

Use Markdown for written work. Keep headings hierarchical (`#`, `##`, `###`) and tables readable in plain text. Write concise Vietnamese for course submissions unless the source material is in English. Preserve existing filenames for student submissions, for example `23127075.md`, and use descriptive names for prompts or notes, such as `bai_tap_kien_truc_smartbooking.docx.md`.

Avoid unnecessary formatting churn in generated Markdown exports. Do not rename PDFs unless the matching Markdown/source file is renamed consistently.

## Testing Guidelines

No automated tests are configured. Before submitting documentation changes, manually verify:

- Markdown renders cleanly in preview.
- Tables have consistent column counts.
- Required student metadata, such as name and MSSV, is present for submissions.
- Exercise answers match the prompt requirements.

## Commit & Pull Request Guidelines

Recent commits use short, direct messages such as `completed: exercise 1` and `modified: json`. Follow the same concise style, using a scope when helpful: `completed: exercise 2` or `docs: add contributor guide`.

Pull requests should include a brief description, list changed folders, and note whether the change affects slides, notes, or exercise submissions. For rendered document changes, include screenshots or exported PDFs only when they help reviewers confirm formatting.

## Agent-Specific Instructions

Respect existing untracked course materials and submissions. Do not delete or overwrite user work without explicit approval. Keep generated guidance specific to this repository rather than adding generic software project instructions.
