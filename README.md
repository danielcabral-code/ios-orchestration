# VS Code Copilot Subagents Setup

This workspace defines three custom agents in `.github/agents`:

- `orchestrator.agent.md` (main coordinator)
- `developer.agent.md` (implementation subagent)
- `reviewer.agent.md` (review subagent)

## How to use

1. Open VS Code in this workspace.
2. Open Copilot Chat in **Agent mode**.
3. Select the **Orchestrator** agent from the agents dropdown.
4. Prompt normally, for example:
   - `Implement a small utility to parse env flags and add tests.`
   - `Review the task list feature for bugs and maintainability.`
   - `Implement feature X and iterate until review is clean.`

The Orchestrator is configured as a coordinator (no direct file edits) and restricted to these subagents:

- `Developer`
- `Reviewer`

Workflow behavior:

- For implementation prompts, Orchestrator runs `Developer` first, then `Reviewer`.
- For review-only prompts, Orchestrator skips `Developer` and runs `Reviewer` directly.
- If `Reviewer` reports `Critical` or `Important` findings and implementation changes are in scope, Orchestrator sends fixes back to `Developer`.
- This loop repeats until no blocking findings remain, or max iterations is reached.
- If max iterations is reached, Orchestrator keeps the best known working implementation and reports remaining risks.

Both workers are `user-invocable: false`, so they are intended to run through the Orchestrator.
