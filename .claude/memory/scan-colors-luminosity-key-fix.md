---
name: scan-colors-luminosity-key-fix
description: Scripts/scan-colors.py was patched locally — do not let sync-with-genericarch revert it
metadata:
  type: project
---

`Scripts/scan-colors.py` line 27 shipped with a bug: it detected dark appearances by testing `x.get("key") == "luminosity"`, but Xcode writes `{"appearance": "luminosity", "value": "dark"}`. The test never matched, so `lum` was always `"any"` and the dark entry **overwrote the light one** in the same dict key.

The output was wrong in two ways at once: every colorset reported 'one appearance only', and the **dark** hex appeared in the 'Any / Light' column while the light value was dropped entirely. On this repo that mislabelled 31 of 32 colorsets.

**Patched locally on 2026-09-08** to `if "luminosity" in (x.get("appearance"), x.get("key"))`, which accepts both spellings. Verified: 31/32 colorsets now report a dark value, matching a hand count.

**Why:** the generated `ASSETS-COLORS.md` table looked authoritative and was wrong — the exact failure `/sync-app-notes` warns about, and unreviewable once believed.

**How to apply:** upstream GenericArch (v0.6.2) still carries the bug, so `/sync-with-genericarch` will offer to overwrite this file — **decline that one file**, or re-apply the patch after syncing, then re-run `./Scripts/sync-notes.sh --apply`. See [[init-scan-conflict-list-is-not-exhaustive]] for the other scan whose output needed checking.
