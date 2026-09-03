# Agent instructions

Guidance for AI coding agents (and humans) working in this repository.

## What this repository is

User documentation for the EMBER Archive, built with MkDocs (Material theme) from the `docs/` directory and published to https://docs.emberarchive.org. See `README.md` for how to preview the site locally.

## Secondary, GitHub-only markdown

Some material is meant to be read on GitHub and is deliberately **not** part of the built site. It lives outside `docs/` so MkDocs never picks it up. Do not add it to `mkdocs.yml`, and do not move it under `docs/`.

- `user-stories/`: user stories for tools in the EMBER ecosystem.

Style for these pages:

- Do not use task-list checkboxes (`- [ ]`). Acceptance criteria and similar lists are plain bullets.

## Mermaid diagrams

GitHub renders Mermaid natively in markdown files, so GitHub-only pages can use ` ```mermaid ` fences directly.

- **Do not insert `<br/>` (or `<br>`) line breaks in node or edge labels.** The renderer sizes and wraps labels on its own and the result looks better without manual breaks. Write labels as plain single-line text.
- Keep labels short enough to read at a glance; move detail into the surrounding prose instead of the diagram.
- Diagram types known to render on GitHub and used here: `flowchart`, `sequenceDiagram`, `stateDiagram-v2`, `mindmap`.
