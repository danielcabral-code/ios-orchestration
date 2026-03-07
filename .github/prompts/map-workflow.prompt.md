---
name: map-workflow
description: Generate Mermaid diagrams for the current agent workflow (runtime flow + static configuration map).
tools: ["read", "search"]
argument-hint: Describe what changed in your agents/skills and what detail level you want (quick, medium, thorough).
---

Create a visual map of this repository's Copilot agent workflow.

## Scope

- Treat `.github/agents/*.agent.md` and `.github/skills/**/SKILL.md` as source of truth.
- Focus on actual configured behavior, not hypothetical behavior.

## Required Output

Return exactly these sections in order:

1. `Runtime Flow (Mermaid)`
2. `Configuration Map (Mermaid)`
3. `What Changed` (3-8 bullets)
4. `Assumptions or Gaps` (only if needed)

## Diagram Requirements

### Runtime Flow (Mermaid)

- Use `flowchart TD`.
- Show user request -> orchestrator -> developer -> reviewer -> synthesis -> final response.
- Show the iterative review/fix loop:
  - if Reviewer has `Critical` or `Important` findings, route back to Developer
  - continue until no blocking findings or max iterations reached
- Do not show parallel implementation/review paths.
- Include model primary/fallback for each agent in node labels.
- Include core constraints (iOS only, Swift only, no backend/auth, local storage only).

### Configuration Map (Mermaid)

- Use `classDiagram`.
- Include classes for each agent and skill.
- Show relationships:
  - orchestrator delegates to developer/reviewer
  - developer uses ios-development skill
  - reviewer uses code-review skill
- Include key frontmatter attributes in class bodies:
  - model
  - tools
  - notable constraints

## Style Rules

- Keep diagrams compact and readable.
- Prefer clear labels over excessive detail.
- Ensure Mermaid syntax is valid and directly renderable.
- Do not include placeholder entities that do not exist in this repo.
- Output Mermaid that is paste-safe for Mermaid Live Editor.
- In flowchart node labels, use `<br/>` for line breaks; do not use escaped newlines like `\n`.
- Avoid Markdown formatting inside Mermaid labels (for example `**bold**`).
- Do not use the `|` character inside node/class labels; reserve it for edge conditions only.
- Prefer plain ASCII text in labels; replace punctuation-heavy tokens with safe forms (for example `Nice_to_have`, `GPT_5_3_Codex`).
- In `classDiagram` class bodies, use simple `name value` pairs and avoid complex punctuation.
- Before finalizing, perform a syntax sanity check to ensure both diagrams parse in Mermaid Live Editor without manual edits.

## Notes

If data is missing, state the minimum assumption in `Assumptions or Gaps` and proceed.
