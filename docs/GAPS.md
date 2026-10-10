# Gaps

What this architecture does not cover **for this product**, as decisions to make rather than a
backlog to burn down. Run `/gaps` to triage.

`/gaps` behaves differently by repo state: on an existing repo it derives each status from the code
without asking; on a fresh one it asks per item. Absence of evidence is not always a decision — no
StoreKit means the product doesn't monetise, but no crash reporting is a **missing safeguard**,
reported as a risk rather than silently skipped.

## Status legend

| Status | Meaning | Gets re-raised? |
|---|---|---|
| ✅ **Applied** | Landed. The row records where | No |
| ▶ **Open** | Needs a decision | Yes, by `/gaps` |
| ⏸ **Deferred** | Tracked, with a named revisit trigger | Only when the trigger fires |
| ⛔ **Skipped** | Decided against. Also recorded in DECISIONS.md *Do not re-propose* | **No** |

**Skip is a real answer.** Most capabilities should end up Skipped for any given product.

Triaged by `/gaps` on 2026-09-08 (Path A — every status below was derived from the code, not asked).
Evidence came from `./Scripts/detect-capabilities.sh`, with each `FOUND` opened and verified.

## ▶ Open — risks found

Absence here is a **missing safeguard**, not a product decision. These are reported, not Skipped.

| Item | Cost of skipping | Status |
|---|---|---|
| Feature flags / remote config | No kill switch. **Sharpened by this product's design:** contributions are scraped from GitHub HTML via SwiftSoup (`NetworkKit/GitHub.swift`). If GitHub changes its markup, every installed copy breaks at once and the only remedy is an App Store release | ▶ |
| Crash reporting + dSYM upload | Crash reports unreadable, unrecoverable after the fact. No crash SDK is linked, and `fastlane/Fastfile` has no `upload_symbols` step to add one to | ▶ |
| SwiftLint / SwiftFormat config | Rules stay review-only and decay. **Now concrete:** CLAUDE.md §2 carries 14 adopted rules and several are mechanically checkable (§2.7 `try?`/force-unwrap, §2.8 `@unchecked Sendable`, §2.10 `#if DEBUG`) — nothing enforces any of them. No `.swiftlint.yml` or `.swiftformat` anywhere | ▶ |
| Any automated tests (unit or UI) | A regression reaches users with no signal. The project has **no test targets at all** — app and widget extension only | ▶ — the framework choice is the *Open* row in [DECISIONS.md](DECISIONS.md); this row is the coverage gap itself |

## ▶ Open — added by `/gaps` (2026-09-08, with approval)

Not in the original template; added because the scan surfaced it.

| Item | Cost of skipping | Status |
|---|---|---|
| Supply-chain hygiene — dependency update and advisory process | Two external packages are pinned (SwiftSoup 2.13.4, Kingfisher 8.8.1) with no process for updating them or noticing an advisory. `NetworkKit/Package.swift` pins SwiftSoup `.upToNextMinor(from: "2.13.4")`, which is good discipline; Kingfisher's version requirement lives in `Contributions.xcodeproj` instead, so the two are governed differently. SwiftSoup is load-bearing — it parses the HTML the whole product reads | ▶ |

## ✅ Applied

| Item | Where it lives | Status |
|---|---|---|
| Widgets + intents | The product's core feature: `ContributionsWidget/` (`WidgetBundle`, `IntentTimelineProvider`), with widget-aware rendering in `InterfaceKit/.../Modifiers/Tile.swift` (`widgetRenderingMode`, `widgetAccentable`). Boundary documented in `ContributionsWidget/CLAUDE.md` | ✅ |

## ⛔ Skipped

No evidence in the repo, and the capability does not apply to this product. Each has a matching
*Do not re-propose* row in [DECISIONS.md](DECISIONS.md).

| Item | Why it does not apply | Status |
|---|---|---|
| Analytics event taxonomy | No analytics SDK linked, no events sent | ⛔ |
| Auth flows | Reads public GitHub contribution data; there is no account or login | ⛔ |
| IAP / StoreKit | No StoreKit usage — the app is free with no purchases | ⛔ |
| CloudKit sync | Usernames persist to `UserDefaults` via `ContributionsStorage`; nothing syncs | ⛔ |
| Universal links / AASA | No deep links and no URL routing, so there is no AASA file to be missing | ⛔ |
| Keyboard / focus management | No form. The only `TextField` is inside a system `.alert` (`ContributionsList.swift:69`), which manages its own focus | ⛔ |
| Search | No search interface or `CoreSpotlight` usage | ⛔ |
| Haptics | No `UIFeedbackGenerator` or `sensoryFeedback` usage | ⛔ |
| Biometrics | No `LocalAuthentication`; nothing to protect behind it | ⛔ |
| Push notifications | No `UNUserNotificationCenter`, no push entitlement | ⛔ |
| Handoff | **Scan false positive.** `Info.plist`'s `NSUserActivityTypes` holds only `ConfigurationIntent` — the widget's intent configuration. No `NSUserActivity` or `onContinueUserActivity` anywhere in Swift | ⛔ |

The original single row reading `Auth flows · IAP · CloudKit · widgets · search · haptics ·
biometrics` was expanded into its seven items above, because `widgets` resolves ✅ while the other
six resolve ⛔. No item was dropped.

## ▶ Open — adoption debt from `/project-init` (2026-09-08)

Not capability gaps: these are rules **adopted** into CLAUDE.md whose existing violations were not
fixed at init. Each is owed work, not a decision to re-make.

| Item | Cost of skipping | Status |
|---|---|---|
| §2.7 — 9 sites: `try?` in `Contributions/Extensions/UserDefaultsExtensions.swift:12` and `:18`, `ContributionsWidget/ContributionsProvider.swift:44`; force-unwraps in `Contributions/Settings/SettingsViewModel.swift:17,21,25`, `NetworkKit/Sources/NetworkKit/Contribution.swift:50`, `ContributionsWidget/ContributionsProvider.swift:40`, `ContributionsWidget/Extensions/DateExtensions.swift:15` | Rule is stated but unmet, so `/review` flags shipped code every run. The three `try?` sites hide real failures — a silent decode error looks like empty data | ▶ |
| §7 wrapper — Kingfisher (`KFImage`) used unwrapped at `Contributions/Guide/StepView.swift:33` | One call site today, so cheap now; swapping or removing the library later touches feature code directly | ▶ |
| iOS floor — lower app target 16.6 → 16.0 to match `Package.swift` | `Package.swift` claims wider support than the app ships. Decided, not applied — build-settings edits belong to `/upgrade-stack` | ▶ |

## Recording an answer

- **Adopt** → do the work, move the row to Applied with where it landed.
- **Defer** → ⏸ and **name the trigger**. A deferral with no trigger is an Open item pretending.
- **Skip** → ⛔ **and** a DECISIONS.md *Do not re-propose* row. Both, or it comes back.

Never delete a row. The value is knowing what was considered and declined.
