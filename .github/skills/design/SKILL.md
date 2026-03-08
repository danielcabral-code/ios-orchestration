---
name: design
description: "Use when designing iOS app UI/UX with SwiftUI: style selection, color palettes, typography, spacing, accessibility, interaction patterns, and screen-level design decisions."
---

# iOS Design Skill

## Purpose

Provide complete, self-contained design guidance for iOS apps built with SwiftUI. Covers style selection, color systems, typography, spacing, interaction patterns, accessibility, and screen-level design decisions. No external tools or scripts required — everything needed is in this file.

---

## Core iOS Design Principles

- Follow Human Interface Guidelines (HIG) as the baseline. Deviate only with clear intent.
- Prefer system components over custom ones. `List`, `Form`, `TabView`, `NavigationStack`, `Sheet`, `Alert`, `Toggle`, `Picker` — use them.
- Use SF Symbols for all icons. They scale, adapt to Dynamic Type, and support multicolor and hierarchical rendering.
- Design for all text sizes. Dynamic Type must never break layouts.
- Design for both light and dark mode unless the app brief explicitly restricts to one.
- Touch targets must be at least 44×44pt.

---

## Step 1 — Style Selection

Choose one primary style that fits the app's purpose. Apply it consistently across all screens.

| Style | Best For | SwiftUI Aesthetic |
|---|---|---|
| **System / HIG-native** | Utility, productivity, data-heavy apps | Default iOS controls, no decoration |
| **Minimal** | Focus, writing, meditation, finance | Generous whitespace, muted palette, light typography weight |
| **Warm / Editorial** | Journaling, recipes, lifestyle | Serif accents, earthy palette, soft corners |
| **Bold / Expressive** | Fitness, gaming, social | High contrast, saturated color, strong type |
| **Dark / Immersive** | Media, music, night use | Dark backgrounds, glowing accents, reduced borders |
| **Soft / Friendly** | Children, wellness, onboarding | Rounded corners, pastel palette, playful type |
| **Professional / Neutral** | Business, finance, reference | Cool grays, tight spacing, structured layout |

**Selection rule:** match the app's primary emotion. A task manager is productive (System or Minimal). A mood tracker is personal (Warm or Soft). A workout tracker is energetic (Bold).

---

## Step 2 — Color System

Define exactly five semantic color roles. Map each to a SwiftUI system color or a specific hex value. Do not define more roles than you use.

| Role | Purpose | SwiftUI Default |
|---|---|---|
| `primary` | Main action color, active state, key UI accent | `.accentColor` (customise in asset catalog) |
| `secondary` | Supporting actions, secondary labels, icons | `.secondary` |
| `destructive` | Delete, remove, irreversible actions | `.red` |
| `background` | Screen background | `.systemBackground` |
| `surface` | Cards, grouped list backgrounds, sheet backgrounds | `.secondarySystemBackground` |

### Palette Reference

Pick the palette that matches your chosen style. All values are adaptable to light/dark automatically if you use system colors; hex values below are light mode — invert luminance for dark.

**Minimal / Neutral**
- primary: `#1C1C1E` (near-black)
- secondary: `#8E8E93` (system gray)
- accent: `#0A84FF` (system blue)
- background: `#FFFFFF`
- surface: `#F2F2F7`

**Warm / Editorial**
- primary: `#3D2314` (deep brown)
- secondary: `#9C7B6A` (muted clay)
- accent: `#D4622A` (terracotta)
- background: `#FBF7F2` (off-white)
- surface: `#F0E8DE`

**Bold / Energetic**
- primary: `#1D1D1F`
- secondary: `#6E6E73`
- accent: `#FF3B30` (system red) or `#FF9F0A` (system orange)
- background: `#000000`
- surface: `#1C1C1E`

**Soft / Friendly**
- primary: `#3A3A3C`
- secondary: `#AEAEB2`
- accent: `#5E5CE6` (system indigo/purple)
- background: `#F9F9FB`
- surface: `#EFEFF4`

**Professional**
- primary: `#1C1C1E`
- secondary: `#8E8E93`
- accent: `#34C759` (system green) or `#0A84FF`
- background: `#F2F2F7`
- surface: `#FFFFFF`

### Dark Mode
- Replace `background` with `.systemBackground` (resolves automatically).
- Replace `surface` with `.secondarySystemBackground`.
- Ensure accent color meets 4.5:1 contrast on both backgrounds.
- Never hardcode white or black — use semantic system colors.

---

## Step 3 — Typography

SwiftUI uses Dynamic Type text styles. Always map your design roles to these — never use fixed font sizes.

| Design Role | SwiftUI Text Style | Typical Use |
|---|---|---|
| Large Title | `.largeTitle` | Screen hero titles, onboarding |
| Title | `.title` / `.title2` / `.title3` | Section headers, card titles |
| Headline | `.headline` | List row primary label, prominent body |
| Body | `.body` | Default reading text |
| Callout | `.callout` | Supplementary body, pull quotes |
| Subheadline | `.subheadline` | Secondary labels, metadata |
| Footnote | `.footnote` | Captions, legal, timestamps |
| Caption | `.caption` / `.caption2` | Chart labels, image captions |

### Font Pairing (for expressive styles only)

Use custom fonts only when the app's style calls for it. Default to SF Pro (system font) for utility apps.

| Pairing | Style | SwiftUI Usage |
|---|---|---|
| SF Pro only | Neutral, System, Professional | Default — no custom font needed |
| New York (serif) + SF Pro | Warm, Editorial | `.font(.system(.title, design: .serif))` |
| Rounded SF Pro | Soft, Friendly | `.font(.system(.body, design: .rounded))` |
| Monospaced SF Pro | Technical, Code | `.font(.system(.body, design: .monospaced))` |

**Rule:** if in doubt, use SF Pro. Never use more than two font designs in one app.

---

## Step 4 — Spacing Scale

Use a consistent spacing scale across all screens. Define three values and stick to them.

| Token | Value | Use |
|---|---|---|
| `small` | 8pt | Internal component padding, icon-to-label gaps |
| `medium` | 16pt | Section padding, row insets, card padding |
| `large` | 24pt | Between sections, screen-level vertical rhythm |

For list row insets, `medium` (16pt) matches the iOS system default. Use this unless deliberately overriding.

Corner radius:
- Small controls (tags, chips): 8pt
- Cards and surfaces: 12pt
- Sheets and modals: 16pt (system default)
- Buttons: use `.buttonStyle` defaults or 10-12pt

---

## Step 5 — Interaction Patterns

Use iOS-native patterns. Never invent custom gestures or navigation metaphors when a system equivalent exists.

| Pattern | When to Use | SwiftUI Implementation |
|---|---|---|
| Swipe to delete | Destructive row action | `.onDelete` on `ForEach` |
| Swipe actions | Non-destructive row actions | `.swipeActions` |
| Pull to refresh | Refreshable list | `.refreshable` |
| Context menu | Secondary actions on long press | `.contextMenu` |
| Sheet | Focused sub-task, creation flow | `.sheet` |
| Full-screen cover | Immersive flow (onboarding, media) | `.fullScreenCover` |
| Confirmation dialog | Irreversible destructive action | `.confirmationDialog` |
| Alert | Short error or status message | `.alert` |
| Navigation push | Moving deeper into a hierarchy | `NavigationLink` |
| Tab switching | Switching between top-level sections | `TabView` |

**Rule:** use `.sheet` for creation/editing tasks that return to the same context. Use `NavigationLink` push for drilling deeper into content.

---

## Step 6 — UI States per Screen

Every data-dependent screen must have a design for each of these states:

| State | When | What to Show |
|---|---|---|
| **Default** | Data loaded, normal use | Primary content |
| **Empty** | No data yet | Illustration or SF Symbol + short message + optional CTA |
| **Loading** | Async fetch in progress | Skeleton view or `ProgressView` |
| **Error** | Load failed | Message + retry action |
| **Search / Filter active** | User is filtering | Filtered results or "no results" state |

Empty state guidance:
- Use a single large SF Symbol (`.font(.system(size: 64))`), muted color.
- One-line headline: what's missing.
- One-line subtext: what the user can do.
- Optional CTA button if there's a direct action to take.

---

## Step 7 — Accessibility

These are non-negotiable baseline requirements.

| Rule | Implementation |
|---|---|
| Dynamic Type support | Use `.body`, `.headline` etc. never fixed sizes. Test at Accessibility Extra Large. |
| Minimum touch target | 44×44pt for all interactive elements. Use `.frame(minWidth: 44, minHeight: 44)` if needed. |
| Color contrast | 4.5:1 minimum for normal text, 3:1 for large text. Never use color as the only indicator. |
| Accessibility labels | All SF Symbol-only buttons need `.accessibilityLabel("Action name")`. |
| Reduce motion | Wrap animations in `withAnimation` conditionally checked against `@Environment(\.accessibilityReduceMotion)`. |
| VoiceOver order | Ensure `accessibilityElement(children:)` grouping makes sense when read sequentially. |

---

## Step 8 — Animation

| Use Case | Duration | Curve | SwiftUI |
|---|---|---|---|
| Micro-interaction (tap, toggle) | 150ms | ease-in-out | `.animation(.easeInOut(duration: 0.15))` |
| Content transition (list appear) | 250ms | ease-out | `.animation(.easeOut(duration: 0.25))` |
| Sheet / modal presentation | System default | — | Let SwiftUI handle it |
| Loading → content | 300ms | ease-in-out | `.transition(.opacity)` |

Always use `transform` / `opacity` animations. Never animate layout properties (frame, size) directly.
Respect `accessibilityReduceMotion` — disable or simplify non-essential animations.

---

## Pre-Delivery Design Checklist

### Visual
- [ ] All screens use the defined color roles — no hardcoded hex values in views
- [ ] Typography uses Dynamic Type styles — no fixed font sizes
- [ ] Spacing is consistent with the defined scale
- [ ] Corner radii are consistent across similar component types
- [ ] All icons are SF Symbols — no emoji used as icons

### Interaction
- [ ] All interactive elements have a minimum 44×44pt touch target
- [ ] Swipe actions, context menus, and confirmations used where appropriate
- [ ] Destructive actions always have a confirmation step

### States
- [ ] Every data-dependent screen has an empty state design
- [ ] Every async screen has a loading state design
- [ ] Every async screen has an error state design

### Accessibility
- [ ] All icon-only buttons have an accessibility label
- [ ] Color is never the only indicator of state (also use shape, label, or icon)
- [ ] Animations are wrapped with reduce-motion guard

### Dark Mode
- [ ] All color roles use semantic system colors where possible
- [ ] No hardcoded white or black
- [ ] Tested visually in both light and dark
