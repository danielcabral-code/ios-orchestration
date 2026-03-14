---
name: ideation
description: "Transforms raw user prompts into concrete, scoped iOS app concepts by defining core features, identifying target users, outlining technical scope, and producing a structured App Brief ready for implementation. Use when brainstorming iPhone or iOS app ideas, planning a new mobile application, or turning a rough app concept into a buildable specification."
---

# Ideation Skill

## Purpose

Transform a loose idea or one-line prompt into a focused, buildable iOS app concept. The output should be clear enough that a developer can begin implementation without ambiguity, and constrained enough that scope creep is avoided from the start.

## Core Principle

One problem. One clear solution. The minimum feature set to solve it well.

## Ideation Process

### Step 1 — Extract the Core Problem

Identify:

- What problem or friction is the user experiencing?
- Who is the user? (Be specific — infer a realistic persona if not stated.)
- What does success look like for that user?

Focus on the underlying need, not the surface-level request.

### Step 2 — Define the App Concept

Commit to a single app concept that:

- Solves the core problem identified in Step 1.
- Is fully achievable on-device with Swift and SwiftUI.
- Requires no backend, no authentication, no remote APIs.
- Can be described in one clear sentence.

If multiple concepts are possible, choose the most focused one. Do not present multiple options — decide.

### Step 3 — Define Core Features

List only features essential to the core experience. Apply this test to each:

| Test | Action |
|------|--------|
| Removing it breaks the core experience | Keep |
| Nice-to-have rather than core | Cut |
| High complexity for marginal value | Cut |

Maximum 7 features. Fewer is better. Features must be user-facing and concrete — not technical implementation details (e.g. "Log a habit with a single tap", not "SwiftData persistence layer").

### Step 4 — Define the Data Model

Identify:

- The key entities (for example: Habit, Entry, Category).
- The essential fields for each entity (name, date, value — no over-engineering).
- Relationships between entities (for example: a Habit has many Entries).

Only include what the defined features require. Do not add fields speculatively.

### Step 5 — Define User Flows

Describe 2–4 main paths a user takes through the app. Each flow is a short numbered step sequence. Cover:

- The primary creation or input flow (for example: creating a new item).
- The primary consumption or review flow (for example: viewing progress or history).
- Any critical secondary flow if essential (for example: editing or deleting).

Describe what the user does and sees at a behavioral level — not UI layout or component details.

### Step 6 — Validate Constraints

Before finalizing, confirm every feature passes all of the following:

- Works fully offline and on-device.
- Requires no user account, login, or identity system.
- Calls no remote API or service.
- Is achievable by a single developer in a focused session.
- Maps cleanly to local persistence (SwiftData, Core Data, UserDefaults, or FileManager).

If any feature fails this check, cut it or reformulate it to be local-only.

## Output Format

Return a structured App Brief with exactly these sections:

### App Name and One-Line Description

A concrete name and a single sentence describing what the app does and for whom.

### Core Features

Numbered list, maximum 7 items. Each feature in one sentence.

### Data Model

Brief description of each entity, its key fields, and relationships. Plain prose or a simple list — no diagrams.

### User Flows

2–4 flows, each as a numbered step sequence. Label each flow clearly (for example: "Create a new habit", "Review weekly progress").

### Constraints and Assumptions

Short bullet list of constraints applied (for example: iOS only, no backend, local storage) and assumptions made about the user or context that shaped the concept.

## Quality Bar

A good App Brief produced by this skill should:

- Be immediately understandable by a developer with no further clarification needed.
- Have a data model that maps directly to the features — nothing unused, nothing missing.
- Leave no ambiguity about what the app does and does not do.
- Be achievable as a real working app on an iOS device.
