# Focus Mode Activator — Implementation Plan

Living document. Updated step by step as points are confirmed.

---

## Confirmed

### Plan-001 · Stack

**Decision:** Vanilla JavaScript, HTML, CSS — no framework, no server, no build step.

| | |
|---|---|
| UI | `popup.html` + `popup.js` + `popup.css` |
| Logic | Service worker (`background.js`) |
| Storage | `chrome.storage.local` |
| Load | Chrome → Load unpacked |

**Rationale:** Smallest setup for 45-minute MVP; React/Vue add build tooling without benefit at this UI size.

### Plan-002 · Project structure

**Decision:** Source in `src/`, docs at project root. Load unpacked → select `src/`.

```
├── requirements.md
├── backlog.md
├── implementation-plan.md
└── src/
    ├── manifest.json
    ├── background.js
    ├── popup/
    │   ├── popup.html
    │   ├── popup.css
    │   └── popup.js
    ├── content/
    │   └── intercept.js
    └── icons/
        ├── icon16.png
        ├── icon48.png
        └── icon128.png
```

### Plan-003 · Notification blocking layers

**Build order:** Layer 1 first, Layer 2 later.

#### Layer 1 · Push notifications (P0 — implement first)

| | |
|---|---|
| Where | `background.js` |
| How | `contentSettings.notifications` — `<all_urls>` → `block` on Focus ON; restore on OFF |
| Blocks | Web push → service worker → tray popup |
| Reliability | High |

#### Layer 2 · In-page toasts (P1 — implement after Layer 1)

| | |
|---|---|
| Where | `content/intercept.js` |
| How | Content script + `MutationObserver` → detect/hide toast DOM → message background to count |
| Blocks | Site-owned in-page banners |
| Reliability | Best-effort (varies by site) |

**Out of scope:** Desktop alerts.

---

## Pending

*(Discuss in chat first; add here after approval.)*
