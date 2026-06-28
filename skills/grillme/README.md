# grillme

GrillMe is the Personal Corporation crystallization skill.

It interviews the CEO, researches local project context before asking, captures answers into a local dossier, builds a decision tree, creates Mermaid diagrams, and prepares a GitHub Issue preview.

## What it is for

Use `grillme` when an idea is not ready for PRD, routing, GitHub Issues, or executor handoff.

It helps prevent generic AI output by forcing unclear areas into explicit CEO decisions.

## Key behavior

- One question at a time.
- Read local repo context before asking.
- Auto-write local markdown dossier.
- Build decision tree.
- Generate Mermaid diagrams.
- Produce PRD handoff.
- Produce GitHub Issue preview.
- Require approval before any GitHub write.

## Safety boundary

Allowed automatically:

- local markdown capture only

Not allowed automatically:

- GitHub Issues
- GitHub Projects
- executor launch
- PR creation
- merge
- deploy