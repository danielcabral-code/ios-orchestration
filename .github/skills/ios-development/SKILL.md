---
name: ios-development
description: "Use when building iOS apps in Swift with SwiftUI/UIKit, local persistence, device APIs, and no backend/auth dependencies."
---

# iOS Development Skill

## Purpose

Provide a consistent, production-minded workflow for building iOS apps that run fully on-device.

## Project Constraints

- Platform: iOS only.
- Language: Swift.
- Architecture: local-first, device-only behavior.
- No backend services.
- No authentication flows.
- No remote API integration.

## Preferred Stack

- UI: SwiftUI by default.
- UIKit: only when existing codebase already uses UIKit patterns or a user requests it.
- Persistence (pick the simplest valid option):
  - SwiftData for modern model-driven storage on recent targets.
  - Core Data for mature relational persistence or existing Core Data projects.
  - UserDefaults for lightweight settings/preferences.
  - FileManager for document/blob-style local content.

## Implementation Rules

1. Start from the smallest correct change.
2. Keep features modular and testable.
3. Avoid premature abstraction.
4. Keep state ownership explicit.
5. Handle empty/loading/error states in UI flows.
6. Ensure persistence operations are safe and predictable.
7. Preserve existing style and structure unless asked to refactor.

## SwiftUI Guidance

- Prefer single-responsibility views.
- Keep view models focused and side-effect boundaries clear.
- Use property wrappers correctly (`@State`, `@Binding`, `@StateObject`, `@ObservedObject`, `@Environment`, `@AppStorage`) based on ownership/lifecycle.
- Minimize view body complexity with extracted subviews and helper methods.

## Concurrency Guidance

- Use structured concurrency (`async`/`await`, `Task`).
- Keep UI mutations on the main actor when required.
- Avoid detached tasks unless isolation is intentionally required.
- Ensure cancellation paths are respected for long-running work.

## Persistence Guidance

- Model clear entities and identifiers.
- Validate user input before persistence.
- Avoid silent data loss on updates/deletes.
- Keep migration impact in mind when changing stored schemas.
- For local-only apps, treat stored data as source of truth.

## Code Quality Baseline

- Naming must be clear and domain-specific.
- Functions should be concise and intent-revealing.
- Avoid duplicated logic; extract shared helpers when justified.
- Prefer explicit types at boundaries where readability improves.
- Add brief comments only for non-obvious logic.

## UX and Platform Quality

- Respect Dynamic Type and accessibility labels/hints where relevant.
- Use system components and platform conventions.
- Keep navigation and interactions predictable.
- Design for offline-first behavior since app is device-local.

## Testing Focus

- Unit test view models and business logic.
- Add regression tests for persistence edge cases.
- Add focused UI tests for critical user flows when feasible.
- Validate behavior for first launch, empty state, and app relaunch persistence.

## Definition of Done

- Feature works on-device with no network dependency.
- Data survives expected app lifecycle events when required.
- Code is readable, maintainable, and aligned with project conventions.
- Known trade-offs and assumptions are explicitly documented.
