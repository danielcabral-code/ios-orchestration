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

Every deliverable must be runnable in Xcode by pressing Play with no manual setup. **Always use xcodegen** to generate the `.xcodeproj` — do not hand-write Xcode project XML.

### xcodegen Setup

Create a `project.yml` file at the repo root. xcodegen reads this file and produces a correct `.xcodeproj` with all source files, resources, build settings, and scheme pre-configured.

**Minimal `project.yml` template:**

```yaml
name: <AppName>
options:
  bundleIdPrefix: com.example
  deploymentTarget:
    iOS: "17.0"
  xcodeVersion: "16.0"
  generateEmptyDirectories: true
configs:
  Debug: debug
  Release: release
targets:
  <AppName>:
    type: application
    platform: iOS
    deploymentTarget: "17.0"
    sources:
      - <AppName>
    resources:
      - <AppName>/Assets.xcassets
      - <AppName>/Resources
    settings:
      base:
        PRODUCT_BUNDLE_IDENTIFIER: com.example.<AppName>
        TARGETED_DEVICE_FAMILY: "1"
        SWIFT_VERSION: "5.9"
        INFOPLIST_FILE: <AppName>/Info.plist
        GENERATE_INFOPLIST_FILE: YES
        CURRENT_PROJECT_VERSION: 1
        MARKETING_VERSION: 1.0.0
    scheme:
      testTargets: []
schemes:
  <AppName>:
    build:
      targets:
        <AppName>: all
    run:
      config: Debug
    archive:
      config: Release
```

Adjust `sources` and `resources` to match the actual folder structure from the Architecture Blueprint. If the app uses Swift packages, add a `packages:` section and reference them under `dependencies:` in the target.

After writing `project.yml`, also create an `Info.plist` at `<AppName>/Info.plist` with at minimum:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>CFBundleName</key>
    <string>$(PRODUCT_NAME)</string>
    <key>CFBundleDisplayName</key>
    <string><AppName></string>
    <key>CFBundleIdentifier</key>
    <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    <key>CFBundleVersion</key>
    <string>$(CURRENT_PROJECT_VERSION)</string>
    <key>CFBundleShortVersionString</key>
    <string>$(MARKETING_VERSION)</string>
    <key>UILaunchScreen</key>
    <dict/>
    <key>UISupportedInterfaceOrientations</key>
    <array>
        <string>UIInterfaceOrientationPortrait</string>
    </array>
</dict>
</plist>
```

Add any `NSUsageDescription` keys required by the app (camera, location, etc.) to this `Info.plist`.

### Assets.xcassets Setup

Create `<AppName>/Assets.xcassets/Contents.json`:
```json
{ "info": { "author": "xcode", "version": 1 } }
```

Create `<AppName>/Assets.xcassets/AppIcon.appiconset/Contents.json`:
```json
{
  "images": [
    {
      "idiom": "universal",
      "platform": "ios",
      "size": "1024x1024",
      "filename": "app-icon.png"
    }
  ],
  "info": { "author": "xcode", "version": 1 }
}
```

For each named image asset referenced in code, create `<AppName>/Assets.xcassets/<name>.imageset/Contents.json`:
```json
{
  "images": [
    { "idiom": "universal", "filename": "<name>.png" }
  ],
  "info": { "author": "xcode", "version": 1 }
}
```

### Delivery Verification

Before completing a task, verify the project is press-and-play:

- `project.yml` exists at repo root and is syntactically valid
- `<AppName>/Info.plist` exists; all required usage description keys are present
- `Assets.xcassets` contains `AppIcon.appiconset` with a `1024×1024` PNG
- Every image referenced by name in code has a matching `.imageset` with `Contents.json`
- All source files are under the folder path declared in `project.yml` `sources`
- All resource folders are listed under `resources`
- If any Swift packages are used, they are declared in `project.yml` and auto-resolve on first open
- The default scheme runs the app target in Debug
- **Include these exact instructions in the implementation summary:** "Run `xcodegen generate` in the project root, then open `<AppName>.xcodeproj`, select an iPhone simulator, and press Play."

## Definition of Done

- Feature works on-device with no network dependency.
- Data survives expected app lifecycle events when required.
- Code is readable, maintainable, and aligned with project conventions.
- Known trade-offs and assumptions are explicitly documented.
- `project.yml` exists at the repo root; running `xcodegen generate` produces a clean `.xcodeproj` with no missing references.
- User's only required actions are: run `xcodegen generate`, open `.xcodeproj`, select simulator, press Play.
