---
name: installer-is-bootstrap-not-install-sh
description: This repo has no install.sh — GenericArch flags go through bootstrap.sh --ref v0.6.2
metadata:
  type: project
---

GenericArch was installed here via `bootstrap.sh`, which fetches upstream and then hands off to install.sh inside its own clone. **There is no `install.sh` in this repo.**

Every command doc that says `./install.sh . --with-architecture` will fail here with 'No such file or directory'.

**Why:** the bootstrap install path leaves only `.claude/`, `Scripts/`, `docs/` and `uninstall.sh` behind; install.sh stays in the transient clone.

**How to apply:** run `bash bootstrap.sh --apply --yes --ref v0.6.2 <flags>` instead — install.sh's flags (`--with-architecture`, `--with-claude-md`, `--root-ok`, `--in-place`) are forwarded verbatim. Omit `--apply` for a dry run that prints the file plan first. Pin `--ref` to the version in `genericarch.installation.md` so docs and install cannot drift.
