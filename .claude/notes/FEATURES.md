# Features & Screens

Inventory of every feature area and the screens it owns. Routes live in [NAVIGATION.md](NAVIGATION.md).

**Read it when** deciding where a screen belongs, checking whether a screen already exists, or
tracing which area owns a view model.

- **Last synced:** 2026-09-08
- **Tree hash:** `a86adb6595b7c787`
- **Paths are repo-relative** — there is no single source root: this is a flat app target plus a
  widget extension and two local packages.
- **Convention:** `file = <dir>/<Key>.swift`; a view model is `<dir>/<Key>ViewModel.swift`.

---

## Features

**There are no feature packages.** This app predates `Packages/Features` and has none, which is why
CLAUDE.md §2.1 is recorded *Not applicable*. The rows below are folder-scoped areas inside one module.

| Feature | Path — *what's there* | Screens | Route cases | Presentation | Skill |
|---|---|---|---|---|---|
| List | Contributions/List/ — main screen, rows, storage | `ContributionsList` | none — tab root | tab 1 of `TabView` | — |
| Settings | Contributions/Settings/ — settings list, row model | `SettingsView` | none — tab root | tab 2 of `TabView` | — |
| Guide | Contributions/Guide/ — paged widget-setup walkthrough | `GuideView` | none — sheet | `.sheet` from `SettingsView` | — |
| Widget | ContributionsWidget/ — timeline provider, widget views | `ContributionsGraphEntryView` | none — WidgetKit | `WidgetBundle` entry | — |

Layers, not features: InterfaceKit/ (design system, zero dependencies) · NetworkKit/ (GitHub fetch).
Each carries its own `CLAUDE.md`.

## Screens

One row per screen. `States` records which content states the screen actually renders — a screen
missing one is a CLAUDE.md §2.5 concern, and this table is where that shows up. §2.5 was adopted
**new code only**, so the gaps below are grandfathered, not violations.

| Screen | View dir | View model | States implemented | L10n prefix |
|---|---|---|---|---|
| `ContributionsList` | Contributions/List/ | ContributionsListViewModel.swift | empty, loaded | `contributions-` |
| `SettingsView` | Contributions/Settings/ | SettingsViewModel.swift | loaded | `settings-`, `setting-about`, `app-version-%@` |
| `GuideView` | Contributions/Guide/ | none — `Step` enum supplies content | loaded | `widget-guide-` |
| `ContributionsGraphEntryView` | ContributionsWidget/ | ContributionsViewModel.swift | error, loaded | `contributions-`, `widget-graph-` |

## Shared components

Not screens, but greppable here for the same reason.

| Component | Dir | Owner — *what it renders* |
|---|---|---|
| `ContributionsRow` | Contributions/List/ | List — one user's graph plus username |
| `SettingsRow` | Contributions/Settings/ | Settings — one row |
| `StepView` | Contributions/Guide/ | Guide — one page of the walkthrough |
| `ContributionsView` | InterfaceKit/Sources/InterfaceKit/Views/ | InterfaceKit — the contribution grid itself |
| `GridStack` | InterfaceKit/Sources/InterfaceKit/Views/ | InterfaceKit — generic grid, `struct GridStack<Content: View>` |
| `ContributionsGraphWidgetView` | ContributionsWidget/ | Widget — graph plus empty text |

## Using this as an index

```bash
# a screen → its dir, its view model, and its states
grep -n "ContributionsList" .claude/notes/FEATURES.md

# every screen against the states it implements — the missing-state audit, without opening Xcode
awk -F'|' 'NF>5 {print $2, "→", $5}' .claude/notes/FEATURES.md

# which area owns a component
grep -n "GridStack" .claude/notes/FEATURES.md
```

Cross-reference with [NAVIGATION.md](NAVIGATION.md): a screen here with no route there is
unreachable, and a route there with no screen here is dead. **In this repo no routes exist at all**,
so that cross-check is vacuous until a route layer is introduced.

A path in this file that does not exist on disk means the note is stale — that is a `Gaps` row.

## Orphans & gaps

Things `/sync-app-notes` flags rather than silently omitting:

| Finding | Where | Noted |
|---|---|---|
| No screen implements a distinct `loading` state | all four screens | `contributions-loading` is passed as `ContributionsRow`'s **emptyText**, so "loading" and "empty" render identically |
| A failed fetch is indistinguishable from no data | Contributions/List/ContributionsRowViewModel.swift | `try? await GitHub.getContributions` falls back to an empty `Contributions()`, so a network error renders as an empty graph. The §2.7 debt and the §2.5 missing error state are the same line of code |
| No screen implements `offline` | all four screens | no reachability check anywhere in the repo |
| No `Route` cases exist | — | navigation is structural: `TabView` + `NavigationStack` + one `.sheet`. See [NAVIGATION.md](NAVIGATION.md) |
| No feature carries a `Feature<Name>.md` | — | there are no feature packages; the four component `CLAUDE.md` files carry the boundaries instead |

**Not checked:** whether each screen's states are *correct* for its data source (the scan reads which
states are rendered, not which are needed); whether `GuideView`'s seven steps are all reachable; and
anything about iPad or landscape layout, which no scan can see.
