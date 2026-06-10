# Chrome extension focus mode planning
_Exported on 6/10/2026 at 16:46:27 GMT+5 from Cursor (3.7.12)_

---

**User**

Chrome Extension — Focus Mode Activator



Build a Chrome extension that blocks all browser notification popups and push notifications with a single toolbar click. When Focus Mode is ON, no site can interrupt you. When turned OFF, a session summary shows how many notifications were blocked and from which sites.

Suggested additions worth planning:



Auto-schedule Focus Mode for specific hours daily

Per-domain distraction score showing how aggressively each site tried to interrupt

Keyboard shortcut to toggle without opening the popup

Whitelist for sites that should never be blocked

Flow: Click toolbar icon → Activate → All notifications silenced → Deactivate → View session report



this is the task assigned to me
understand it
we will discuss and plan around it
For the planning, I want you to create an md file
my current codebase contains a boilerplate code of nest js
you can simply skip this codebase, it has nothing useful as of now
and keep the structured plan there so that at the end, we have our appraoch documented and can start development
you just need to understand the task for now
don't create a plan yet
we will do it step by step

---

**Cursor**

The task is a **Chrome extension** (not part of your NestJS repo). Here’s my read of it.

## Core product

**Focus Mode Activator** — one-click focus from the toolbar that silences browser interruptions while it’s on, then gives you a short report when you turn it off.

| State | Behavior |
|--------|----------|
| **OFF** | Normal browser notifications |
| **ON** | No site can show notification popups or push notifications |
| **Turn OFF** | Session summary: how many were blocked and from which sites |

**Primary flow:** toolbar click → activate → everything silenced → deactivate → session report.

---

## What “blocking notifications” likely means technically

In Chrome extensions, this usually involves:

1. **Permission API** — intercept or suppress `Notification` requests (sites calling `new Notification()` or the Notifications API).
2. **Push / service workers** — harder; may need to block or redirect push subscription behavior where the extension API allows it.
3. **Tracking** — log blocked attempts (origin/domain, timestamp, maybe type) for the session report and distraction score.

Chrome’s extension surface (e.g. `notifications`, `declarativeNetRequest`, content scripts, background service worker) defines what’s actually possible; some push paths may be partially blockable rather than fully silenced.

---

## Suggested features (beyond MVP)

1. **Auto-schedule** — Focus Mode on automatically during chosen daily hours (e.g. 9–5).
2. **Per-domain distraction score** — rank sites by how often they tried to notify you during a session.
3. **Keyboard shortcut** — toggle without opening the popup (`chrome.commands`).
4. **Whitelist** — domains that are never blocked even when Focus Mode is on.

---

## Likely extension pieces

- **Manifest V3** — `manifest.json`, background service worker, popup UI, optional options page.
- **Toolbar icon** — ON/OFF state (badge/color).
- **Storage** — session stats, schedule, whitelist (`chrome.storage`).
- **Popup** — toggle + summary after deactivation.
- **Permissions** — notifications-related APIs, storage, possibly `alarms` for scheduling.

---

## Out of scope for now

Your **NestJS boilerplate** isn’t part of this; the extension can be a separate project/folder unless you later add sync, accounts, or analytics via a backend.

---

## What I’m not doing yet

Per your instructions: **no plan MD file yet** and **no implementation** — we’ll build the structured plan step by step when you’re ready.

When you want to continue, we can pick a starting point, for example:

- MVP scope (toggle + block + session report only)
- Chrome APIs and limits (what we can vs can’t block)
- Project layout and manifest skeleton
- Then the planning doc

Tell me which piece you want to tackle first.

---

**User**

I want you to keep your answers to the point and accurate
My first and main question is
What tools are commonly used to build a chrome extension?


---

**Cursor**

**Core (required)**

- **HTML, CSS, JavaScript** — popup, options page, and UI
- **`manifest.json`** — extension config (Manifest V3 is current)
- **Chrome Extension APIs** — e.g. `chrome.storage`, `chrome.action`, `chrome.commands`, background service worker

**Languages**

- **Plain JS** — most common for small extensions
- **TypeScript** — common for anything non-trivial (types, fewer bugs)

**Build / bundling (optional but common)**

- **Vite** or **Webpack** — bundle TS/JS, assets
- **CRXJS** (`@crxjs/vite-plugin`) — Vite + hot reload for extensions
- **WXT** — modern extension framework (Vite-based, MV3-focused)
- **Plasmo** — React/Vue/Svelte + TS, fast scaffolding

**UI frameworks (optional)**

- **React**, **Vue**, or **Svelte** — if the popup/options UI is more than a few controls

**Dev & test**

- **Chrome** — load unpacked at `chrome://extensions`
- **Chrome DevTools** — debug popup, service worker, content scripts
- **Extension Reloader** — reload on save during dev

**Storage / state**

- **`chrome.storage.local` / `sync`** — settings, whitelist, session stats (no backend needed for your task)

**Not needed for your task**

- NestJS or any backend unless you add sync/accounts later

**Practical pick for Focus Mode:** **TypeScript + WXT or CRXJS + Vite**, or **Plasmo** if you want React in the popup. Plain JS is fine for a hackathon MVP.

