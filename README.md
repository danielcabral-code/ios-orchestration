# iOS App Development — Copilot Agent Workflow

A multi-agent pipeline for building local-only iOS apps with Swift and SwiftUI, coordinated through VS Code Copilot Agent mode.

## Agents

| Agent                | Role                                                                                                   |
| -------------------- | ------------------------------------------------------------------------------------------------------ |
| `Orchestrator`       | Main coordinator. Routes requests, manages agent handoffs, runs the review/fix loop.                   |
| `Ideator`            | Translates a raw prompt into a scoped App Brief.                                                       |
| `Functional Analyst` | Produces a Functional Requirements Document from an App Brief or feature description.                  |
| `Technical Analyst`  | Evaluates feasibility, selects frameworks, defines the data model. Produces a Technical Specification. |
| `Architect`          | Designs the MVVM structure, navigation graph, and folder layout. Produces an Architecture Blueprint.   |
| `Designer`           | Generates image assets (nano-banana) and screen mockups (Google Stitch). Produces a Design Spec.       |
| `Developer`          | Implements Swift/SwiftUI code from the full document package. Runs a project readiness check before finishing — the resulting Xcode project must build and run on an iPhone simulator by pressing Play with no manual setup. |
| `Reviewer`           | Reviews code quality with prioritized findings. Also verifies Xcode project readiness (any failure is a Critical finding). |

All subagents are `user-invocable: false` — use them through the Orchestrator.

## Skills

Each agent is backed by one or more reference skills in `.github/skills/`:

- `ideation` — App Brief format and feature scoping
- `functional-requirements` — Requirements structure and quality bar
- `technical-analysis` — Feasibility assessment and framework selection
- `architecture` — MVVM layers, navigation, folder structure
- `design` — iOS/SwiftUI styles, color palettes, typography, accessibility
- `ios-development` — SwiftUI patterns, persistence, device APIs
- `code-review` — Quality, correctness, maintainability

## How to use

1. Open VS Code in this workspace.
2. Open Copilot Chat in **Agent mode**.
3. Select the **Orchestrator** agent from the agents dropdown.
4. Describe your request. The Orchestrator will determine the path automatically.

## Request paths

**New app from scratch** — Full pipeline: Ideator → Functional Analyst → Technical Analyst → Architect → Designer → Developer → Reviewer loop.

**New feature on existing code** — Additive pipeline: Functional Analyst → Technical Analyst → Architect → Designer → Developer → Reviewer loop. Each agent scopes strictly to the new feature and preserves existing decisions.

**Bug fix or small change** — Developer directly, then Reviewer loop.

**Review only** — Reviewer directly. Findings returned to the user; no implementation loop.

## Review/fix loop

After every implementation path, the Reviewer runs and its findings are checked:

- `Critical` or `Important` findings → sent back to Developer for fixes
- Loop repeats until no blocking findings remain or a maximum of 3 iterations is reached
- At the iteration cap, the best known working implementation is kept and remaining risks are reported

## Project constraints

All apps in this workflow are:

- iOS only, Swift, SwiftUI
- Device-local: no backend, no auth, no remote APIs
- Persistence via SwiftData, Core Data, UserDefaults, or FileManager

## Generated assets

The Designer saves all nano-banana image assets into the workspace under:

```
<AppName>/DesignAssets/<category>/<filename>.png
```

Categories: `AppIcon`, `Launch`, `EmptyStates`, `Onboarding`, `Content`, `3D`

The Developer copies these into `Assets.xcassets` as named image sets before writing any view code. Images are referenced in Swift as `Image("name")` — never by filesystem path.
