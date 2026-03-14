---
name: functional-requirements
description: "Generates developer-ready functional requirements, acceptance criteria, and structured feature specifications from an iOS App Brief. Use when translating an iOS App Brief, app spec, PRD, product requirements, mobile requirements, or feature documentation into a precise, unambiguous Functional Requirements Document covering happy paths, edge cases, persistence boundaries, and UI states."
---

# Functional Requirements Skill

## Purpose

Transform an App Brief into a precise, unambiguous set of functional requirements that a developer can implement directly — no interpretation required.

## iOS Constraints (Authoritative Reference)

All requirements must conform to these constraints — verified in Step 5:

- State does not persist across launches unless explicitly saved.
- Navigation is stack- or tab-based; flows must respect standard iOS navigation patterns.
- Persistence is local only (SwiftData, Core Data, UserDefaults, or the file system).
- There is no network layer; all data is on-device.
- Requirements must account for foreground, background, and relaunch states where relevant.
- Every data-dependent view must handle empty, loading, and error states.
- No dependencies on backend, auth, or remote services.

## Requirements Process

### Step 1 — Parse the App Brief

Read the App Brief carefully and extract:

- The confirmed feature list (max 7).
- The data model (entities, fields, relationships).
- The user flows.
- The stated constraints and assumptions.

Do not invent features not present in the App Brief. Do not reduce or merge features without flagging it explicitly.

### Step 2 — Write Functional Requirements per Feature

For each feature in the App Brief, produce a set of functional requirements using this format:

**FR-[N]: [Feature Name]**

- Requirement statements, each starting with "The app must..." or "The app shall...".
- Cover the happy path, edge cases, and empty/error states.
- Reference the relevant data entities where applicable.
- Specify persistence behavior explicitly (for example: "The app must persist X across app relaunch using local storage").
- Specify UI state behavior explicitly (for example: "The app must display an empty state message when no items exist").

Each requirement must be:

- Atomic: one condition per statement.
- Testable: a developer or tester can verify it pass/fail.
- Unambiguous: no room for interpretation.

### Step 3 — Define Global Requirements

After per-feature requirements, list global requirements that apply across the whole app:

- Data persistence: what is persisted, when, and how durably.
- App lifecycle: what state is preserved across relaunch, what is reset.
- Empty and error states: general rules for how the app behaves with no data.
- Accessibility: minimum expectations (for example: all interactive elements have accessibility labels).
- Platform conventions: expected iOS navigation and interaction patterns.

### Step 4 — Flag Ambiguities

If the App Brief is unclear on any point, list each ambiguity explicitly in an "Open Questions" section. Do not silently assume — surface the question so the Orchestrator or user can resolve it before development begins.

### Step 5 — Validate Against iOS Constraints

Before finalising, confirm every requirement conforms to the iOS Constraints defined above and does not introduce scope beyond what the App Brief defines.

## Output Format

Return a Functional Requirements Document with exactly these sections:

### Overview

One paragraph summarising the app and the scope of these requirements.

### Functional Requirements

One subsection per feature (FR-1, FR-2, etc.), each containing requirement statements.

### Global Requirements

Requirements that apply across all features.

### Open Questions

Any ambiguities or gaps in the App Brief that must be resolved before development. Omit this section if there are none.

## Quality Bar

A good Functional Requirements Document must:

- Cover every App Brief feature with no omissions.
- Conform to all iOS Constraints listed in the Authoritative Reference above.
- Explicitly state persistence boundaries for all stored data.
- Use only "must" or "shall" — never "should", "might", or "could".
