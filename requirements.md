# Focus Mode Activator — Requirements

Living document. Updated step by step as requirements are clarified.

## Project

Chrome extension (MV3): toolbar toggle for Focus Mode — silence site notifications while ON, show session summary when OFF.

---

## Confirmed

### CR-001 · Blocking site notifications

**Question:** Does Chrome allow permissions for blocking notifications?

**Answer:** No dedicated permission. Blocking is done via `contentSettings`, not `notifications`.


|                            |                                                                             |
| -------------------------- | --------------------------------------------------------------------------- |
| Block site notifications   | Yes — `contentSettings` permission + `chrome.contentSettings.notifications` |
| Global block               | `primaryPattern: "<all_urls>"`, `setting: "block"`                          |
| `notifications` permission | Extension creates its own alerts only — does not block sites                |


**Implication:** Use `contentSettings` as the primary blocking mechanism.

**Docs:** [https://developer.chrome.com/docs/extensions/reference/api/contentSettings](https://developer.chrome.com/docs/extensions/reference/api/contentSettings)

### CR-002 · Notification scope

**Decision:** Block **website** push notifications, alerts, and toasts while Focus Mode is ON.

| In scope | Push notifications, desktop alerts, in-page toasts (site-origin) |
| Out of scope | Other apps — see backlog |

### ~CR-003 · Data storage

**Decision:** Store all data in the user's browser — no backend server.


|                                    |                        |
| ---------------------------------- | ---------------------- |
| API                                | `chrome.storage.local` |
| Session stats, settings, whitelist | Local only             |
| Internet required                  | No                     |


**Rationale:** Privacy preserved; extension works offline.

### CR-004 · Focus Mode control

**Decision:** **Manual toggle only** via toolbar popup. No auto-schedule in current scope.


|     |                                                              |
| --- | ------------------------------------------------------------ |
| ON  | User clicks toggle → notifications blocked                   |
| OFF | User clicks toggle → blocking removed, session summary shown |


### CR-005 · Session stats

**Decision:** Track **notification count per site** during a Focus session. Show in summary when Focus Mode turns OFF.


|         |                                            |
| ------- | ------------------------------------------ |
| Metric  | Number of notification attempts per origin |
| Scope   | While Focus Mode is ON                     |
| Storage | `chrome.storage.local`                     |


**Not in scope:** Bypass detection, distraction score formula.

### CR-006 · Popup UI

**Decision:** Toolbar icon opens a popup showing **current Focus Mode state** and the toggle.


|         |                                                              |
| ------- | ------------------------------------------------------------ |
| ON      | Popup shows active state (e.g. "Focus Mode ON")              |
| OFF     | Popup shows inactive state; session summary after toggle off |
| Updates | State reflects immediately after toggle                      |


---

Deferred tasks → see `[backlog.md](backlog.md)`