---
name: init-scan-conflict-list-is-not-exhaustive
description: ga-init-scan.sh's conflict table misses §2.7 violations and over-counts memories across projects
metadata:
  type: project
---

`ga-init-scan.sh`'s `## conflicts` table is a starting point, not the full set. Two things it got wrong on the 2026-09-08 adoption:

1. **It does not scan for §2.7 at all** — `try?` that swallows, `fatalError`, force-unwraps. Nine real violations were found by hand and are now tracked in GAPS.md. Two of the five signals it *did* report (completion handlers, singletons) turned out to be false positives.
2. **Its `## levels` memory count globs every project on the machine.** It reported '13 project-scoped memories' from `~/.claude/projects/*/memory/*.md`; this repo's own memory directory was empty.

**Why:** treating the scan's five rows as the complete conflict set would have adopted §2.7 believing the code already satisfied it, and would have sent a non-existent duplicate list to `/clean-up-genericarch-extra-memory`.

**How to apply:** on any later adoption or `/review` pass, grep for `try?`, `fatalError`, `as!` and force-unwrap patterns directly rather than trusting the artifact, and check `~/.claude/projects/<this-repo>/memory/` by exact path before reporting duplicates. See [[installer-is-bootstrap-not-install-sh]].
