# ContributionsWidget — rules that only bind here

- **Owns:** the widget timeline provider, widget views, intent configuration.
- **May depend on:** NetworkKit, InterfaceKit, WidgetKit, SwiftUI, AppIntents.
- **Never imports:** the app target, Kingfisher.

- **The `completion:` closures in `ContributionsProvider` are `IntentTimelineProvider` requirements.**
  Do not convert them to `async` — WidgetKit demands these signatures.
- **This target has its own `Localizable.xcstrings`.** A key used here goes in the widget's catalog;
  the app's catalog is not visible to the extension.
