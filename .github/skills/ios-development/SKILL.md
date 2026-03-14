---
name: ios-development
description: "Builds iOS user interfaces with SwiftUI or UIKit, implements local data persistence (SwiftData, Core Data, UserDefaults, FileManager), integrates device APIs (camera, location, sensors), configures Xcode projects, and establishes app lifecycle and concurrency patterns. Use when building iOS apps in Swift with no backend or authentication dependencies — including creating views and navigation, wiring up local storage, accessing on-device hardware features, or setting up a runnable Xcode project from scratch."
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

## Feature Workflow

Follow these steps when implementing a new feature, validating at each checkpoint before proceeding:

1. **Define the model** — create or update the data model (SwiftData `@Model`, Core Data entity, or plain Swift struct).
2. **Verify build** — ensure the project compiles cleanly with no errors.
3. **Add the view** — build the SwiftUI view using appropriate property wrappers; confirm preview renders.
4. **Wire persistence** — connect the view to the data layer; test save/load round-trip manually.
5. **Handle edge cases** — empty state, loading state, error state must all be reachable and visually handled.
6. **Test** — unit test the view model/business logic; add regression tests for persistence edge cases.
7. **Confirm Definition of Done** — run through the checklist at the bottom of this document before marking complete.

## Implementation Rules

1. Start from the smallest correct change.
2. Keep state ownership explicit.
3. Handle empty/loading/error states in UI flows.
4. Ensure persistence operations are safe and predictable.
5. Preserve existing style and structure unless asked to refactor.

## SwiftUI Guidance

- Prefer single-responsibility views.
- Keep view models focused and side-effect boundaries clear.
- Use property wrappers correctly based on ownership and lifecycle (`@State`, `@Binding`, `@StateObject`, `@ObservedObject`, `@Environment`, `@AppStorage`).
- Minimize view body complexity with extracted subviews and helper methods.

**Example — SwiftUI view with correct property wrapper usage:**

```swift
// ViewModel owns state; view observes it
@MainActor
final class ItemListViewModel: ObservableObject {
    @Published var items: [Item] = []
    @Published var isLoading = false

    func load(from store: ItemStore) async {
        isLoading = true
        defer { isLoading = false }
        items = await store.fetchAll()
    }
}

struct ItemListView: View {
    @StateObject private var viewModel = ItemListViewModel()
    @Environment(\.itemStore) private var store

    var body: some View {
        Group {
            if viewModel.isLoading {
                ProgressView()
            } else if viewModel.items.isEmpty {
                ContentUnavailableView("No Items", systemImage: "tray")
            } else {
                List(viewModel.items) { item in
                    ItemRow(item: item)
                }
            }
        }
        .task { await viewModel.load(from: store) }
    }
}
```

## Concurrency Guidance

- Use structured concurrency (`async`/`await`, `Task`).
- Keep UI mutations on the main actor when required.
- Avoid detached tasks unless isolation is intentionally required.
- Ensure cancellation paths are respected for long-running work.

**Example — async persistence operation on the main actor:**

```swift
@MainActor
func saveItem(_ item: Item) async {
    do {
        try await store.insert(item)
    } catch {
        // Surface error to UI; never silently swallow
        self.errorMessage = error.localizedDescription
    }
}
```

## Persistence Guidance

- Model clear entities and identifiers.
- Validate user input before persistence.
- Avoid silent data loss on updates/deletes.
- Keep migration impact in mind when changing stored schemas.
- For local-only apps, treat stored data as source of truth.

**Example — SwiftData model setup:**

```swift
import SwiftData

@Model
final class Item {
    var id: UUID
    var title: String
    var createdAt: Date

    init(title: String) {
        self.id = UUID()
        self.title = title
        self.createdAt = .now
    }
}

// In the App entry point:
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(for: Item.self)
    }
}

// In a view:
struct ContentView: View {
    @Query private var items: [Item]
    @Environment(\.modelContext) private var context

    var body: some View {
        List(items) { item in Text(item.title) }
        .toolbar {
            Button("Add") {
                context.insert(Item(title: "New"))
            }
        }
    }
}
```

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

- Load `.usdz` assets from the app bundle; do not fetch remotely.
- Use `SCNScene(named:)` and handle the nil case; use `ModelEntity.loadAsync(named:)` and catch load errors.
- Use `.allowsCameraControl` for user-rotatable objects; omit for static display.
- Wrap 3D views in a sized container — they do not self-size reliably in SwiftUI layouts.
- Load large `.usdz` files asynchronously and show a `ProgressView` while loading.
- Test on device; SceneKit/RealityKit performance in Simulator is unreliable.

---

## Testing Focus

- Unit test view models and business logic.
- Add regression tests for persistence edge cases.
- Add focused UI tests for critical user flows when feasible.
- Validate behavior for first launch, empty state, and app relaunch persistence.

## Xcode Project Configuration

Every deliverable must be runnable in Xcode by pressing Play with no manual setup. Verify before considering the task done:

- **Project file:** Single `.xcodeproj` or `.xcworkspace` at repo root; opens without missing-file errors.
- **Bundle Identifier:** Reverse-DNS format, e.g. `com.example.<AppName>`; non-empty and unique.
- **Deployment Target:** iOS 17.0 or later.
- **Info.plist:** `INFOPLIST_FILE` or `GENERATE_INFOPLIST_FILE` correctly set so the app target builds.
- **Supported Destinations:** At least one iPhone form factor.
- **Run destination:** Default scheme targets an iPhone simulator (e.g. iPhone 16) — not "Any iOS Device".
- **Compile Sources:** Every `.swift` file in the app target's Compile Sources phase; no red references in the Project Navigator.
- **Copy Bundle Resources:** Asset folders, storyboards, `.xib`, and resource files all included.
- **Asset catalog:** `Assets.xcassets` exists and is bundled; AppIcon set populated with a `1024×1024` universal single-scale image (Xcode 14+). Any image referenced by name in code must have a matching named image set.
- **Swift Package dependencies:** All declared packages resolved; section empty if no external packages are used.
- **Scheme:** Default scheme builds and runs the app target (not a test target or framework); Run configuration is `Debug`.

## Definition of Done

- Feature works on-device with no network dependency.
- Data survives expected app lifecycle events when required.
- Code is readable, maintainable, and aligned with project conventions.
- Known trade-offs and assumptions are explicitly documented.
- Project passes all Xcode Project Configuration checks above — pressing Play in Xcode on an iPhone simulator produces a running app.
