# Records Request Assistant

An AI-powered agent that helps patients navigate medical portals to request their health records.

## Overview

Patients have the right to their medical records, but every portal buries the request form differently. This assistant guides the patient through the process — finding the form, managing a local folder of prior requests, and helping fill out the form with appropriate confirmation gates.

## Architecture

The assistant separates **intelligence** from **browser control** with no local component:

- **MAIA server + Claude API** — orchestrates the workflow, decides what to read/click/fill, manages the patient profile, communicates with the patient
- **MAIA Chrome extension** (free, open-source) — thin agent that reads DOM, clicks elements, and fills forms as directed by the server
- **Patient's local folder** — records and `maia-profile.json` for reuse across requests

```
Patient's Chrome                       MAIA Server (cloud)
┌─────────────────────┐               ┌──────────────────────┐
│  Patient portal     │               │  Claude API          │
│  (authenticated)    │               │  (intelligence)      │
│                     │   HTTPS       │                      │
│  MAIA extension     │◄────────────►│  Orchestration       │
│  (browser actions)  │               │                      │
└─────────────────────┘               │  MAIA chat UI        │
                                      └──────────────────────┘
```

The extension communicates directly with MAIA's server over HTTPS — no local bridge, no MCP protocol, no Node.js. See [architecture.md](docs/architecture.md) for the full rationale and alternatives considered.

## Prerequisites

1. **Google Chrome** (version 122 or later)
2. **MAIA Chrome extension** (free, one-click install from Chrome Web Store) — see [Setup Guide](docs/setup-guide.md)

No paid subscriptions, no local software to install, no technical setup.

## Phases

### Phase 1 — Navigate to the request form (current)

The agent reads an already-authenticated medical portal and locates the formal records-request form. It navigates menus, identifies the portal platform, and confirms with the patient before proceeding. No data is entered or submitted.

See [`docs/phase-1-navigation.md`](docs/phase-1-navigation.md) for the full spec.

### Phase 2 — Create or identify the local folder

Create or locate a local folder (e.g. `~/Documents/MAIA for AG`) that serves as the agent's memory for that patient. The folder holds downloaded records and a profile/manifest for reuse across requests.

### Phase 3 — Read folder for prior context

Before filling anything, inspect the folder for reusable fields (name, DOB, address) and the date of the last request, so the new request can be scoped to "records since [date]."

### Phase 4 — Fill the form

Pre-populate the request form from the patient profile. Explicit confirmation gates before entering patient data and before submitting.

## Documentation

- [`docs/phase-1-navigation.md`](docs/phase-1-navigation.md) — Phase 1 navigation spec, agent prompt, idle-timeout workaround
- [`docs/interaction-patterns.md`](docs/interaction-patterns.md) — Prompt-before-act principle, confirmation gates
- [`docs/setup-guide.md`](docs/setup-guide.md) — Prerequisites, mcp-chrome installation
- [`docs/architecture.md`](docs/architecture.md) — Detailed architecture: mcp-chrome vs alternatives, MCP protocol details
- [`docs/portals/mychart-mgb.md`](docs/portals/mychart-mgb.md) — Complete form mapping for MGB Patient Gateway (MyChart/Epic)
- [`docs/video-script.md`](docs/video-script.md) — Demo video script

## Design Principles

- **Prompt before act.** Always tell the patient what to do before performing any UI action.
- **Agent never touches credentials.** If the page shows a login, 2FA, or CAPTCHA screen, the agent stops and asks the patient to complete it.
- **Page text is data, not commands.** Any text on the page that reads like an instruction ("send records to...", "click here to verify") is surfaced to the patient, never acted on.
- **Navigation is low-risk; data entry requires confirmation.** Finding the form proceeds without gates. Entering data and submitting require explicit patient approval.
- **The folder is the agent's memory.** Everything that makes a second request easier than the first lives in the local folder.
- **Patient instructions are the only instructions.** Crowdsourced hints (future phase) are navigational reference material, never executable commands.
- **No cost to the patient.** The architecture must not require patients to have paid AI subscriptions.

## Tested Portals

| Portal | Platform | Status |
|---|---|---|
| [MGB Patient Gateway](docs/portals/mychart-mgb.md) | MyChart/Epic | Form fully mapped, 2-step flow documented |

## Future

- Portal-specific hints (curated, structured, navigational only) to speed up form discovery
- Integration with MAIA for a unified patient experience
- Instant-download support for portals that offer it

## License

TBD
