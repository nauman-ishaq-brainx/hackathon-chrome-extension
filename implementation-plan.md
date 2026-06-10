# Focus Mode Activator — Implementation Plan

Based on confirmed requirements in [`requirements.md`](requirements.md). Backlog items excluded unless time remains.

---

## Stack

**Use: vanilla JavaScript, HTML, CSS — no framework, no build step, no server.**

| Choice | Why |
|--------|-----|
| **Vanilla JS** | Chrome extensions are browser APIs + small UI; your Node/JS skills transfer directly |
| **HTML + CSS popup** | One toggle + session summary — no component framework needed |
| **No Node server** | CR-003: all data in `chrome.storage.local` |
| **No React/Vue** | Adds build tooling and time; no benefit at this scale |
| **No TypeScript (MVP)** | Optional later; plain JS is faster for a 45-minute build |

**Optional later (not MVP):** Vite for bundling, TypeScript, or a minimal CSS approach — only if the project grows.

**Load in Chrome:** `chrome://extensions` → Developer mode → Load unpacked → select project folder.

---

## Architecture (MV3)

```
┌─────────────────┐     messages      ┌──────────────────────┐
│  popup.html/js  │ ◄──────────────► │  service worker      │
│  (toggle + UI)  │                   │  (background.js)     │
└─────────────────┘                   └──────────┬───────────┘
                                                 │
                    ┌────────────────────────────┼────────────────────────────┐
                    ▼                            ▼                            ▼
         contentSettings.notifications   chrome.storage.local      content script (toasts)
         block / restore on toggle       state + session stats     inject when Focus ON
```

| Piece | Role |
|-------|------|
| **Service worker** | Toggle Focus Mode, apply/remove `contentSettings`, persist state, aggregate stats |
| **Popup** | Show ON/OFF state, toggle control, session summary on deactivate |
| **Content script** | Best-effort toast interception + counting (CR-002); push/alerts via `contentSettings` |
| **Storage** | `focusMode`, `sessionStats`, saved prior notification setting (for restore) |

---

## File structure

```
chrome-extension/
├── manifest.json
├── background.js          # service worker
├── popup/
│   ├── popup.html
│   ├── popup.css
│   └── popup.js
├── content/
│   └── intercept.js       # Notification API + toast counting (page context if needed)
├── icons/
│   ├── icon16.png
│   ├── icon48.png
│   └── icon128.png
├── requirements.md
├── backlog.md
└── implementation-plan.md
```

---

## Manifest essentials

```json
{
  "manifest_version": 3,
  "permissions": ["contentSettings", "storage"],
  "host_permissions": ["<all_urls>"],
  "background": { "service_worker": "background.js" },
  "action": { "default_popup": "popup/popup.html" },
  "content_scripts": [{
    "matches": ["<all_urls>"],
    "js": ["content/intercept.js"],
    "run_at": "document_start"
  }]
}
```

Adjust `content_scripts` if toast interception needs a separate injected script in page context.

---

## Implementation phases

### Phase 1 — Core toggle (~15 min)

**Goal:** CR-004, CR-006 basics

1. `manifest.json` + placeholder icons
2. `background.js`: read/write `focusMode` in `chrome.storage.local`
3. On ON: `chrome.contentSettings.notifications.set({ primaryPattern: "<all_urls>", setting: "block" })`
4. On OFF: restore previous setting (save on first ON if not in backlog — minimal: set to `"ask"`)
5. `popup.html/js`: toggle switch, show "Focus Mode ON" / "OFF"
6. Toolbar badge optional: `ON` / blank

**Done when:** Toggle blocks a test site notification (e.g. MDN Notification demo).

---

### Phase 2 — Session stats (~15 min)

**Goal:** CR-005

1. Storage shape:
   ```js
   {
     focusMode: boolean,
     session: { startedAt, counts: { "https://origin": number } }
   }
   ```
2. Reset `session.counts` when Focus Mode turns ON
3. Content script / injected script: on blocked notification attempt, `chrome.runtime.sendMessage({ type: "NOTIFICATION_ATTEMPT", origin })`
4. Background increments count per origin
5. Popup on OFF: render total + list sorted by count

**Done when:** Deactivate shows count per site from a test page.

---

### Phase 3 — Toasts + polish (~15 min)

**Goal:** CR-002 (toasts best-effort)

1. Content script at `document_start`: patch or observe common toast patterns if feasible within time; otherwise document as partial
2. Popup styling (clear ON/OFF states)
3. Manual test checklist (below)

**If time runs out:** Ship Phase 1 + 2; toasts stay partial — push/alerts still covered by `contentSettings`.

---

## Key functions (background)

| Function | Responsibility |
|----------|----------------|
| `enableFocusMode()` | Save prior content setting → set global block → reset session |
| `disableFocusMode()` | Restore prior setting → return session summary |
| `recordAttempt(origin)` | Increment `session.counts[origin]` |
| `getState()` | Return `{ focusMode, session }` for popup |

---

## Testing checklist

- [ ] Toggle ON → site cannot show desktop notification
- [ ] Toggle OFF → notifications allowed again (or prior setting restored)
- [ ] Popup reflects state immediately after toggle
- [ ] Session summary shows total + per-origin counts after OFF
- [ ] State persists after closing/reopening browser
- [ ] Works offline (no network calls)

---

## Out of scope for this plan

See [`backlog.md`](backlog.md): schedule, keyboard shortcut, whitelist (full override when added), distraction score, other apps.

---

## Time budget (45 min)

| Phase | Minutes |
|-------|---------|
| Phase 1 — Core toggle | 15 |
| Phase 2 — Session stats | 15 |
| Phase 3 — Toasts + polish | 15 |

**Priority if slipping:** Phase 1 → Phase 2 → Phase 3.
