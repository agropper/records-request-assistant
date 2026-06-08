# Setup Guide

## Prerequisites

1. **Google Chrome** (version 122 or later)
2. **mcp-chrome extension** (free, open source)

No paid subscriptions or accounts required.

## Installation

### Step 1: Install mcp-chrome

1. Open Chrome
2. Go to the Chrome Web Store
3. Search for "mcp-chrome"
4. Click "Add to Chrome" → "Add extension"
5. The mcp-chrome icon appears in the toolbar

The extension starts an MCP server on `http://127.0.0.1:12306/mcp` that the assistant connects to.

### Step 2: Log in to your patient portal

1. Navigate to your patient portal (e.g., Patient Gateway, MyChart)
2. Log in with your credentials as you normally would
3. Complete any 2FA or security questions
4. Stay on the portal's home page

The assistant will never see or touch your password. You handle all authentication yourself.

### Step 3: Start the assistant

1. Open MAIA
2. Ask for help requesting your records
3. The assistant connects to your Chrome tab through mcp-chrome and begins guiding you

## The Onboarding Challenge

The biggest barrier for non-technical users is understanding what a Chrome extension is. The current flow requires:

1. Knowing what a Chrome extension is
2. Finding the Chrome Web Store
3. Searching for "mcp-chrome"
4. Clicking "Add to Chrome"

### What we're doing about it

- **Demo video:** A step-by-step walkthrough showing the exact clicks needed, from extension install through completing a records request. See [video-script.md](video-script.md).
- **Direct install link:** Provide a one-click link that opens the Chrome Web Store directly on the mcp-chrome extension page, skipping the search step.
- **Detection and guidance:** When the assistant starts, it checks if mcp-chrome is reachable. If not, it displays specific installation instructions rather than a generic error.
- **MAIA integration (future):** The extension install becomes one step in MAIA's existing onboarding flow, where the patient is already going through setup.

## Troubleshooting

### "Assistant can't connect to your browser"

The mcp-chrome extension may not be running. Check:
- Is the mcp-chrome icon visible in Chrome's toolbar?
- Try clicking the icon to check its status
- Restart Chrome and try again

### Portal page seems stuck or unresponsive

Medical portals (especially MyChart/Epic) are heavy web applications. The assistant handles this automatically, but if you notice slowness:
- Wait a few seconds for the page to fully load
- Make sure you're on the portal's home page, not a login screen

### Extension disconnects

If Chrome restarts, sleeps, or the extension updates, the connection may drop. The assistant will detect this and prompt you to refresh or reconnect.

## Technical Notes

- mcp-chrome communicates via HTTP on localhost only (`127.0.0.1:12306`) — your portal data is never sent to external servers through the extension
- The extension can read page content but never captures or stores your passwords
- Medical portal SPAs have idle-timeout issues — see [Phase 1 Navigation](phase-1-navigation.md) for how the assistant handles this
