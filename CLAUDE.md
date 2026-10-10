# CLAUDE.md — GitHubContributions

Rules for building this app. Adopted from GenericArch v0.6.2 on 2026-09-08; every rule below was
individually approved. Numbering follows GenericArch §2 so skills and `/review` cite it correctly.

---

## 0. Decisions Claude must ASK about, never assume

Presentation pattern (per feature/screen) · persistence engine · caching and offline policy · any
new external dependency · extracting a package. **Read [DECISIONS.md](docs/DECISIONS.md) first —
it may already be answered.** Record answers with `/decide`.

---

## 1. Stack

| Item | Value | Source |
|---|---|---|
| Minimum iOS / iPadOS | **16.6** | project — converging on 16.0, see DECISIONS.md |
| Minimum macOS | n/a — iOS only (app + widget extension) | project |
| Xcode / Swift | 26.6 / 6.3.3 | machine |
| Swift language mode | **6** | project (`SWIFT_VERSION = 6.0`) |
| UI | SwiftUI | project |
| Dependencies | SPM | project |
| Project files | checked-in `.xcodeproj` — **no `.xcconfig` files** | project |
| Concurrency | async/await | project |
| Testing | **unresolved** — no test targets exist | DECISIONS.md *Open* |

Local packages: `InterfaceKit` (design system, zero dependencies) · `NetworkKit` (GitHub fetching,
depends on SwiftSoup). Vendors: SwiftSoup 2.13.4, Kingfisher 8.8.1.

**Concurrency carve-out:** the `completion:` signatures in `ContributionsWidget/ContributionsProvider.swift`
are `IntentTimelineProvider` protocol requirements, not a style choice. Do not "modernize" them.

---

## 2. The rules that must never be broken

1. *(Not applicable)* No package imports a sibling feature — there is no `Packages/Features`;
   `Guide/`, `Settings/`, `List/` are folders in one module. Applies if features are ever extracted.
2. **No third-party type crosses a module boundary.** `NetworkKit` returns `GitHub.Contribution`,
   never a SwiftSoup type. Kingfisher's owed wrapper is in GAPS.md.
3. **No hardcoded user-facing string** — localized key, always. *New code only:* existing keys keep
   their kebab scheme (`contributions-empty-title`) and new keys follow it. Never rename a shipped
   key — `es` and `ja` are translated.
4. *(Open — see DECISIONS.md)* One message presenter. The single `.alert` site stays as-is for now.
5. **Every data-driven screen handles every content state** — loading, empty, offline, error,
   loaded. **One** state value per screen, never a boolean beside it. *New code only.*
6. **Every dependency injected via protocol.** No `.shared` from inside a feature type.
   Carve-out: Apple's own singletons (`UIApplication.shared`, `UserDefaults.standard`) are fine.
7. **No `try?` that swallows an error**, no `fatalError` in a shipping path, no force-unwrap outside
   tests. *Adopted fully* — 9 pre-existing sites are owed work, see GAPS.md.
8. **No `@unchecked Sendable`** without a comment justifying the manual synchronization.
9. **No silent architectural choice.** If it's in §0, ask.
10. **No `#if DEBUG` or build-flag branching in a feature.**
11. **Never `commit` or `push`** — not to "save progress", not because the work looks finished.
    Leave it in the working tree and say what changed.
12. **Build to validate on your own initiative; ask before you run or test.** Prefer
    `./Scripts/check.sh` and report what it printed. Running and testing need consent.
13. **Follow the matching skill and name it before starting.** If none fits, say so.
14. **Stop on a vague instruction.** Ask for a reference, a focused goal, or which reading —
    never pick silently, never ship a "safe subset".
15. **Never delete an installed file with `rm`.** Use `./Scripts/ga-remove.sh <path> --reason "…"`.
    Any command that rewrites installed files closes with `./Scripts/ga-reseal.sh --apply`.
16. **Every component carries its own `CLAUDE.md`**, written before the code, opening with its
    boundary (owns · may depend on · never imports).

---

## 5. Index

Grep `.claude/MAP.tsv` to find any doc, note, skill or pattern. Architecture reference is fetched
on demand — a `docs/…` path that is not on disk is a fetch instruction, not a broken link; the
map's `# FETCH-BASE:` line says where to get it, and `genericarch.installation.md` is the full index.

---

## Sections not carried here

§3, §4, §6–§12 of the GenericArch base were not adopted into this file. A skill citing one means the
base `CLAUDE.md` — fetch it via the `# FETCH-BASE:` in `.claude/MAP.tsv`. **Nothing in them binds
this repo unless it appears in §2 above** — `/review` must not report violations of an unadopted rule.
