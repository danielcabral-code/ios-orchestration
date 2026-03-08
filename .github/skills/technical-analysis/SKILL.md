---
name: technical-analysis
description: "Use when evaluating iOS technical feasibility and selecting the right frameworks, persistence strategy, and platform constraints for a set of functional requirements."
---

# Technical Analysis Skill

## Purpose

Evaluate a Functional Requirements Document against real iOS platform capabilities and constraints. Select the right frameworks, data model strategy, and persistence approach. Identify technical risks and infeasible requirements before any architecture or code is written.

## Core Principle

A technical specification answers: what iOS technologies are needed, which are the right choices, and what are the risks? It does not describe code structure — that is the Architect's job.

## iOS Framework Awareness

### Persistence
- **SwiftData**: preferred for new projects on iOS 17+; model-driven, `@Model` macros, native Swift concurrency.
- **Core Data**: use for existing Core Data projects or complex relational schemas with migration history.
- **UserDefaults**: lightweight key-value only; settings, flags, last-used values. Never for collections or large data.
- **FileManager**: documents, blobs, exported files, or any content the user might share/export.

Choose the simplest option that satisfies all persistence requirements. Do not mix SwiftData and Core Data in the same app.

### UI Framework
- **SwiftUI**: default for all new projects.
- **UIKit**: only if the existing codebase uses UIKit or a specific requirement cannot be met in SwiftUI.
- Never mix frameworks without a justified reason.

### Device APIs (local-only scope)
- **MapKit**: on-device maps and local annotations. No remote geocoding beyond on-device capabilities.
- **CoreLocation**: device GPS/location. No remote services.
- **EventKit**: on-device calendar and reminders.
- **Notifications (UNUserNotificationCenter)**: local notifications only. No push.
- **HealthKit / CoreMotion**: on-device sensor data only.
- **AVFoundation**: local audio/video recording and playback.
- **PhotosUI / PhotosPicker**: on-device photo library access.

Reject any requirement that implies a remote API, authentication service, or cloud sync — flag it as infeasible under local-only constraints.

## Feasibility Assessment

For each functional requirement:

1. Confirm it is achievable using only on-device iOS APIs.
2. Identify the specific framework(s) required.
3. Flag any requirement that:
   - Requires a remote server or API call.
   - Requires user authentication.
   - Cannot be implemented with the chosen persistence strategy.
   - Has significant complexity or performance risk on device.

If a requirement is infeasible, return it to the Functional Analyst with a clear explanation. Do not silently drop or modify requirements.

## Technical Specification Format

Produce a Technical Specification with these sections:

**1. Platform Target**
- Minimum iOS version.
- Deployment device class (iPhone only / iPad / Universal).

**2. Framework Selection**
- UI framework and justification.
- Persistence framework and justification.
- Additional frameworks required (one per requirement that needs one), with reason.

**3. Data Model**
- Concrete entities, fields, and types (informed by the FRD data model).
- Relationships between entities.
- Persistence strategy per entity.

**4. Technical Constraints**
- Local-only constraints confirmed.
- Any requirement-level constraints (for example: max file size, concurrency requirements, background processing).

**5. Risk Register**
- Each identified risk with likelihood and mitigation approach.
- Flag any requirements that were modified or scoped down for feasibility.

**6. Open Questions** (if any)
- Technical ambiguities that require clarification from the Functional Analyst before the Architect can proceed.
