# GenericArch — installed reference

Reference material is **not copied into this repo**. It is listed below and fetched when
something actually needs it, so nothing goes stale and the repo stays uncluttered.

> ## A missing `docs/…` link is a fetch instruction, not a broken link
>
> The installed skills and commands link to these documents by relative path. Those paths
> resolve **after** you fetch — that is the design. When a link under `docs/` does not
> exist locally, fetch it to that exact path and continue; do not treat it as missing, and
> do not guess the content from the filename.
>
> Fetch only what the task needs. Fetching all 24 defeats the point.

- **Source:** `https://github.com/kalpesh-jetani/GenericArch`
- **Pinned to:** `v0.6.2`
- **Installed:** files under `.claude/` and `Scripts/` — those must be local to work.

## Fetch one

```bash
curl -fsSL "$GA_RAW/<path>" -o <path>     # GA_RAW=https://raw.githubusercontent.com/kalpesh-jetani/GenericArch/v0.6.2
```

Fetch into the same relative path so links between docs keep resolving. Delete it again
afterwards if you do not want it tracked — the manifest is the durable record.

## Available

| Document | What's there | Read it when |
|---|---|---|
| `docs/STRUCTURE.md` | Documentation structure | before adding or moving any guidance; before editing CLAUDE.md. |
| `docs/CONVENTIONS.md` | Conventions | naming a new type, deciding what to make `public`, or reviewing a diff. |
| `docs/DONE.md` | Definition of Done | **Read this before saying a change is finished.** `/verify` walks it against your working  |
| `docs/REPO.md` | Repository — versioning, local development, tooling choice | changing an extracted package, setting up a machine, or asking why the |
| `docs/DELIVERY.md` | Delivery — CI, signing, versioning, release | setting up CI, changing signing, cutting a release, or answering "what |
| `docs/patterns/change.md` | Pattern — change | **Not a skill yet.** It becomes one when this repo has the code it describes — `/learn cha |
| `docs/patterns/dark-light-mode.md` | Pattern — dark-light-mode | **Not a skill yet.** It becomes one when this repo has the code it describes — `/learn dar |
| `docs/patterns/feature-complete.md` | Pattern — feature-complete | **Not a skill yet.** It becomes one when this repo has the code it describes — `/learn fea |
| `docs/patterns/release-bump.md` | Pattern — release-bump | **Not a skill yet.** It becomes one when this repo has the code it describes — `/learn rel |
| `docs/patterns/rtl-support.md` | Pattern — rtl-support | **Not a skill yet.** It becomes one when this repo has the code it describes — `/learn rtl |
| `docs/patterns/style-guide.md` | Pattern — style-guide | **Not a skill yet.** It becomes one when this repo has the code it describes — `/learn sty |
| `docs/patterns/wrapper.md` | Wrapping a third-party library | adding, replacing or removing a vendor dependency. The rule is CLAUDE.md §7; |
| `docs/PERFORMANCE.md` | Performance | before adding work to launch, when a list stutters, when a view rebuilds |
| `docs/ADOPTION.md` | Adopting into an existing codebase | adopting the base into a codebase that already exists, or reviewing how a |
| `docs/SHARING.md` | Sharing GenericArch | you are the one **publishing** this base. |
| `docs/PATTERN-SEARCH.md` | Pattern Search: Using `.claude/notes/` to reduce token spend | **Goal**: Claude searches `.claude/notes/` inventories first before calling grep, reducing |
| `docs/SCAN-TRAPS.md` | Scan traps — why the scan scripts are shaped the way they are | **Read this only when you are about to change a scan**, or when a scan's result surprises  |
| `docs/CLAUDE-TASKS.md` | The CLAUDE.md task pipeline | driving the pipeline, adding a phase, or working out why a phase refused. |
| `docs/INSTALL-MANIFEST.md` | Install manifest — the record uninstall.sh trusts | inspecting what an install actually did, debugging an uninstall that kept |
| `docs/SEQUENCE.md` | Command sequence | a command refused with "cannot run yet", you are adopting into an existing |
| `docs/BUILD-PROCESS.md` | Build process | you are about to build, test or archive, or a build failed and you need to |
| `docs/DEPLOYMENT-PROCESS.md` | Deployment process | shipping — a merge that will reach testers or users, a release tag, an |
| `docs/PROJECT-SETTINGS.md` | Project settings | adding a capability, wiring an environment, setting a floor, or answering a |
| `docs/OPENSPEC.md` | OpenSpec bridge | wiring OpenSpec into a repo, or working out why a proposal ignored a rule |
