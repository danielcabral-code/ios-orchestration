---
name: design
description: "Guides iOS app UI/UX design with SwiftUI, covering style selection, color systems, typography, spacing, accessibility, interaction patterns, animation, asset generation strategy, and screen-level design decisions. Use when designing or planning iOS app screens, choosing color palettes or typography, establishing a mobile design system, defining SwiftUI interaction patterns, generating image assets with nano-banana, or making UI/UX decisions for an iOS or Swift project."
---

# iOS Design Skill

## Core iOS Design Principles

- Follow Human Interface Guidelines (HIG) as the baseline. Deviate only with clear intent.
- Prefer system components over custom ones. `List`, `Form`, `TabView`, `NavigationStack`, `Sheet`, `Alert`, `Toggle`, `Picker` — use them.
- Use SF Symbols for functional icons. Reserve generated image assets for aesthetic, hero, and brand moments.
- Design for all text sizes. Dynamic Type must never break layouts.
- Design for both light and dark mode unless the app brief explicitly restricts to one.
- Touch targets must be at least 44×44pt.

---

## Asset-First Design Stance

Apps that use custom-generated image assets feel more distinctive, premium, and memorable than apps that rely solely on SF Symbols. **Actively look for every opportunity to place a generated image asset in a screen** — don't ask whether an asset should exist, ask whether there is a good reason NOT to generate one.

### Where to always generate an asset

| Screen / Context | Asset to generate |
|---|---|
| App identity | App icon — always, for every app |
| Onboarding / welcome | Hero illustration — one per step |
| Any screen with an empty state | Empty state illustration (if not System/Minimal style) |
| Launch / splash screen | Splash hero or wordmark |
| Feature paywall or upgrade screen | Feature highlight illustration |
| Home / dashboard hero area | Ambient hero image or branded header graphic |
| Settings profile area | Default avatar or profile placeholder |

### When SF Symbols are sufficient

Use SF Symbols only for:
- Functional, interactive icons (tab bar, toolbar, navigation)
- Inline icons beside labels
- Loading / status indicators
- Destructive or utility actions

If an area is visually prominent, content-rich, or brand-defining — generate an asset.

### Asset philosophy

All generated assets must make the app feel **quietly premium** — never stock-photo, never generic. Flat or softly rendered, with breathing room, using the app's own palette. One strong, well-placed asset beats five mediocre ones.

---

## Step 1 — Style Selection

Choose one primary style that fits the app's purpose. Apply it consistently across all screens.

> **Default aesthetic direction:** Lean toward designs that feel *sleek, modern, and quietly premium*. Clean surfaces, bold typographic hierarchy, a single restrained accent color, and generous negative space. Apps should look memorable and considered — distinctive without ornamentation. When no strong brief pushes otherwise, bias toward **Sleek / Elevated**.

| Style                      | Best For                               | SwiftUI Aesthetic                                           |
| -------------------------- | -------------------------------------- | ----------------------------------------------------------- |
| **Sleek / Elevated**       | Any app meant to feel premium, modern, and memorable | Near-black or white canvas, high-contrast type, single restrained accent, zero decorative chrome |
| **System / HIG-native**    | Utility, productivity, data-heavy apps | Default iOS controls, no decoration                         |
| **Minimal**                | Focus, writing, meditation, finance    | Generous whitespace, muted palette, light typography weight |
| **Warm / Editorial**       | Journaling, recipes, lifestyle         | Serif accents, earthy palette, soft corners                 |
| **Bold / Expressive**      | Fitness, gaming, social                | High contrast, saturated color, strong type                 |
| **Dark / Immersive**       | Media, music, night use                | Dark backgrounds, glowing accents, reduced borders          |
| **Soft / Friendly**        | Children, wellness, onboarding         | Rounded corners, pastel palette, playful type               |
| **Professional / Neutral** | Business, finance, reference           | Cool grays, tight spacing, structured layout                |

**Selection rule:** match the app's primary emotion. A task manager is productive (Sleek / Elevated or System). A mood tracker is personal (Warm or Soft). A workout tracker is energetic (Bold). When the brief doesn't suggest a strong emotional direction, default to **Sleek / Elevated**.

---

## Step 2 — Color System

Define exactly five semantic color roles. Map each to a SwiftUI system color or a specific hex value. Do not define more roles than you use.

| Role          | Purpose                                            | SwiftUI Default                             |
| ------------- | -------------------------------------------------- | ------------------------------------------- |
| `primary`     | Main action color, active state, key UI accent     | `.accentColor` (customise in asset catalog) |
| `secondary`   | Supporting actions, secondary labels, icons        | `.secondary`                                |
| `destructive` | Delete, remove, irreversible actions               | `.red`                                      |
| `background`  | Screen background                                  | `.systemBackground`                         |
| `surface`     | Cards, grouped list backgrounds, sheet backgrounds | `.secondarySystemBackground`                |

### Palette Reference

Pick the palette that matches your chosen style. Hex values are light mode — invert luminance for dark. Use system colors where possible.

**Sleek / Elevated**

- primary: `#0D0D0D` (near-black)
- secondary: `#6B7280` (cool mid-gray)
- accent: `#6366F1` (indigo) — substitute `#0EA5E9` (sky) or `#F59E0B` (amber) when indigo conflicts with the app's domain
- background: `#FFFFFF` (light) / `#0A0A0A` (dark)
- surface: `#F5F5F5` (light) / `#141414` (dark)

Typography note: favor `.semibold` or `.bold` weight for headlines; `.regular` or `.light` for body. Strong typographic contrast is central to this style.

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

- Replace `background` with `.systemBackground` and `surface` with `.secondarySystemBackground`.
- Ensure accent color meets 4.5:1 contrast on both backgrounds.
- Never hardcode white or black — use semantic system colors.

---

## Step 3 — Typography

Always map design roles to Dynamic Type text styles — never use fixed font sizes.

| Design Role | SwiftUI Text Style               | Typical Use                            |
| ----------- | -------------------------------- | -------------------------------------- |
| Large Title | `.largeTitle`                    | Screen hero titles, onboarding         |
| Title       | `.title` / `.title2` / `.title3` | Section headers, card titles           |
| Headline    | `.headline`                      | List row primary label, prominent body |
| Body        | `.body`                          | Default reading text                   |
| Callout     | `.callout`                       | Supplementary body, pull quotes        |
| Subheadline | `.subheadline`                   | Secondary labels, metadata             |
| Footnote    | `.footnote`                      | Captions, legal, timestamps            |
| Caption     | `.caption` / `.caption2`         | Chart labels, image captions           |

### Font Pairing (for expressive styles only)

Default to SF Pro for utility apps. Use custom fonts only when the style calls for it.

| Pairing                   | Style                         | SwiftUI Usage                                |
| ------------------------- | ----------------------------- | -------------------------------------------- |
| SF Pro only               | Neutral, System, Professional | Default — no custom font needed              |
| New York (serif) + SF Pro | Warm, Editorial               | `.font(.system(.title, design: .serif))`     |
| Rounded SF Pro            | Soft, Friendly                | `.font(.system(.body, design: .rounded))`    |
| Monospaced SF Pro         | Technical, Code               | `.font(.system(.body, design: .monospaced))` |

**Rule:** if in doubt, use SF Pro. Never use more than two font designs in one app.

---

## Step 4 — Spacing Scale

Use a consistent spacing scale across all screens. Define three values and stick to them.

| Token    | Value | Use                                            |
| -------- | ----- | ---------------------------------------------- |
| `small`  | 8pt   | Internal component padding, icon-to-label gaps |
| `medium` | 16pt  | Section padding, row insets, card padding      |
| `large`  | 24pt  | Between sections, screen-level vertical rhythm |

For list row insets, `medium` (16pt) matches the iOS system default. Use this unless deliberately overriding.

Corner radius:

- Small controls (tags, chips): 8pt
- Cards and surfaces: 12pt
- Sheets and modals: 16pt (system default)
- Buttons: use `.buttonStyle` defaults or 10-12pt

---

## Step 5 — Interaction Patterns

Use iOS-native patterns. Never invent custom gestures or navigation metaphors when a system equivalent exists.

| Pattern             | When to Use                          | SwiftUI Implementation   |
| ------------------- | ------------------------------------ | ------------------------ |
| Swipe to delete     | Destructive row action               | `.onDelete` on `ForEach` |
| Swipe actions       | Non-destructive row actions          | `.swipeActions`          |
| Pull to refresh     | Refreshable list                     | `.refreshable`           |
| Context menu        | Secondary actions on long press      | `.contextMenu`           |
| Sheet               | Focused sub-task, creation flow      | `.sheet`                 |
| Full-screen cover   | Immersive flow (onboarding, media)   | `.fullScreenCover`       |
| Confirmation dialog | Irreversible destructive action      | `.confirmationDialog`    |
| Alert               | Short error or status message        | `.alert`                 |
| Navigation push     | Moving deeper into a hierarchy       | `NavigationLink`         |
| Tab switching       | Switching between top-level sections | `TabView`                |

**Rule:** use `.sheet` for creation/editing tasks that return to the same context. Use `NavigationLink` push for drilling deeper into content.

---

## Step 6 — UI States per Screen

Every data-dependent screen must have a design for each of these states:

| State                      | When                    | What to Show                                             |
| -------------------------- | ----------------------- | -------------------------------------------------------- |
| **Default**                | Data loaded, normal use | Primary content                                          |
| **Empty**                  | No data yet             | Illustration or SF Symbol + short message + optional CTA |
| **Loading**                | Async fetch in progress | Skeleton view or `ProgressView`                          |
| **Error**                  | Load failed             | Message + retry action                                   |
| **Search / Filter active** | User is filtering       | Filtered results or "no results" state                   |

Empty state guidance:

- Use a single large SF Symbol (`.font(.system(size: 64))`), muted color.
- One-line headline: what's missing.
- One-line subtext: what the user can do.
- Optional CTA button if there's a direct action to take.

---

## Step 7 — Accessibility

These are non-negotiable baseline requirements.

| Rule                 | Implementation                                                                                                |
| -------------------- | ------------------------------------------------------------------------------------------------------------- |
| Dynamic Type support | Use `.body`, `.headline` etc. never fixed sizes. Test at Accessibility Extra Large.                           |
| Minimum touch target | 44×44pt for all interactive elements. Use `.frame(minWidth: 44, minHeight: 44)` if needed.                    |
| Color contrast       | 4.5:1 minimum for normal text, 3:1 for large text. Never use color as the only indicator.                     |
| Accessibility labels | All SF Symbol-only buttons need `.accessibilityLabel("Action name")`.                                         |
| Reduce motion        | Wrap animations in `withAnimation` conditionally checked against `@Environment(\.accessibilityReduceMotion)`. |
| VoiceOver order      | Ensure `accessibilityElement(children:)` grouping makes sense when read sequentially.                         |

---

## Step 8 — Animation

| Use Case                         | Duration       | Curve       | SwiftUI                                  |
| -------------------------------- | -------------- | ----------- | ---------------------------------------- |
| Micro-interaction (tap, toggle)  | 150ms          | ease-in-out | `.animation(.easeInOut(duration: 0.15))` |
| Content transition (list appear) | 250ms          | ease-out    | `.animation(.easeOut(duration: 0.25))`   |
| Sheet / modal presentation       | System default | —           | Let SwiftUI handle it                    |
| Loading → content                | 300ms          | ease-in-out | `.transition(.opacity)`                  |

Always animate `transform` / `opacity`. Never animate layout properties (frame, size) directly.
Respect `accessibilityReduceMotion` — disable or simplify non-essential animations.

---

## Step 9 — nano-banana Asset Generation

All image assets are generated with the `mcp_nano-banana_generate_image` tool. The prompt is the only parameter — size, quality, and style are all controlled through prompt wording.

### Mandatory size presets

These sizes are fixed. Never generate larger — doing so increases Gemini cost without meaningful quality gain at mobile display densities.

| Asset type | Size in prompt | Notes |
|---|---|---|
| App icon | `1024x1024` | Required by App Store |
| Onboarding hero | `512x512` | One per onboarding step |
| Launch / splash hero | `512x512` | — |
| Empty state illustration | `256x256` | Keep composition simple |
| In-app content image (square) | `256x256` | — |
| In-app content image (wide) | `512x256` | — |
| 3D / clay render asset | `512x512` | Max size for 3D-style |

Always append the size (`NxN`) and `, PNG format` to every prompt. Append `, transparent background` for icons and illustrations intended for compositing.

### Quality baked into prompts

There is no separate quality parameter. Instead, every asset prompt must include these descriptors to produce a clean, premium result on the first attempt:

- `flat illustration style` or `soft 3D clay render` — never photorealistic
- `generous negative space, minimal composition` — compositions that breathe
- `[1-2 colors from the app's visual language] palette` — palette-matched
- `no text, no badges, no borders` — unless a wordmark is explicitly required
- `PNG format` + size from the table above

Producing a clean result on the first attempt is the primary cost-control measure — every regeneration costs an additional Gemini call. **Write the prompt carefully and completely before generating.** If a result is poor, use `mcp_nano-banana_continue_editing` once to refine before starting over.

### Asset save path convention

```
<ProjectRoot>/DesignAssets/<category>/<filename>.png
```

| Asset type | Category folder |
|---|---|
| App icon | `AppIcon` |
| Launch / splash | `Launch` |
| Empty state illustration | `EmptyStates` |
| Onboarding hero | `Onboarding` |
| In-app content image | `Content` |
| 3D / clay render | `3D` |

### Placeholder naming rule

The filename (without extension) of a generated asset **must exactly match** the placeholder name used in the Stitch screen prompt. This 1:1 mapping ensures the Developer can connect every Stitch placeholder to the correct file without ambiguity.

Example:
- Asset saved as: `DesignAssets/Onboarding/welcome-hero.png`
- Stitch placeholder text: `[ASSET PLACEHOLDER: welcome-hero]`

Never use IDs (e.g., `A-01`) as the canonical identifier. The filename is the identifier.

---

### Prompt Templates by Asset Type

Use these as fill-in-the-blank starting points. Replace bracketed placeholders with app-specific values. Always append size and format from the mandatory preset table.

**App Icon**
> `Flat vector app icon of [subject or metaphor], [primary accent color] and [secondary color] palette, centered on [background color] background, bold simple silhouette, no text, no badge, minimal modern style, 1024x1024, PNG format`

Tip: pick a single bold metaphor — a shape, object, or glyph. Avoid compositions with more than two elements.

**Onboarding Hero**
> `Flat illustration of [scene or concept representing the onboarding step], [color 1] and [color 2] palette, generous negative space, soft line weights, centered subject, no text, no borders, friendly and airy feel, 512x512, PNG format, transparent background`

Tip: each step should have a distinct subject — avoid reusing the same visual metaphor across steps.

**Launch / Splash Hero**
> `Flat illustration of [app name metaphor or primary value proposition], [primary color] palette with [accent color] highlight, minimal composition, single centered element, no text, 512x512, PNG format, transparent background`

**Empty State Illustration**
> `Flat illustration of [object or scene that reflects the empty context — e.g. 'an empty inbox tray', 'a blank map with a pin'], [muted color from visual language] palette, light and airy, simple shapes, no text, generous whitespace, 256x256, PNG format, transparent background`

Tip: the image should visually suggest the *absence* of content — open space, a single waiting object — not a busy scene.

**In-App Content Image (square)**
> `Flat illustration of [specific content topic], [palette], minimal composition, soft shadows, no text, no UI chrome, 256x256, PNG format`

**In-App Content Image (wide)**
> `Flat illustration of [specific content topic], [palette], wide panoramic composition with breathing room on both sides, no text, 512x256, PNG format`

**3D / Clay Render (icon or hero)**
> `Soft 3D clay render of [subject], [color] palette, matte finish, centered, subtle ambient occlusion shadow, floating on white background, no text, minimal modern style, 512x512, PNG format`

---

### Common Failure Modes — What to Avoid

| Problem | Cause | Fix |
|---|---|---|
| Photorealistic output | Missing style keyword | Always include `flat illustration style` or `soft 3D clay render` |
| Cluttered, busy composition | Missing space keyword | Add `generous negative space, minimal composition` |
| Off-palette colors | No palette constraint | Explicitly name 1-2 colors; never just say "colorful" |
| Text or labels appear in asset | Missing no-text keyword | Always include `no text, no badges, no decorative borders` |
| Asset looks stock-photo generic | Subject too vague | Be specific: `a single minimalist hourglass` not `a time concept` |
| Icon has harsh white border | Missing background instruction | Add `transparent background` or `floating on [color] background` |
| 3D result looks plastic / shiny | Missing surface keyword | Add `matte finish` and `no specular highlights` |

---

## Step 10 — 3D Assets

Use 3D visual assets selectively to add tactile depth and a premium feel. Overuse breaks visual consistency — apply only where it meaningfully enhances the experience.

### When to use 3D assets

- App icon: appropriate for Warm, Soft, Bold, and Dark styles. Skip for System, Minimal, or Professional.
- Hero / onboarding images: one 3D-style illustration as a focal point per step. One asset per step maximum.
- Empty state focal object: when an SF Symbol is insufficient, a 3D-rendered object adds character. One per screen maximum.
- Feature highlights / paywall screens: a 3D object representing the feature is appropriate.

### When NOT to use 3D assets

- Do not use 3D assets in list rows, navigation bars, tab bars, or form controls.
- Do not use 3D for supporting icons or functional UI elements — SF Symbols only.
- Do not apply 3D styling to more than one element per screen.
- System, Minimal, and Professional style apps should not use 3D assets at all.

### iOS native 3D options (no generation needed)

| Option                               | When to use                                          | Implementation note                                                                                                         |
| ------------------------------------ | ---------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| SF Symbols multicolor / hierarchical | Decorative icons with depth layers                   | `.symbolRenderingMode(.hierarchical)` or `.multicolor`                                                                      |
| SF Symbols variable color            | Icons that communicate quantity or progress          | `.symbolVariants` + variable value                                                                                          |
| SceneKit / RealityKit view           | Interactive 3D object the user can rotate or examine | `SceneView` or `RealityView` — note in Design Spec as a Developer concern, specify the expected model file format (`.usdz`) |

Prefer native iOS options when they satisfy the need. Generate with nano-banana only when a custom 3D-style illustration adds value that SF Symbols cannot provide.

### Generating 3D-style assets with nano-banana

Use a soft 3D / clay render prompt style. All aesthetic principles from Image Asset Generation apply — add these 3D-specific descriptors:

- `soft 3D render` or `clay render` — matte, tactile surface without specular glare
- `isometric` or `front-facing` depending on composition
- `subtle ambient occlusion shadow` — grounding shadow only, not dramatic
- `pastel or muted palette` — avoid saturated plastic-looking colors
- `no background` or `floating on white` — keeps the asset composable
- `single object, centered` — one focal element per asset

Example prompt structure: _"Soft 3D clay render of [subject], [color from visual language] palette, matte finish, centered on white background, no text, subtle ambient shadow, minimal modern style"_

---

## Pre-Delivery Design Checklist

### Visual

- [ ] All screens use the defined color roles — no hardcoded hex values in views
- [ ] Typography uses Dynamic Type styles — no fixed font sizes
- [ ] Spacing is consistent with the defined scale
- [ ] Corner radii are consistent across similar component types
- [ ] All functional icons are SF Symbols — no emoji used as icons

### Assets

- [ ] App icon generated and saved to `DesignAssets/AppIcon/`
- [ ] Every onboarding screen has a generated hero asset
- [ ] Every generated asset filename matches its Stitch placeholder name exactly
- [ ] No asset generated larger than the allowed preset size for its type
- [ ] All assets saved to the correct `DesignAssets/<category>/` folder
- [ ] Any failed assets have a documented SF Symbol fallback

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
