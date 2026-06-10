# Backlog — Implement If Time Remains

Tasks deferred from the 45-minute MVP. Pick up after core flow works.

---

## Auto-schedule (deferred from requirements)

| Task | Notes |
|------|-------|
| Fixed daily time range | Single start/end window; `chrome.alarms` |
| Manual override vs schedule | User toggle wins until next scheduled cycle |
| UTC schedule times | Store/evaluate schedule in UTC |
| Local time UI | Show/set local times; convert to/from UTC |

---

## Keyboard shortcut

**Approach:** Suggest a default in manifest — do not assign programmatically.

| Step | Detail |
|------|--------|
| Default | Uncommon combo in `commands` (e.g. `Alt+Shift+F`) |
| Check | `chrome.commands.getAll()` — if our command has no `shortcut`, default was not assigned (conflict) |
| Prompt | Ask user to set their own at `chrome://extensions/shortcuts` |

**Note:** Cannot scan all keys in use system-wide — only detect whether Chrome assigned our suggested default.

---

## From original brief (suggested features)

| Task | Notes |
|------|-------|
| Whitelist domains | Per-domain `allow` rules overriding global block |
| Bypass detection | Sites that notified while Focus Mode was on but evaded blocking |
| Distraction score | Derived ranking from per-site notification counts (CR-005) |
| Multiple schedule slots | Out of scope |
| Auto-suggest schedule from usage | Out of scope |

---

## Other (discussed, not confirmed)

| Task | Notes |
|------|-------|
| Other apps notifications | OS apps, other Chrome extensions |
| Restore prior notification settings | Save/restore `contentSettings` on Focus Mode OFF |
| Incognito support | Separate `contentSettings` scope |
| Session history beyond last session | Persist past session summaries locally |
