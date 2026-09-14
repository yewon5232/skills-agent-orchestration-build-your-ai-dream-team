# Project Pulse Agent Team

Four custom agents in `.github/agents/` work together to build the Project Pulse dashboard.

## Agents

| Agent | Model | Responsibility | File |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (copilot) | Breaks the request into phases and delegates to the specialists. Coordinates only, never implements. | `.github/agents/orchestrator.md` |
| Planner | Claude Opus 4.7 (copilot) | Researches the repository and produces an ordered implementation plan with file assignments and dependencies. Writes no code. | `.github/agents/planner.md` |
| Designer | Gemini 3.1 Pro (copilot) | Owns UI/UX, accessibility, layout, and visual styling for the dashboard. | `.github/agents/designer.md` |
| Coder | GPT-5.5 (copilot) | Implements the application files and supporting configuration such as `.vscode/launch.json`. | `.github/agents/coder.md` |

## How the team will work together

1. I give the Orchestrator a single request: build the Project Pulse dashboard.
2. The Orchestrator asks the Planner for an implementation plan with ordered steps, file ownership, and dependencies.
3. The Orchestrator splits that plan into phases, giving each specialist an explicit file scope.
4. The Designer defines the dashboard experience and owns `app/styles.css`.
5. The Coder builds `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`.
6. Tasks run in parallel only when file scopes do not overlap; overlapping or dependent work runs sequentially.
7. The Orchestrator verifies the integrated result and reports the final handoff.
8. No agent touches git. I run every stage, commit, and push myself through Copilot CLI prompts.