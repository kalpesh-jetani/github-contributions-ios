# Decisions

Settled choices. **Read this before asking a CLAUDE.md §0 question** — it may already be answered.
Add a row when a §0 decision is made; never remove one.

## Settled — follow these, don't re-derive

| Scope | Decision | Why | Detail |
|---|---|---|---|
| §2.2 | **Adopted** — no third-party type crosses a module boundary | Already true: SwiftSoup is confined to `NetworkKit/GitHub.swift` and the public API returns `GitHub.Contribution` | Kingfisher is imported unwrapped at `Contributions/Guide/StepView.swift:33` — owed work, see GAPS.md |
| §2.3 | **Adopted, new code only** — Literal strings in Text | All 10 sites are already `LocalizedStringKey` with `es`/`ja` translated; renaming shipped keys breaks translator continuity for no functional gain | Boundary: existing kebab keys (`contributions-empty-title`) keep their names; **new** keys follow the same kebab scheme. Never rename a shipped key |
| §2.5 | **Adopted, new code only** — every content state, one state value per screen | Only two data-driven surfaces exist, so this costs nothing now and shapes what comes next | Boundary: all new screens. The existing list and widget are grandfathered |
| §2.6 | **Adopted** — every dependency injected via protocol, no `.shared` | Already true: `ContributionsStorage` is a protocol with constructor injection | Carve-out: Apple's own **Singletons** (`UIApplication.shared`, `UserDefaults.standard`) remain allowed |
| §2.7 | **Adopted fully, with migration** — no `try?` that swallows, no force-unwrap in shipping paths | The rule most likely to catch a real defect: a swallowed JSON decode error is invisible at runtime | **9 sites owed** — 3 `try?` + 6 force-unwraps. Not done at init; tracked in GAPS.md |
| §2.8, §2.10 | **Adopted** — no unjustified `@unchecked Sendable`; no `#if DEBUG` in a feature | Zero occurrences today and both are grep-checkable, so adoption is free | §2.10's `AppEnvironment` half does not apply — this repo has no `.xcconfig` files |
| §2.9, §2.11–§2.16 | **Adopted** — process and tooling rules | No code impact; they govern how Claude works here | #11 never commit/push · #12 build freely, ask before run/test · #15 removals via `ga-remove.sh` · #16 four component `CLAUDE.md` files written |
| §1.1 | **Declared platform floors** — converge the app on iOS **16.0** | Packages declare `.iOS(.v16)` while the app declares 16.6; the existing `#available(iOS 17)` shims already cover pre-17, so lowering is low-risk | **The edit was NOT applied at init.** Mutating build settings is a separate act of consent — hand to `/upgrade-stack`, which asks twice |
| Architecture layer | **Installed** via `bootstrap.sh --with-architecture` | Most of §2 was adopted, so `/review` has real rules to check against | Added `.claude/skills/new-feature/SKILL.md` and `.claude/commands/review.md`. Caveat: `new-feature` scaffolds into `Packages/Features`, which this repo does not use — adapt its output to the top-level package layout |
| Permissions | `.claude/settings.json` created with **read-only git only** | Only group approved at init | `git status/diff/log/show`. Swift build/test, Xcode build/test/archive, and mutating git were all declined. Consequence: builds still prompt despite §2.12 |
| Layers (S2) | **Nothing scaffolded** — the layers this product needs already exist | Derived from the code, not defaulted: design system = `InterfaceKit`, networking = `NetworkKit`, storage = `ContributionsStorage` (actor over UserDefaults), image caching = Kingfisher. Adding a `Packages/` tree beside the existing top-level packages would create a competing structure | Skipped as unreached by the requirements: DI container (nothing to inject beyond one storage protocol), routing (no deep links, shallow navigation), message presentation (§2.4 Open), logging (no shipping logs), notifications (none). Revisit any of these when its first consumer appears |
| Doc scope | Root `CLAUDE.md` carries §0, §1, §2 and §5 only | Keeps the always-loaded file small; §3, §4 and §6–§12 stay fetch-on-demand | Recorded in CLAUDE.md *Sections not carried here* |

## Ask every time — never assume

Presentation pattern (per feature/screen) · persistence engine (only if data is stored) · caching
and offline policy (any remote fetch) · any new external dependency · extracting a package.
Options and phrasing: CLAUDE.md §0. Record the answer here with `/decide`.

## Do not re-propose

Rejected with reasons recorded — reopen only with new information, not a fresh preference.

| Rejected | Where the reasoning lives |
|---|---|
| Rewriting the **Completion handlers** in `ContributionsWidget/ContributionsProvider.swift` as `async` | Both sites are `IntentTimelineProvider` protocol requirements WidgetKit demands, and `getTimeline` already wraps `Task { await … }`. CLAUDE.md §1 concurrency carve-out and `ContributionsWidget/CLAUDE.md` |
| Applying §2.6 to Apple's own **Singletons** | All three flagged references are `UIApplication.shared`. CLAUDE.md §2.6 carve-out |
| Adopting §3, §4 and §6–§12 into this repo's `CLAUDE.md` | Not adopted; nothing in them binds this repo. `/review` must not report violations of an unadopted rule. CLAUDE.md *Sections not carried here* |

Skipped capabilities — triaged by `/gaps` on 2026-09-08, Path A (derived from the code, not asked).
Each has a ⛔ row in [GAPS.md](GAPS.md) carrying the same evidence.

| Rejected | Where the reasoning lives |
|---|---|
| Analytics event taxonomy | No analytics SDK linked and no events sent. GAPS.md ⛔ |
| Auth flows | The app reads public GitHub contribution data; there is no account or login. GAPS.md ⛔ |
| IAP / StoreKit | No StoreKit usage — free app, no purchases. GAPS.md ⛔ |
| CloudKit sync | Usernames persist to `UserDefaults` via `ContributionsStorage`; nothing syncs across devices. GAPS.md ⛔ |
| Universal links / AASA | No deep links and no URL routing, so there is no AASA file to be missing. GAPS.md ⛔ |
| Keyboard / focus management | No form — the only `TextField` is inside a system `.alert`, which manages its own focus. GAPS.md ⛔ |
| Search | No search interface or `CoreSpotlight` usage. GAPS.md ⛔ |
| Haptics | No `UIFeedbackGenerator` or `sensoryFeedback` usage. GAPS.md ⛔ |
| Biometrics | No `LocalAuthentication`; nothing to protect behind it. GAPS.md ⛔ |
| Push notifications | No `UNUserNotificationCenter` and no push entitlement. GAPS.md ⛔ |
| Handoff | `detect-capabilities.sh` reports FOUND from `Info.plist`'s `NSUserActivityTypes`, but that array holds only `ConfigurationIntent` — the widget's intent configuration. No `NSUserActivity` usage exists. **The scan will keep reporting FOUND; this row is the answer.** GAPS.md ⛔ |

## Open

| Question | Blocks | Note |
|---|---|---|
| §2.4 — route **Direct alerts / sheets** through one `MessagePresenting` | Nothing today | One `.alert` (`Contributions/List/ContributionsList.swift:68`, a bound `TextField` prompt) and one `.sheet` (navigation, not a message). Deferred at init — an abstraction for a single call site was not justified |
| Testing framework — Swift Testing, XCTest, or both | `/verify` and the Definition of Done have no test signal until answered | There are no test targets in the project at all (app + widget extension only) |

---

## Per feature

| Date | Feature | Presentation | Persistence | Caching / offline |
|---|---|---|---|---|
| — | — | — | — | — |
