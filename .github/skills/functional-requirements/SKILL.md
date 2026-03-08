---
name: functional-requirements
description: "Use when translating an iOS App Brief into detailed, developer-ready functional requirements."
---

# Functional Requirements Skill

## Purpose

Transform an App Brief into a precise, unambiguous set of functional requirements that a developer can implement directly — no interpretation required. Requirements must be grounded in how iOS apps actually work: state management, navigation, persistence, and offline-first behavior.

## Core Principle

A functional requirement describes what the app must do and under what conditions — not how to build it. Each requirement must be testable, specific, and tied to a real user need from the App Brief.

## iOS Context Awareness

Requirements must reflect how iOS apps behave on device:

- State is owned by the app process; it does not persist across launches unless explicitly saved.
- Navigation is stack- or tab-based; flows must respect standard iOS navigation patterns.
- Persistence is local only; data survives app relaunch only if written to SwiftData, Core Data, UserDefaults, or the file system.
- There is no network layer; all data is on-device.
- The app lifecycle matters: requirements must account for foreground, background, and relaunch states where relevant.
- UI must handle empty states, loading states, and error states for every data-dependent view.

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

If the App Brief is unclear on any point, list each ambiguity explicitly in an "Open Questions" section at the end. Do not silently assume — surface the question so the Orchestrator or user can resolve it before development begins.

### Step 5 — Validate Against iOS Constraints

Before finalising, confirm every requirement:

- Is achievable without a backend, auth, or remote API.
- Maps to the data model defined in the App Brief.
- Respects standard iOS app lifecycle and persistence behavior.
- Does not introduce scope beyond what the App Brief defines.

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

A good Functional Requirements Document produced by this skill should:

- Cover every feature from the App Brief with no omissions.
- Have zero requirements that depend on backend, auth, or remote services.
- Explicitly state persistence boundaries for all stored data.
- Handle empty, loading, and error states for every data-dependent view.
- Be directly usable by a developer as an implementation checklist.
- Contain no ambiguous language ("should", "might", "could" — use "must" or "shall").
