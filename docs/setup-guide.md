# Setup Guide

## Prerequisites

1. **Google Chrome** (version 122 or later)
2. **MAIA Chrome extension** (free, from Chrome Web Store)

No paid subscriptions, no local software, no accounts beyond MAIA.

## Installation

### Step 1: Install the MAIA extension

1. Open Chrome
2. Click the install link provided by MAIA (or search "MAIA" in the Chrome Web Store)
3. Click "Add to Chrome" → "Add extension"
4. Done — the MAIA icon appears in the toolbar

No sign-in needed for the extension. No configuration. No local servers or background processes.

### Step 2: Log in to your patient portal

1. Navigate to your patient portal (e.g., Patient Gateway, MyChart)
2. Log in with your credentials as you normally would
3. Complete any 2FA or security questions
4. Stay on the portal's home page

MAIA will never see or touch your password. You handle all authentication yourself.

### Step 3: Start the assistant

1. Open MAIA
2. Ask for help requesting your records
3. MAIA connects to the extension in your Chrome tab and begins guiding you

## What the extension does

The MAIA extension is a thin helper that lets MAIA's server read and interact with the patient portal page you have open. It can:

- Read text on the page (to find the right form)
- Click buttons and menu items (to navigate)
- Fill in form fields (dates, checkboxes — always with your confirmation first)

The extension does NOT:

- See or store your passwords
- Access pages you haven't directed it to
- Send data to anyone other than MAIA's server
- Run any background processes on your computer

## Privacy

- The extension only activates on the tab you point it to
- Portal page content is sent to MAIA's server over encrypted HTTPS
- MAIA already handles your health information — this uses the same trust relationship
- No data is stored in the extension itself

## Troubleshooting

### "MAIA can't connect to your browser"

Check:
- Is the MAIA extension icon visible in Chrome's toolbar?
- Is Chrome open and on your portal page?
- Try refreshing the portal page

### Portal page seems stuck

Medical portals (especially MyChart/Epic) are complex web applications. MAIA handles this automatically, but if you notice slowness, wait a few seconds for the page to fully load.

### Extension disconnects

If Chrome restarts or the extension updates, the connection may drop. MAIA will detect this and prompt you to refresh.

## Technical Details

The extension communicates with MAIA's server over HTTPS — the same standard web encryption used by every banking and medical website. There is no local server, no MCP bridge, and no Node.js involved. The extension is published on the Chrome Web Store and is open source for security audit.

For the full architecture rationale, see [architecture.md](architecture.md).
