# InterfaceKit — rules that only bind here

- **Owns:** shared SwiftUI views (`ContributionsView`, `GridStack`), modifiers (`Tile`,
  `TextCaption`), colour extensions, `Resources/`.
- **May depend on:** SwiftUI, Foundation. **Nothing else** — `Package.swift` declares zero dependencies.
- **Never imports:** NetworkKit, the app target, ContributionsWidget, any vendor.

- **Zero dependencies is a rule, not a coincidence.** Adding one is a §0 decision.
- **No business logic and no networking** — this package renders what it is given.
- Colours resolve through `Resources/`, never a literal `Color(red:green:blue:)` in a view.
