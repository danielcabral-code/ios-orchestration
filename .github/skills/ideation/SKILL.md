---
name: ideation
description: "Use when turning a raw user prompt into a concrete, scoped iOS app concept ready for specification and implementation."
---

# Ideation Skill

## Purpose

Transform a loose idea or one-line prompt into a focused, buildable iOS app concept. The output should be clear enough that a developer can begin implementation without ambiguity, and constrained enough that scope creep is avoided from the start.

## Core Principle

One problem. One clear solution. The minimum feature set to solve it well.

A good app idea is not the most complete or feature-rich answer to a problem. It is the simplest solution that fully solves the core problem without introducing unnecessary complexity.

## Ideation Process

### Step 1 — Extract the Core Problem

Read the raw prompt and identify:

- What problem or friction is the user experiencing?
- Who is the user? (Be specific even if not stated — infer a realistic persona.)
- What does success look like for that user?

Discard surface-level requests and focus on the underlying need. A prompt like "I want a habit tracker" is really "I want to build and maintain consistency in my daily routines."

### Step 2 — Define the App Concept

Commit to a single app concept that:

- Solves the core problem identified in Step 1.
- Is fully achievable on-device with Swift and SwiftUI.
- Requires no backend, no authentication, no remote APIs.
- Can be described in one clear sentence.

If multiple concepts are possible, choose the most focused one. Do not present multiple options — decide.

### Step 3 — Define Core Features

List only the features that are essential to the core experience. Apply the following test to each feature:

- Does removing this feature break the core experience? If yes, keep it.
- Does this feature solve the core problem, or is it a nice-to-have? If nice-to-have, cut it.
- Does this feature introduce significant complexity for marginal user value? If yes, cut it.

Maximum 7 features. Fewer is better. Features must be user-facing and concrete, not technical implementation details.

Good feature: "Log a habit completion for today with a single tap."
Bad feature: "SwiftData persistence layer for habits."

### Step 4 — Define the Data Model

Identify:

- The key entities (for example: Habit, Entry, Category).
- The essential fields for each entity (name, date, value — no over-engineering).
- Relationships between entities (for example: a Habit has many Entries).

Keep the model minimal. Do not add fields speculatively. Only include what the defined features require.

### Step 5 — Define User Flows

Describe 2-4 main paths a user takes through the app. Each flow should be a short numbered sequence of steps. Cover:

- The primary creation or input flow (for example: creating a new item).
- The primary consumption or review flow (for example: viewing progress or history).
- Any critical secondary flow if essential (for example: editing or deleting).

Do not describe UI layout or component details. Describe what the user does and sees at a behavioral level.

### Step 6 — Validate Constraints

Before finalizing, confirm:

- Every feature works fully offline and on-device.
- No feature requires a user account, login, or identity system.
- No feature calls any remote API or service.
- The scope is achievable by a single developer in a focused session.
- The data model maps cleanly to local persistence (SwiftData, Core Data, UserDefaults, or FileManager).

If any feature fails this check, either cut it or reformulate it to be local-only.

## Output Format

Return a structured App Brief with exactly these sections:

### App Name and One-Line Description

A concrete name and a single sentence describing what the app does and for whom.

### Core Features

Numbered list, maximum 7 items. Each feature in one sentence.

### Data Model

Brief description of each entity, its key fields, and relationships. Plain prose or a simple list — no diagrams.

### User Flows

2-4 flows, each as a numbered step sequence. Label each flow clearly (for example: "Create a new habit", "Review weekly progress").

### Constraints and Assumptions

Short bullet list of any constraints applied (for example: iOS only, no backend, local storage) and any assumptions made about the user or context that shaped the concept.

## Quality Bar

A good App Brief produced by this skill should:

- Be immediately understandable by a developer with no further clarification needed.
- Contain zero features that require backend, auth, or remote services.
- Have a data model that maps directly to the features — nothing unused, nothing missing.
- Leave no ambiguity about what the app does and does not do.
- Be achievable as a real working app on an iOS device.
