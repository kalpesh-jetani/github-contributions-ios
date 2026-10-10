# Contributions (app target) — rules that only bind here

- **Owns:** app entry (`ContributionsApp`), composition, and the `Guide/`, `Settings/`, `List/` areas.
- **May depend on:** InterfaceKit, NetworkKit, Kingfisher, SwiftUI, UIKit (interop only).
- **Never imports:** ContributionsWidget.

- **Kingfisher stays in `Guide/StepView.swift`.** It is the one unwrapped vendor import — do not add
  a second call site; wrap it first (GAPS.md).
- **Localization keys go in this target's `Localizable.xcstrings`**, kebab-case, matching the
  existing scheme — not the widget's catalog.
- **New screens carry one state value** covering loading/empty/offline/error/loaded (root §2.5).
