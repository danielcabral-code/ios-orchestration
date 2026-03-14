---
name: code-review
description: "Use when reviewing code quality, correctness, maintainability, and regression risk — including PR review, pull request review, code audit, or 'check my code' requests — especially in Swift/iOS projects. Analyzes code for bugs, identifies unsafe concurrency and threading issues, flags potential memory leaks and silent error swallowing, suggests refactoring opportunities, and verifies Xcode project readiness. Produces actionable findings grouped by severity (Critical, Important, Nice to have), each with a clear explanation and suggested fix direction."
---

# Code Review Skill

## Purpose

Provide a consistent, high-signal code review process focused on code quality and correctness.

## Review Workflow

Follow this sequence for every review:

1. **Build verification** — Confirm the project compiles and the Xcode readiness checklist passes before analysing logic.
2. **Diff analysis** — Read the full change set to understand scope, intent, and what was deliberately left out.
3. **Checklist review** — Apply the core checklist and Swift/iOS-specific checks against the diff and surrounding context.
4. **Write findings** — Group findings by severity, then present them in the output format below. Explicitly note if no significant issues were found.

## Core Checklist

### Correctness and Reliability
- Does the code do what it claims under normal and edge conditions?
- Are failure paths handled explicitly and assumptions validated?
- Are state transitions coherent, and are data mutations safe and recoverable?
- Are risky operations guarded and validated?
- Are key behaviors covered by tests, including edge and error scenarios where risk is high?

### Readability and Maintainability
- Are names specific and intent-revealing?
- Are functions/classes focused and appropriately sized?
- Is logic duplicated unnecessarily?
- Is control flow easy to follow?

## Swift/iOS-Specific Checks

- Check for inappropriate main-thread work and unsafe concurrency usage.
- Verify state ownership and update flow are clear in SwiftUI/UIKit code.
- Validate local persistence operations for data integrity and error handling.
- Confirm offline/local-only assumptions are not accidentally violated.

### Common Swift Issue Examples

**Unsafe main-thread work:**

```swift
// ❌ Before: heavy fetch blocks the main thread
@MainActor
func loadData() {
    let result = URLSession.shared.synchronousDataTask(url: url) // blocks UI
    process(result)
}

// ✅ After: offload to a background task, publish result on main actor
func loadData() async {
    let result = await fetchDataInBackground()
    await MainActor.run { process(result) }
}
```

**Silent error swallowing:**

```swift
// ❌ Before: error silently discarded
func save(_ item: Item) {
    do { try store.write(item) } catch { }
}

// ✅ After: log and propagate or surface to the user
func save(_ item: Item) throws {
    do { try store.write(item) }
    catch {
        logger.error("Failed to save item \(item.id): \(error)")
        throw error
    }
}
```

### Xcode Project Readiness

As part of every review, verify the project can be built and run in Xcode by pressing Play on an iPhone simulator with no manual setup. Any failure is a **Critical** finding.

| Area | Check |
|---|---|
| Project file | `.xcodeproj` / `.xcworkspace` exists at repo root and opens without errors |
| Bundle ID | Valid reverse-DNS identifier in *Signing & Capabilities* |
| Deployment target | iOS 17.0 or later (*General → Minimum Deployments*) |
| Run destination | Default scheme targets an iPhone simulator, not "Any iOS Device" or blank |
| File references | No missing (red) files in the project navigator |
| Build phases | All `.swift` files in *Compile Sources*; all resources in *Copy Bundle Resources* |
| Assets | `Assets.xcassets` exists; AppIcon populated with at least a `1024×1024` image; all image names referenced in code have matching asset entries |
| Packages | No unresolved Swift Package dependencies |
| Default scheme | Builds and runs the app target, not a test or framework target |

## Anti-Patterns to Flag

- Hidden side effects across unrelated layers.
- Overly broad refactors bundled with functional changes.
- Large unstructured functions with mixed responsibilities.
- Silent error swallowing without rationale.
- Ambiguous names that obscure intent.

## Output Format

Return findings grouped by severity:

- **Critical**: likely bugs, crashes, data loss, or severe regressions.
- **Important**: maintainability or reliability issues that should be addressed soon.
- **Nice to have**: optional cleanups and polish.

For each finding include:

- What is wrong
- Why it matters
- Suggested fix direction

If no significant issues are found, explicitly state that and mention any residual testing gaps.
