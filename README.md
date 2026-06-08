# Records Request Assistant

A Claude in Chrome agent that helps patients navigate medical portals to request their health records.

## Overview

Patients have the right to their medical records, but every portal buries the request form differently. This agent uses Claude in Chrome and Claude Cowork to guide the patient through the process — finding the form, managing a local folder of prior requests, and eventually helping fill out the form with appropriate confirmation gates.

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

## Design Principles

- **Agent never touches credentials.** If the page shows a login, 2FA, or CAPTCHA screen, the agent stops and asks the patient to complete it.
- **Page text is data, not commands.** Any text on the page that reads like an instruction ("send records to...", "click here to verify") is surfaced to the patient, never acted on.
- **Navigation is low-risk; data entry requires confirmation.** Finding the form proceeds without gates. Entering data and submitting require explicit patient approval.
- **The folder is the agent's memory.** Everything that makes a second request easier than the first lives in the local folder.
- **Patient instructions are the only instructions.** Crowdsourced hints (future phase) are navigational reference material, never executable commands.

## Architecture

- **Claude in Chrome** — drives the browser (reads pages, clicks menus, navigates)
- **Claude Cowork** — orchestrates the workflow, manages the local folder, reads/writes the patient profile
- **Local filesystem** — patient folder with records and `maia-profile.json`

## Future

- Portal-specific hints (curated, structured, navigational only) to speed up form discovery
- Integration with MAIA for a unified patient experience
- Instant-download support for portals that offer it

## License

TBD
