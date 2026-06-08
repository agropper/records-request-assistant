# Interaction Patterns

## Core Principle: Prompt Before Act

The agent must always tell the patient what to do **before** performing any UI action. The patient should never see a dropdown open, a menu appear, or a page change without first understanding what's expected of them.

**Wrong:** Open a dropdown, then say "select your organization."
**Right:** Say "Select your organization from the dropdown below," then open the dropdown.

This applies to every patient-facing interaction:
- Opening dropdowns or menus for the patient to choose from
- Navigating to a new page
- Scrolling to bring a form field into view
- Any action that changes what the patient sees on screen

## Pattern: Prompt → Act → Wait

Every interaction where the patient needs to do something follows this sequence:

1. **Prompt** — Tell the patient what they need to do and why
2. **Act** — Perform the UI action (open dropdown, scroll to field, highlight element)
3. **Wait** — Wait for the patient to complete their action before proceeding

### Example: Organization Selection

```
Agent: "Select your organization from the dropdown below."
       [opens the dropdown by clicking it]
       [waits for patient to click an option]
       [reads the selected value]
       [proceeds to next field]
```

### Example: Confirming the Form

```
Agent: "I found the records request form. It's at Menu → My Record → Request Records.
        This page asks for your organization and where to send records.
        Is this the right form?"
       [waits for patient confirmation]
```

## Pattern: Confirm Before Consequential Actions

Any action that enters patient data or submits a form requires explicit confirmation:

1. **Show** — Tell the patient what you're about to do
2. **Confirm** — Wait for explicit "yes" / approval
3. **Act** — Perform the action
4. **Verify** — Confirm the action completed correctly

### Example: Submitting a Form

```
Agent: "Ready to submit your records request for Mass General Brigham,
        records from 2025-01-15 to today, sent to your Patient Gateway.
        Should I click Submit?"
       [waits for explicit confirmation]
       [clicks Submit]
       [reads confirmation page]
Agent: "Your request has been submitted. Confirmation number: [X]."
```

## Technical Notes

### Opening Native `<select>` Dropdowns

Native HTML `<select>` elements cannot be opened programmatically via JavaScript (`mousedown` event dispatch doesn't work reliably). The agent must click the element by coordinates using the Chrome automation tools.

Flow:
1. Use `javascript_tool` to get the element's bounding rectangle
2. Use `left_click` at the element's center coordinates
3. The native dropdown opens and the patient can click their choice

### Idle-Timeout Workaround

Medical portal SPAs (MyChart/Epic, etc.) have persistent background scripts that prevent Chrome's `document_idle` signal. When `screenshot`, `find`, or `read_page` fail with idle-timeout errors:

1. Use `javascript_tool` for reading page state (always works)
2. Use `left_click` by coordinates for UI interactions
3. Use `wait` between actions to allow page transitions

See [Phase 1 Navigation](phase-1-navigation.md) for details.

## Anti-Patterns

- **Silent actions** — Never change the UI without telling the patient first
- **Assuming selections** — Never pre-select a dropdown value; always let the patient choose
- **Skipping confirmation** — Never submit or enter data without explicit approval
- **Technical jargon** — Use plain language ("Select your organization" not "Choose the ROI form entity")
