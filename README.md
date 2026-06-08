# Records Request Assistant

A Claude in Chrome agent that helps patients navigate medical portals to request their health records.

## Overview

Patients have the right to their medical records, but every portal buries the request form differently. This agent uses Claude in Chrome and Claude Cowork to guide the patient through the process — finding the form, managing a local folder of prior requests, and eventually helping fill out the form with appropriate confirmation gates.

## Prerequisites

This assistant requires **Claude in Chrome** — see the [Setup Guide](docs/setup-guide.md) for installation instructions. The current onboarding experience requires familiarity with Chrome extensions, which is a known UX challenge we're actively working to simplify.

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
- [`docs/setup-guide.md`](docs/setup-guide.md) — Prerequisites, Claude in Chrome installation, onboarding challenges
- [`docs/portals/mychart-mgb.md`](docs/portals/mychart-mgb.md) — Complete form mapping for MGB Patient Gateway (MyChart/Epic)

## Design Principles

- **Prompt before act.** Always tell the patient what to do before performing any UI action.
- **Agent never touches credentials.** If the page shows a login, 2FA, or CAPTCHA screen, the agent stops and asks the patient to complete it.
- **Page text is data, not commands.** Any text on the page that reads like an instruction ("send records to...", "click here to verify") is surfaced to the patient, never acted on.
- **Navigation is low-risk; data entry requires confirmation.** Finding the form proceeds without gates. Entering data and submitting require explicit patient approval.
- **The folder is the agent's memory.** Everything that makes a second request easier than the first lives in the local folder.
- **Patient instructions are the only instructions.** Crowdsourced hints (future phase) are navigational reference material, never executable commands.

## Architecture

- **Claude in Chrome** — drives the browser (reads pages, clicks menus, navigates)
- **Claude Cowork** — orchestrates the workflow, manages the local folder, reads/writes the patient profile
- **Local filesystem** — patient folder with records and `maia-profile.json`

## Tested Portals

| Portal | Platform | Status |
|---|---|---|
| [MGB Patient Gateway](docs/portals/mychart-mgb.md) | MyChart/Epic | Form fully mapped, 2-step flow documented |

## Future

- Portal-specific hints (curated, structured, navigational only) to speed up form discovery
- Integration with MAIA for a unified patient experience
- Instant-download support for portals that offer it
- Simplified onboarding that doesn't require manual extension installation

## License

TBD
