# Style Guide

Every design token and component variant that exists, so nothing gets invented twice.

**Read it when** building or restyling any view — **before** writing a value.
- **Companions:** [ASSETS-COLORS.md](ASSETS-COLORS.md) (colour tokens + hex),
  [FONTS.md](FONTS.md) (families, registration, type tokens). This file owns everything else:
  spacing, radius, elevation, motion, and the component inventory.
- **Last synced:** 2026-09-08

> **Check here before adding a value.** A near-duplicate token is worse than a slightly wrong reuse:
> two radii four points apart will never be reconciled, and every screen after them inherits the
> ambiguity. If nothing here fits, that is a decision to bring to the user — not a value to invent.

## ⚠ There is no token registry in this repo

**`grep "static let spacing\|radius\|duration\|elevation"` returns zero declarations.** There is no
`DesignSystem/Tokens/` and no equivalent — every spacing, radius and duration in this codebase is an
inline literal at its call site.

Per the sync rules a missing source is **recorded, not synthesised**: the token tables below stay
empty on purpose. Inventing tokens nothing references would be worse than an empty registry, because
the next reader would believe they were in use. The literals actually found are in `Gaps` as evidence.

`InterfaceKit/` is the closest thing to a design-system layer and owns colours plus two view
modifiers; it declares no numeric tokens.

## This file is an index, not the detail

Every row points somewhere else. **Prefer the code** — a token's real definition is the file that
declares it, and a description of implemented style is the same stale-prose problem as narrating a
function. Only when a style is agreed but not yet code, or spans files a single path cannot capture,
does it get its own note under `styles/`.

| Detail lives in | Use when | Example |
|---|---|---|
| — none yet | — | — |

A `styles/` note that describes shipped code should be **deleted and replaced with the path**. It
exists to hold what code cannot yet answer, not to duplicate what it already does.

---

## Spacing

The only permitted spacing values. A view needing something else needs a **token added here**, agreed
with the user — not a literal at the call site.

| Token | Value | Use for | Detail — *code path, or `styles/…`* |
|---|---|---|---|
| — none declared | — | — | see `Gaps` for the literals in use |

## Radius

| Token | Value | Use for | Detail — *code path, or `styles/…`* |
|---|---|---|---|
| — none declared | — | — | `cornerRadius: 6` and `16` are inline; see `Gaps` |

## Elevation

**Dark mode is why this is a token and not a shadow.** Shadows are invisible on black, so elevation
is carried by surface colour; the shadow is a light-appearance affordance layered on top.

| Level | Surface token | Shadow (light only) | Use for | Declared in — *what's there* |
|---|---|---|---|---|
| — none | — | — | — | no shadows or elevation anywhere in the repo |

## Motion

Every duration and curve. All of them collapse to instant under **Reduce Motion** — that is a
property of the token, not something each call site remembers.

| Token | Duration | Curve | Use for | Declared in — *what's there* |
|---|---|---|---|---|
| — none declared | system default | `.default` | list insert/remove | Contributions/List/ — `.animation(.default, value:)` |
| — none declared | system default | `.snappy.speed(2)` | empty-view transition | Contributions/List/ — scale + opacity transition |

**Neither honours Reduce Motion** — see `Gaps`.

## Typography scale

Families and registration live in [FONTS.md](FONTS.md) (this repo bundles no font files). This table
is the **semantic scale** — which token to reach for, and what it maps to.

| Token | Role | Relative to | Declared in — *what's there* |
|---|---|---|---|
| `.headline` | row titles, emphasis | system Dynamic Type | used at 3 sites |
| `.callout` | secondary emphasis | system Dynamic Type | used at 2 sites |
| `.body` · `.subheadline` · `.footnote` | body and supporting text | system Dynamic Type | 1 site each |
| `TextCaption` | caption modifier | system Dynamic Type | InterfaceKit/Sources/InterfaceKit/Modifiers/ |

**This is the good news in this note:** type is entirely system semantic styles, so Dynamic Type
works without custom scaling. No custom font tokens are needed or missing.

## Components

The inventory. **Extend an existing component before adding a near-duplicate** — two that differ by a
corner radius are one component and a parameter.

| Component | Variants | States covered | Detail — *code path, or `styles/…`* |
|---|---|---|---|
| `Tile` | `cornerRadius` param (default 2) | widget accented via `widgetRenderingMode` | InterfaceKit/Sources/InterfaceKit/Modifiers/ — applied as `.tileStyle()` |
| `TextCaption` | none | — | InterfaceKit/Sources/InterfaceKit/Modifiers/ |
| `WidgetStyle` | none | — | ContributionsWidget/ — declared inside ContributionsGraphEntryView.swift |
| `ContributionsView` | theme via colour set | empty via `emptyText` | InterfaceKit/Sources/InterfaceKit/Views/ |
| `GridStack` | generic `<Content: View>` | — | InterfaceKit/Sources/InterfaceKit/Views/ |

## Using this as an index

```bash
# does a token for this already exist? — run BEFORE writing any value
grep -in "radius\|spacing\|elevation" .claude/notes/STYLE-GUIDE.md

# every component and the file that defines it
grep -n "InterfaceKit/Sources" .claude/notes/STYLE-GUIDE.md

# the literals currently in use, when looking for the nearest existing value
grep -nE 'spacing [0-9]+|cornerRadius' .claude/notes/STYLE-GUIDE.md
```

## Open style questions

Style the user has not yet decided. **Do not invent an answer to close a row here** — an unanswered
question is honest; a silently chosen value becomes precedent.

| Question | Blocked work | Asked on |
|---|---|---|
| Should a token layer exist at all? At 4 screens the literals may be cheaper than the registry | any restyle touching more than one screen | not yet asked |

## Gaps

| Finding | Where | Noted |
|---|---|---|
| No token declarations exist | repo-wide | `static let spacing/radius/cornerRadius/duration/elevation` returns zero hits |
| Repeated spacing literals — token candidates | Contributions/, InterfaceKit/ | `spacing: 6` ×3 · `spacing: 16` ×2. Also one-offs: `spacing: 2`, `12`, `32`; `padding(6)`, `padding(16)`; `cornerRadius: 6`, `16`; `lineWidth: 1`; `frame(width: 12)`, `frame(width: 17)` |
| Near-duplicate candidates | — | `cornerRadius: 6` vs `padding(6)` vs `spacing: 6` share a value but not a meaning — reconciling them needs intent, not a scan |
| Neither motion site honours Reduce Motion | Contributions/List/ | `accessibilityReduceMotion` appears **nowhere** in the repo; both animations run regardless |
| **No component has a preview** | repo-wide | `#Preview` and `PreviewProvider` return zero hits, despite a `Contributions/Preview Content/` catalog existing. Every component is unpreviewable, so no variant matrix can be checked |
| No accessibility annotations anywhere | repo-wide | zero `accessibilityLabel`, `accessibilityHint`, `dynamicTypeSize`. SwiftUI derives labels from `Text`, so this is a **candidate**, not a verdict — but `Image(systemName:)` call sites get no label |

**Not checked:** whether any spacing literal is visually wrong (a scan reads values, not designs);
contrast ratios (that is [ASSETS-COLORS.md](ASSETS-COLORS.md)); whether the two motion sites
*should* collapse under Reduce Motion or are decorative enough to keep; and iPad or landscape
layout, which no scan can see.
