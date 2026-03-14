---
name: code-review
description: "Use when reviewing code quality, correctness, maintainability, and regression risk, especially in Swift/iOS projects."
---

# Code Review Skill

## Purpose

Provide a consistent, high-signal code review process focused on code quality and correctness.

## Review Scope

- Focus on implementation quality, not product roadmap.
- Prioritize correctness, readability, maintainability, and regression risk.
- Evaluate changes in the context of existing patterns.

## Review Priorities

1. Correctness
2. Reliability and edge-case behavior
3. Readability and maintainability
4. Simplicity and design consistency
5. Test coverage and regression protection

## Core Checklist

### Correctness

- Does the code do what it claims under normal and edge conditions?
- Are failure paths handled explicitly?
- Are assumptions validated where needed?

### Readability and Maintainability

- Are names specific and intent-revealing?
- Are functions/classes focused and appropriately sized?
- Is logic duplicated unnecessarily?
- Is control flow easy to follow?

### Safety and Reliability

- Are state transitions coherent and predictable?
- Are data mutations safe and recoverable?
- Are risky operations guarded and validated?

### Tests

- Are key behaviors covered by tests?
- Do tests assert outcomes that would catch regressions?
- Are edge and error scenarios represented where risk is high?

## Swift/iOS-Specific Checks

- Check for inappropriate main-thread work and unsafe concurrency usage.
- Verify state ownership and update flow are clear in SwiftUI/UIKit code.
- Validate local persistence operations for data integrity and error handling.
- Confirm offline/local-only assumptions are not accidentally violated.

### Xcode Project Readiness

As part of every review, verify the project can be built and run in Xcode by pressing Play on an iPhone simulator with no manual setup. Any failure is a **Critical** finding.

- `.xcodeproj` / `.xcworkspace` exists at the repo root and opens without errors.
- App target has a valid reverse-DNS Bundle Identifier.
- Deployment target is iOS 17.0 or later.
- Default scheme run destination is set to an iPhone simulator (not "Any iOS Device" or blank).
- No missing (red) file references in the project.
- All `.swift` files are in the Compile Sources phase; all resources are in Copy Bundle Resources.
- `Assets.xcassets` exists and AppIcon set is populated with at least a `1024x1024` image.
- All image names referenced in code have a matching asset catalog entry or a file at the declared path.
- No unresolved Swift Package dependencies.
- Default scheme builds and runs the app target (not a test or framework target).

## Anti-Patterns to Flag

- Hidden side effects across unrelated layers.
- Overly broad refactors bundled with functional changes.
- Large unstructured functions with mixed responsibilities.
- Silent error swallowing without rationale.
- Ambiguous names that obscure intent.

## Output Format

Return findings grouped by severity:

- Critical: likely bugs, crashes, data loss, or severe regressions.
- Important: maintainability or reliability issues that should be addressed soon.
- Nice to have: optional cleanups and polish.

For each finding include:

- What is wrong
- Why it matters
- Suggested fix direction

If no significant issues are found, explicitly state that and mention any residual testing gaps.
