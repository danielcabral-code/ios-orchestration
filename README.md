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
| `Story Writer`       | Reads all planning documents and breaks the work into ordered, self-contained development stories. Each story is a Markdown file containing the exact acceptance criteria, files to create/modify, Stitch screen IDs, and assets needed to implement that one piece of the app. |
| `Developer`          | Implements one story at a time. Reads the story file as the primary contract and the full planning docs as reference. Produces Swift source files. |
| `Reviewer`           | Reviews code quality with prioritized findings. Also verifies `project.yml` correctness and Xcode readiness (any failure is a Critical finding). |

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

## MCP servers

Two MCP servers are required. Add them to your VS Code MCP configuration (`~/Library/Application Support/Code/User/mcp.json` on macOS, or `.vscode/mcp.json` for workspace-level):

| Server | Purpose | Key required |
|---|---|---|
| `stitch` | Google Stitch — generates and manages iOS screen mockups | Google API key with Stitch access |
| `nano-banana` | Image generation via Gemini — produces app icons, illustrations, and content images | Gemini API key |

The Designer performs a live connectivity check on both servers before starting any design work. If either is unavailable it halts and surfaces the issue to the Orchestrator.

## Prerequisites

- **xcodegen** — used by the Developer to produce `.xcodeproj` files. Install once:
  ```
  brew install xcodegen
  ```
- **Xcode 16+** with at least one iPhone simulator installed.
- VS Code with GitHub Copilot and both MCP servers configured (see MCP servers section).

## How to use

1. Open VS Code in this workspace.
2. Open Copilot Chat in **Agent mode**.
3. Select the **Orchestrator** agent from the agents dropdown.
4. Describe your request. The Orchestrator will determine the path automatically.

The Orchestrator runs `xcodegen generate` automatically at the end of every implementation path. When it completes, open the generated `.xcodeproj`, select an iPhone simulator, and press Play.

## Document convention

Every planning document produced by a subagent is written to disk as a Markdown file. The Orchestrator coordinates by passing the `<AppName>` folder name — never document contents — between agents.

**New app — all documents live in `<AppName>/`:**

```
<AppName>/
  AppBrief.md
  FunctionalRequirements.md
  TechnicalSpecification.md
  ArchitectureBlueprint.md
  DesignSpec.md
  DesignAssets/
  stories/
    _index.md
    01-foundation.md
    02-navigation-skeleton.md
    03-<feature>.md
project.yml           ← workspace root
```

**New feature — addendum documents live in a scoped subfolder:**

```
<AppName>/features/<FeatureName>/
  FunctionalRequirements.md
  TechnicalSpecification.md
  ArchitectureBlueprint.md
  DesignSpec.md
  DesignAssets/
  stories/
    _index.md
    01-<story>.md
```

`<AppName>` is the app name in PascalCase (e.g. `GlowUpCoach`). `<FeatureName>` is a short PascalCase label derived from the feature description (e.g. `ActivityLog`).

## Request paths

**New app from scratch** — Full pipeline: Ideator → Functional Analyst → Technical Analyst → Architect → Designer → Story Writer → Developer (per story) → Reviewer (per story + final) → `xcodegen generate`.

**New feature on existing code** — Additive pipeline: Functional Analyst → Technical Analyst → Architect → Designer → Story Writer → Developer (per story) → Reviewer (per story + final) → `xcodegen generate`. Each agent scopes strictly to the new feature and preserves existing decisions.

**Bug fix or small change** — Developer directly, then Reviewer loop.

**Review only** — Reviewer directly. Findings returned to the user; no implementation loop.

## Review/fix loop

The Reviewer runs twice per implementation path:

**Per-story** — after the Developer completes each story, the Reviewer checks that story's acceptance criteria and the files it touched. Any `Critical` or `Important` findings go back to the Developer (max 3 iterations per story). The next story does not start until the current story passes.

**Final (whole-app)** — after all stories are complete, the Reviewer does a full sweep for cross-story consistency, Xcode readiness, and integration issues. Same 3-iteration cap applies. `xcodegen generate` runs only after the final review passes.

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

For features:

```
<AppName>/features/<FeatureName>/DesignAssets/<category>/<filename>.png
```

Categories: `AppIcon`, `Launch`, `EmptyStates`, `Onboarding`, `Content`, `3D`

The Developer copies these into `Assets.xcassets` as named image sets before writing any view code. Images are referenced in Swift as `Image("name")` — never by filesystem path. The asset filename (without extension) is the canonical identifier and matches the placeholder name used in Stitch screen mockups.
