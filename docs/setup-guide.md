# Setup Guide

## Prerequisites

1. **Google Chrome** (version 122 or later)
2. **Claude account** (claude.ai)
3. **Claude in Chrome extension**

## The Onboarding Problem

The biggest barrier for naive users is installing Claude in Chrome. The current flow requires:

1. Knowing what a Chrome extension is
2. Finding the Chrome Web Store
3. Searching for "Claude"
4. Clicking "Add to Chrome"
5. Signing in to the extension
6. Understanding what the extension icon means and how to interact with it

Most patients requesting medical records are not power users. "Install a Chrome extension" is a significant friction point.

## Current Installation Steps

### Step 1: Install Claude in Chrome

1. Open Chrome
2. Go to the Chrome Web Store: `chrome.google.com/webstore`
3. Search for "Claude" by Anthropic
4. Click "Add to Chrome" → "Add extension"
5. The Claude icon (a sparkle) appears in the toolbar

### Step 2: Sign in

1. Click the Claude icon in the Chrome toolbar
2. Sign in with your Claude account
3. The icon should become active (not grayed out)

### Step 3: Connect to Cowork

1. Open Claude Code or Claude Desktop
2. The Chrome extension should appear as a connected tool
3. Verify by checking available MCP tools

## Ideas to Simplify Onboarding

### Near-term (work within current constraints)

- **Direct install link:** Provide a one-click link that opens the Chrome Web Store directly on the Claude extension page, skipping the search step.
- **Visual step-by-step guide:** A short animated GIF or video showing the exact clicks needed. Embedded in the assistant's first interaction.
- **Detection and guidance:** When the assistant starts, check if Claude in Chrome is connected. If not, display specific instructions with screenshots rather than a generic error.

### Medium-term (requires Anthropic platform changes)

- **Auto-install prompt:** If Claude detects Chrome but no extension, prompt the user with a one-click install dialog (similar to how sites prompt "Install our app").
- **Companion app:** A lightweight installer that sets up Chrome + extension + Claude account in one flow.
- **Progressive Web App (PWA):** Avoid the extension entirely by running as a PWA with the necessary permissions.

### Long-term (architectural alternatives)

- **No-extension approach:** Use a proxy or API-based approach that doesn't require a browser extension at all. The patient would authenticate with their portal through a controlled browser session.
- **Native app:** A dedicated application that embeds a browser and the assistant, eliminating the extension requirement entirely.
- **MAIA integration:** Once integrated with MAIA, the setup could happen during MAIA onboarding (which the patient is already doing for their health records). MAIA's existing welcome/setup flow could guide the extension installation as one step in a larger setup process.

## Technical Notes

- Claude in Chrome requires an active connection to work. If the extension disconnects (browser restart, sleep, etc.), the assistant should detect this and guide reconnection.
- The extension creates a "tab group" for the assistant's tabs. Closing all tabs in the group removes the group — the assistant handles this gracefully.
- Medical portal SPAs (MyChart/Epic) have idle-timeout issues with the extension. See [Phase 1 Navigation](phase-1-navigation.md) for the workaround.
