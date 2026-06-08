# Demo Video Script — Records Request Assistant

## Target audience

Patients who want to request their medical records but aren't tech-savvy. Assume no familiarity with Chrome extensions, AI tools, or browser automation.

## Setup (before recording)

- Chrome open
- mcp-chrome extension NOT installed (so you can show the install flow)
- Patient Gateway account ready to log in
- MAIA open (or Claude Code for the prototype demo)

---

## Part 1: Install the browser helper (~1 min)

**[Show Chrome browser, toolbar visible]**

"To get started, we need to add a small free tool to your browser that lets MAIA see and interact with your patient portal. I'll show you exactly how."

1. Click the puzzle piece icon (Extensions) in the Chrome toolbar
2. Click "Manage Extensions" at the bottom
3. Click "Open Chrome Web Store" (or navigate to chrome.google.com/webstore)
4. Type "mcp-chrome" in the search bar
5. Find "mcp-chrome" — click "Add to Chrome"
6. Click "Add extension" in the confirmation dialog
7. The mcp-chrome icon appears in the toolbar

"That's it — the browser helper is installed. It's free, and it doesn't require any account or sign-in."

---

## Part 2: Log in to your patient portal (~30 sec)

**[Navigate to Patient Gateway]**

"Now go to your patient portal and log in the way you normally do. MAIA will never see or touch your password — you handle the login yourself."

1. Navigate to patientgateway.massgeneralbrigham.org
2. Log in with your credentials
3. Complete any 2FA/security questions
4. Arrive at the home page ("Welcome, Adrian!")

"Once you're on your home page, you're ready to start."

---

## Part 3: Start the assistant (~30 sec)

**[Switch to MAIA]**

"Now switch to MAIA and ask it to help you request your records."

Type something like: "Help me request my medical records from the Patient Gateway page I have open."

**[Assistant connects to the Chrome tab]**

"MAIA can now see your portal page through the browser helper — but only because you gave it permission. It can't access anything else on your computer."

---

## Part 4: Navigate to the form (~1 min)

**[Show the browser as the assistant works]**

MAIA says: "I can see you're logged in to Mass General Brigham Patient Gateway. I'll open the menu to find the records request form."

1. **[Hamburger menu opens]** — MAIA clicks the menu
2. **[Scrolls to "Request Records"]** — MAIA finds it under "My Record"
3. **[Navigates to the form]**

MAIA says: "I found the request form at Menu → My Record → Request Records. This is the Release of Information page. Is this the right place?"

**[User confirms]**

---

## Part 5: Fill out the form (~2 min)

### Organization

MAIA says: "Select your organization from the dropdown below."

**[Dropdown opens, user clicks "Mass General Brigham"]**

### Recipient

MAIA says: "Who should the records be sent to — you, or someone else?"

**[User selects "Me"]**

### Date range

MAIA says: "I'll set the date range to the past 6 months — December 7, 2025 through June 7, 2026. Does that look right?"

**[User confirms, dates are filled in]**

### Locations

MAIA says: "Select which hospital locations to include."

**[User checks their locations]**

### Record types

MAIA says: "I'll include all record types except 'Other.' OK?"

**[User confirms, checkboxes are checked]**

### Continue

MAIA says: "Everything looks good on this page. Ready to continue to the review step?"

**[User confirms, Continue is clicked]**

---

## Part 6: Review and submit (~1 min)

**[Review page loads]**

MAIA says: "Here's a summary of your request:
- Organization: Mass General Brigham
- Sent to: You
- Date range: 12/7/2025 – 6/7/2026
- Location: Massachusetts General Hospital
- Record types: All except Other

There's a consent section that says your records may include sensitive information and you should review them before sharing. The authorization expires in 90 days.

Would you like to submit this request?"

**[User confirms]**

MAIA says: "Submitting your request now."

**[Send request is clicked]**

**[Confirmation page]**

MAIA says: "Your request has been submitted. You should receive your records within [timeframe]. I've saved the details so next time will be even faster."

---

## Closing (~15 sec)

"That's it — your records have been requested. Next time you need to do this, MAIA will remember your preferences and the date of this request, so it can pick up right where you left off."

---

## Production Notes

- **Total estimated length:** 5–6 minutes
- **Key moments to emphasize:** MAIA always asks before doing anything. The patient stays in control. No paid subscriptions needed.
- **Tone:** Calm, unhurried, encouraging. This is for people who may be anxious about technology touching their health records.
- **Captions:** Add captions — accessibility matters, and some viewers will watch without sound.
- **Resolution:** Record at 1080p minimum. The form fields need to be readable.
- **Terminology:** Say "browser helper" instead of "extension" when possible. Avoid "MCP," "API," "DOM," or any technical jargon.
