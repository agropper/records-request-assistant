# Architecture

## Design Decision: Separated Intelligence and Browser Control

The assistant splits into two layers:

| Layer | Component | Cost to patient |
|---|---|---|
| **Intelligence** | MAIA server calling Claude API | Free (MAIA pays API costs) |
| **Browser control** | mcp-chrome extension | Free (MIT license) |

This replaces the earlier Claude in Chrome approach, which bundled both layers but required a paid Claude subscription ($20+/month).

## mcp-chrome

**Repository:** [github.com/hangwin/mcp-chrome](https://github.com/hangwin/mcp-chrome)
**License:** MIT
**Stars:** ~12k (as of June 2026)

### What it provides

A Chrome extension that exposes 20+ MCP tools over HTTP on `http://127.0.0.1:12306/mcp`:

- **DOM reading** — query selectors, get text content, read attributes
- **Clicking** — click elements by selector or coordinates
- **Form filling** — set input values, check/uncheck checkboxes, select dropdown options
- **Screenshots** — capture the visible page
- **Navigation** — go to URLs, click links
- **JavaScript execution** — run arbitrary JS in the page context
- **Network monitoring** — observe XHR/fetch requests
- **Semantic search** — find elements by description

### How the server connects

The MAIA server acts as an MCP client, connecting to mcp-chrome's HTTP endpoint:

```
MAIA Server                          Patient's Chrome
┌──────────────┐    HTTP POST        ┌──────────────┐
│ MCP Client   │───────────────────►│ mcp-chrome   │
│              │  localhost:12306    │ extension    │
│ Claude API   │    /mcp            │              │
│ (reasoning)  │◄───────────────────│ (browser     │
└──────────────┘    MCP response    │  automation) │
                                    └──────────────┘
```

The server:
1. Receives a patient request via the MAIA chat UI
2. Calls Claude API to decide what to do (read the page, click a menu, fill a field)
3. Executes the action by calling mcp-chrome tools via MCP/HTTP
4. Reads the result and calls Claude API again to decide the next step
5. Communicates with the patient through the MAIA chat UI

### What we lose vs Claude in Chrome

| Capability | Claude in Chrome | mcp-chrome + Claude API |
|---|---|---|
| Natural language element finding | Built-in `find("submit button")` | Must use CSS selectors or JS queries — the server constructs these based on Claude API reasoning |
| Integrated reasoning | Extension reasons about the page | Server makes separate Claude API calls for reasoning |
| Screenshot analysis | Claude sees and reasons about screenshots | Server must send screenshots to Claude API as images |
| Idle-timeout handling | Affected (45s timeout on heavy SPAs) | To be tested — mcp-chrome may handle this differently |
| Setup cost for patient | Extension + paid subscription | Extension only (free) |
| Prompt-before-act | Built into Claude in Chrome's flow | Must be implemented in the server orchestration |

**The key tradeoff:** More server-side code to write, but no cost barrier for patients.

## Alternatives Considered

### Playwright MCP (Microsoft)

Chrome extension + MCP server using Playwright under the hood. Free, Apache 2.0. Slightly more complex setup. Good fallback if mcp-chrome proves unreliable.

### Puppeteer/CDP (direct)

No extension needed — patient launches Chrome with `--remote-debugging-port=9222`, server connects via Chrome DevTools Protocol. Maximum control but requiring a CLI flag is arguably harder than installing an extension for non-technical patients.

### browser-use (Python)

Open-source Python library for LLM browser agents. 60k+ GitHub stars. Would require a Python service alongside MAIA's Node.js server, or rewriting in Python.

### Stagehand (Browserbase)

AI-native TypeScript SDK with `act()`, `extract()`, `observe()` primitives. Good fit for Node.js but relatively new (v3 launched Feb 2026). Cloud features require paid Browserbase subscription.

## Security Considerations

- **Credentials never leave the browser.** The patient logs in to their portal directly in Chrome. mcp-chrome can read the DOM but the MAIA server never sees passwords or session tokens.
- **Localhost only.** mcp-chrome's MCP endpoint is on `127.0.0.1` — not exposed to the network. The MAIA server must run locally or tunnel securely.
- **Page content is data, not commands.** The server treats all DOM content as untrusted data. Prompt injection from portal pages is mitigated by the same rules as Claude in Chrome: page text is never treated as instructions.
- **PHI handling.** The server sees DOM content that may include PHI (patient name, DOB, medical records). All PHI stays local — never logged, never sent to external services beyond Claude API calls (which are subject to Anthropic's data handling policies).

## Future: MAIA Integration

Once proven standalone, this assistant integrates into MAIA:

1. MAIA's existing chat UI becomes the patient communication channel
2. MAIA's server adds MCP client capabilities to connect to mcp-chrome
3. The patient folder (Phase 2) maps to MAIA's existing local storage
4. mcp-chrome installation becomes part of MAIA's onboarding flow
