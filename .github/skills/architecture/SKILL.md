---
name: architecture
description: "Use when designing the structural blueprint of an iOS app: folder structure, MVVM layers, navigation graph, protocols, and component boundaries."
---

# Architecture Skill

## Purpose

Design a clear, minimal structural blueprint for an iOS app based on a Functional Requirements Document and a Technical Specification. The Architecture Blueprint defines how the codebase is organised, how components relate, and where responsibilities live — so the Developer can begin writing code without making unguided structural decisions.

## Core Principle

Architecture describes _how the app is structured_, not what it does (FRD) or which frameworks to use (Technical Spec). It defines layers, boundaries, and contracts. Good iOS architecture is predictable, testable, and easy to navigate. Avoid over-engineering.

## Architectural Pattern: MVVM

Default to MVVM for all SwiftUI apps:

- **Model**: data entities and persistence. Owns the data layer. Ideally pure Swift types with no UI dependencies.
- **ViewModel**: owns state and business logic for a screen or feature. Mediates between Model and View. Exposes `@Published` properties. Has no UIKit/SwiftUI imports beyond `Foundation`/`Combine` where possible.
- **View**: SwiftUI views. Reads from ViewModel. Sends user actions to ViewModel. Contains no business logic.

Deviation from MVVM is acceptable only when explicitly justified (for example: a purely presentational screen with no state may not need a ViewModel).

## Folder Structure

Organise by feature, not by type. Each feature folder contains its View, ViewModel, and any feature-local models.

```
AppName/
├── App/
│   ├── AppNameApp.swift      ← app entry point
│   └── ContentView.swift     ← root view / navigation container
├── Features/
│   └── FeatureName/
│       ├── FeatureNameView.swift
│       ├── FeatureNameViewModel.swift
│       └── (FeatureNameModel.swift if feature-local)
├── Models/
│   └── SharedEntityName.swift  ← shared data entities
├── Persistence/
│   └── PersistenceController.swift  ← SwiftData/Core Data stack
├── Services/
│   └── (shared services: notifications, location, etc.)
└── Utilities/
    └── (extensions, helpers used across features)
```

Only create a layer if it is needed. Do not create empty folders or placeholder files.

## Navigation Design

- Define the top-level navigation structure: TabView, NavigationStack, or both.
- Identify each distinct screen and its navigation entry point.
- Specify which screens are pushed (NavigationStack), presented as sheets, or shown as tabs.
- Name the root view/container and describe how it owns navigation state.

## Component Boundaries

For each feature:

- Name the View and ViewModel.
- State what data the ViewModel holds (derived from the FRD + data model).
- State what actions/intents the ViewModel exposes to the View.
- State what the ViewModel reads from the persistence layer.

## Protocol Contracts

Define protocols only where they add testability or decoupling value:

- Persistence access patterns (for example: an `ItemRepository` protocol with a concrete SwiftData implementation).
- Device service wrappers (for example: a `LocationProvider` protocol wrapping CoreLocation).
- Do not create protocols for ViewModels unless a concrete requirement for abstraction exists.

## Architecture Blueprint Format

Produce an Architecture Blueprint with these sections:

**1. Navigation Structure**

- Top-level container (TabView / NavigationStack).
- Screen inventory: name, navigation method (push / sheet / tab), and owning feature.

**2. Feature Breakdown**
For each feature: View name, ViewModel name, ViewModel state properties, ViewModel actions, persistence interactions.

**3. Shared Models**

- Shared entity definitions and their persistence layer.

**4. Persistence Layer**

- Persistence controller or container setup.
- How features access the persistence layer (direct model context / repository pattern).

**5. Services Layer** (if needed)

- Any shared service classes (for example: NotificationService, LocationService).

**6. Folder Structure**

- Concrete folder and file tree for the full app.

**7. Open Questions** (if any)

- Structural ambiguities requiring Technical Analyst or Functional Analyst clarification before the Developer can begin.
