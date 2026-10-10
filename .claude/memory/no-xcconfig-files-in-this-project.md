---
name: no-xcconfig-files-in-this-project
description: Build settings live in project.pbxproj — this repo has zero .xcconfig files, unlike GenericArch's model
metadata:
  type: project
---

All build settings live in `Contributions.xcodeproj/project.pbxproj`. **There are no `.xcconfig` files anywhere in this repo**, and no DEV/TEST/BETA/PROD scheme split — there are two configurations and two targets (app + widget extension).

**Why:** GenericArch's `SCHEMES.md` and `PROJECT.md` assume four `.xcconfig` files with per-stage bundle-ID suffixes. That model does not describe this project, so a note built from the template would look authoritative and be fiction. It is also why CLAUDE.md §2.10 was adopted as the prohibition only — its `AppEnvironment`-at-the-composition-root half has nothing to read from.

**How to apply:** when `/sync-app-notes` builds SCHEMES.md, take the values from `project.pbxproj` (bundle ID `com.andergoig.GitHubContributions`, Team ID `75Z728ZJJT`, `CURRENT_PROJECT_VERSION = 19`) and record that there are no xcconfigs rather than emitting empty rows for four of them.
