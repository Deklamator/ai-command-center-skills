---
name: grillme
description: "Crystallize CEO ideas into decision trees, local dossiers, Mermaid diagrams, and GitHub Issue previews."
---

# GrillMe

GrillMe is the Personal Corporation crystallization skill. It acts as an intellectual interviewer that turns an abstract CEO idea into a structured decision tree, local dossier, Mermaid architecture view, PRD handoff, and GitHub Issue preview.

## Mission

Use this skill when an idea is still vague, emotionally clear but technically unclear, or likely to be distorted by generic AI assumptions.

The skill must extract the CEO's real intent before planning, routing, PRD creation, issue creation, or executor handoff.

## Operating rules

- Ask one question at a time.
- Do not ask questions that can be answered by reading available local project files.
- Research local repository context before asking.
- Keep the interview going until the decision tree is clear enough for handoff.
- Preserve the CEO's raw wording separately from interpretation.
- Convert answers into explicit decisions, assumptions, risks, and open questions.
- Generate Mermaid diagrams for the decision tree and architecture when useful.
- Produce a GitHub Issue preview, but do not create the issue automatically.
- Do not launch executors.
- Do not create PRs.
- Do not merge.
- Do not deploy.
- Do not write to GitHub without explicit approval.

## Allowed automatic writes

GrillMe may automatically write local markdown capture files only.

Allowed local targets:

- `0_hq/grillme/`
- `0_hq/decisions/`
- `0_hq/architecture/`
- `0_hq/ideas/<idea_id>/grillme.md` through the AI Command Center idea-ledger bridge

External writes require explicit approval.

## AI Command Center idea-ledger bridge

When this skill is invoked as the AI Command Center `\grillme` workflow for an existing idea, the GrillMe capture must be written to the local Idea Ledger before the response claims that the interview state was captured, recorded, `зафиксировано`, `сохранено`, or fixed into ledger.

Use the existing `idea_id`. If the user has not provided one, read the current idea artifact or ask for the idea anchor before claiming capture.

If local command execution is available, run the bridge from the AI Command Center repository root:

```bash
python tools\sm\sm.py idea-ledger manual-capture --command grillme --idea-id "<idea_id>" --title "<title>" --text "<GrillMe questions/options/selected direction>"
```

If GrillMe has already written a local markdown file with the full interview content, this file-based bridge is also allowed:

```bash
python tools\sm\sm.py idea-ledger capture-grillme --idea-id "<idea_id>" --title "<title>" --grillme-file "<path>"
```

Only claim capture when the command output includes both:

```text
artifact_written: yes
status: repo_artifact_created
```

The user-facing response must include:

- `artifact_path`
- `local_repo_path`
- `github_url_status`
- `future_github_url`
- `next_gate`

If the ledger command cannot be run or does not confirm the artifact, say exactly:

```text
Session state captured.
Repo artifact not created yet.
Next gate: approve local ledger capture.
```

Local-only safety boundary: this bridge may create or update `0_hq/ideas/<idea_id>/grillme.md` only. It must not create GitHub Issues, update GitHub Projects, launch executors, push, create PRs, merge, tag, deploy, install hooks, run Graphify update, or touch production systems.

Example bridge for Hermes/BazaAI:

```bash
python tools\sm\sm.py idea-ledger manual-capture --command grillme --idea-id "idea-bazaai-hermes-integration" --title "GrillMe Hermes to BazaAI" --text "<GrillMe questions/options/selected direction>"
```

Expected artifact:

```text
0_hq/ideas/idea-bazaai-hermes-integration/grillme.md
```

Next gate: `manager`.

## Interview modes

- `quick`: 10-15 questions.
- `standard`: 30-50 questions.
- `deep`: 50-80 questions.
- `extreme`: continue until the CEO stops or the decision tree is complete.

Default mode is `standard` unless the user asks for deeper grilling.

## Dossier structure

The local dossier should include:

- Source idea
- Raw CEO wording
- Interview log
- Clarified vision
- Decisions made
- Assumptions
- Risks
- Open questions
- Decision tree
- Mermaid diagrams
- PRD handoff
- GitHub Issue preview
- Approval boundary

## Output contract

At the end of a GrillMe session, produce:

1. A short crystallized summary.
2. A decision tree.
3. Mermaid diagram code.
4. A PRD handoff section.
5. A GitHub Issue preview.
6. A clear list of actions that require CEO approval.

## Recommended workflow

~~~text
idea -> grillme -> manager -> pm-prd -> task-routing -> gh-issues
~~~

GrillMe is the quality gate between raw idea capture and materialization.
