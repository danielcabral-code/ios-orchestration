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

## 3D Content

Use SceneKit or RealityKit only when the Design Spec explicitly calls for an interactive or displayed 3D object.

| Framework  | When to use                                                       | SwiftUI integration         |
| ---------- | ----------------------------------------------------------------- | --------------------------- |
| SceneKit   | Non-AR 3D scenes, rotatable objects, supported on all iOS targets | `SceneView(scene:options:)` |
| RealityKit | AR scenes or when the Design Spec specifies RealityKit            | `RealityView` (iOS 17+)     |

Implementation rules:

- Load `.usdz` assets from the app bundle. Do not fetch remotely.
- Use `SceneView.Options` `.allowsCameraControl` for user-rotatable objects; omit for static display.
- For `SceneView`, load the scene with `SCNScene(named:)` and handle the nil case gracefully.
- For `RealityView`, load entities with `ModelEntity.loadAsync(named:)` and catch load errors.
- Wrap 3D views in a sized container — they do not self-size reliably in SwiftUI layouts.
- Test on device; SceneKit/RealityKit performance in Simulator is unreliable.
- If the `.usdz` file is large, load asynchronously and show a `ProgressView` while loading.

---

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
