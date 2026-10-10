# NetworkKit — rules that only bind here

- **Owns:** GitHub contribution fetching; the `GitHub` namespace and `GitHub.Contribution` model.
- **May depend on:** SwiftSoup (this target only), Foundation.
- **Never imports:** InterfaceKit, the app target, ContributionsWidget.

- **Only this target may import SwiftSoup.** HTML parsing stays inside `GitHub.swift`.
- **Public API returns NetworkKit-owned types** — a SwiftSoup type must never appear in a `public`
  signature (root §2.2).
- No SwiftUI here; this package has no UI.
