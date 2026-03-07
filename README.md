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

The Orchestrator is configured with the `agent` tool and restricted to these subagents:

- `Developer`
- `Reviewer`

Workflow behavior:

- Orchestrator runs `Developer` first.
- Then it runs `Reviewer`.
- If `Reviewer` reports `Critical` or `Important` findings, Orchestrator sends fixes back to `Developer`.
- This loop repeats until no blocking findings remain, or max iterations is reached.

Both workers are `user-invocable: false`, so they are intended to run through the Orchestrator.
