# Architecture

## Design Decision: No Local Component

MAIA is a web application today. Adding a local component (desktop app, native binary, background service) would introduce significant complexity for naive users:

- An installer to run and maintain
- Auto-update mechanisms or the risk of falling behind
- A background process that can crash or need restarting
- An enlarged security attack surface on the patient's machine
- Support burden across OS versions, architectures, and permissions

**The architecture must work with only two things the patient already has or can easily add: a browser and a Chrome extension.**

## Architecture: MAIA Extension

A purpose-built Chrome extension that communicates directly with MAIA's server over HTTPS. No local bridge, no MCP protocol, no Node.js, no native messaging host.

```
Patient's Chrome                       MAIA Server (cloud)
┌─────────────────────┐               ┌──────────────────────┐
│                     │               │                      │
│  Patient portal     │               │  Claude API          │
│  (authenticated)    │               │  (intelligence)      │
│                     │               │                      │
│  MAIA extension     │◄── HTTPS ────►│  Orchestration       │
│  (browser actions)  │               │  (decides what to    │
│                     │               │   read/click/fill)   │
└─────────────────────┘               │                      │
                                      │  MAIA chat UI        │
         Patient ◄── chat ───────────►│  (patient comms)     │
                                      │                      │
                                      └──────────────────────┘
```

### How it works

1. Patient logs in to their portal in Chrome (MAIA never sees credentials)
2. Patient opens MAIA and asks for help requesting records
3. MAIA's server sends instructions to the extension via HTTPS
4. The extension reads the DOM, clicks elements, fills forms as directed
5. The extension sends page content back to MAIA's server
6. MAIA's server calls Claude API to reason about what to do next
7. MAIA communicates with the patient through its existing chat UI

### What the extension does (thin agent)

The extension is deliberately thin — it executes actions, it doesn't decide them:

- **Read DOM** — query selectors, get text content, read form values
- **Click** — click elements by selector or coordinates
- **Fill forms** — set input values, check checkboxes, select dropdowns
- **Execute JS** — run JavaScript in the page context (for portals with idle-timeout issues)
- **Report state** — send page title, URL, visible text back to the server

The extension does NOT:
- Reason about what to do (that's Claude API on the server)
- Store patient data (stateless — all state lives on MAIA's server or in the patient's local folder)
- Communicate with any server other than MAIA's

### Patient setup

1. Install the MAIA extension from the Chrome Web Store (one click)
2. Log in to their patient portal
3. Open MAIA and ask for help

No Node.js. No CLI. No local servers. No developer mode. No accounts to create beyond MAIA.

## Why Not MCP-Based Solutions

Every MCP-Chrome solution we evaluated (mcp-chrome, Playwright MCP, Browser MCP) requires a **local bridge process** running on the patient's machine:

| Solution | Local requirement | Why |
|---|---|---|
| mcp-chrome | Node.js 20+ + `npm install -g mcp-chrome-bridge` + unpacked extension in Developer Mode | MCP protocol needs a local process to bridge between the browser extension and MCP clients |
| Playwright MCP | Node.js + local Playwright server | Same bridge requirement |
| Claude in Chrome | None, but requires $20+/month subscription | Anthropic bundles the bridge into their infrastructure |
| Antigravity (Google) | Full IDE install (bundles Node.js internally) | Bridge hidden inside the IDE |

The fundamental issue: Chrome extensions cannot expose MCP endpoints on their own. They need a native messaging host or local HTTP server to bridge to MCP clients. This always means a local component.

**Our solution: skip MCP.** The extension communicates directly with MAIA's server over HTTPS — standard web technology that Chrome extensions support natively. No bridge needed.

## Security Considerations

### PHI in transit

Unlike the localhost-only MCP approach, the MAIA extension sends DOM content (which may include PHI) to MAIA's server over the network. This requires:

- **HTTPS only** — all communication encrypted in transit
- **Authentication** — the extension authenticates with MAIA's server (the patient is already authenticated with MAIA)
- **Minimal data** — the extension sends only the DOM content MAIA requests, not the entire page
- **No logging of PHI** — MAIA's server must not log portal DOM content
- **Claude API data handling** — DOM content sent to Claude API is subject to Anthropic's data handling policies (no training on API data by default)

This is a real tradeoff vs the localhost approach, but MAIA already handles PHI (it's a health records application), so the trust boundary is consistent.

### Credentials

- The extension never reads or transmits passwords, session tokens, or cookies
- The patient logs in to their portal directly — MAIA is not in that loop
- The extension only activates on pages the patient explicitly directs it to

### Extension permissions

The extension requests only the permissions it needs:
- `activeTab` — read/interact with the current tab only when the patient activates the extension
- Access to MAIA's server domain — for HTTPS communication
- No `<all_urls>`, no `tabs`, no `history`, no `cookies`

### Attack surface

- **Extension itself** — published on Chrome Web Store, subject to Google's review. Code is open source for audit.
- **MAIA server** — existing attack surface, already handles PHI. No new surface beyond a new API endpoint for extension commands.
- **No local component** — nothing new running on the patient's machine beyond a standard Chrome extension.

## Alternatives Considered

### mcp-chrome (rejected)

Free, open-source, 12k GitHub stars. But requires Node.js 20+, global npm install, unpacked extension in Developer Mode, and a running bridge process. Developer setup, not patient setup.

### Playwright MCP (rejected)

Same local bridge requirement as mcp-chrome. Slightly more mature automation primitives but same fundamental problem.

### Claude in Chrome (rejected)

Excellent UX but requires paid Claude subscription ($20+/month). Non-starter for patients.

### Puppeteer/CDP direct (rejected)

No extension needed, but requires launching Chrome with `--remote-debugging-port=9222`. A CLI flag is harder than installing an extension for non-technical users.

### Antigravity-style bundled app (rejected)

Google bundles the bridge into a desktop IDE. We could do the same, but MAIA has no local component today and adding one introduces installer, update, and security complexity that outweighs the benefit.

### Native Messaging API (rejected)

Chrome's built-in way for extensions to talk to local processes. Still requires a native host binary installed on the patient's machine — a local component by another name.

## What We Lose vs Claude in Chrome

| Capability | Claude in Chrome | MAIA extension |
|---|---|---|
| Natural language `find` | Built-in | Server constructs selectors via Claude API |
| Integrated reasoning | Extension reasons locally | Server makes Claude API calls |
| Screenshot analysis | Built-in | Extension captures, server sends to Claude API |
| Works offline | Partially | No — requires MAIA server |
| Setup complexity | Extension + subscription | Extension only |
| Cost to patient | $20+/month | Free |
| Local component | None (Anthropic infra) | None (MAIA server) |
| PHI stays local | Yes (localhost only) | No — transits to MAIA server over HTTPS |

The main tradeoffs: PHI transits the network (mitigated by HTTPS + MAIA already handles PHI), and no offline capability (acceptable — patients need internet for the portal anyway).

## Implementation Plan

1. **Build the MAIA Chrome extension** — thin agent with DOM reading, clicking, form filling, JS execution, HTTPS communication with MAIA server
2. **Publish to Chrome Web Store** — one-click install for patients
3. **Add extension API to MAIA server** — endpoints for sending commands and receiving page state
4. **Add orchestration logic to MAIA server** — Claude API calls to reason about portal pages, prompt-before-act pattern, confirmation gates
5. **Integrate with MAIA chat UI** — patient communicates through existing chat interface

## Future

- **Portal-specific optimizations** — known paths and form structures (like the [MGB MyChart mapping](portals/mychart-mgb.md)) allow the server to skip exploratory navigation
- **Patient profile / folder** — reusable fields and request history for faster repeat requests
- **Crowdsourced hints** — curated navigational data for portal discovery (data only, never commands)
