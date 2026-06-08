# MyChart/Epic — Mass General Brigham Patient Gateway

Tested: 2026-06-07

## Portal Details

- **URL:** `https://patientgateway.massgeneralbrigham.org/MyChart-PRD/Home/`
- **Platform:** MyChart (Epic), detected via `window.Epic`
- **Idle-timeout:** Yes — persistent background scripts prevent `document_idle`. Use `javascript_tool` for page reads, `left_click` by coordinates for UI interactions.

## Navigation Path

**Menu (≡) → My Record → Request Records**

The hamburger menu is in the upper-left corner. "Request Records" is near the bottom of the "My Record" section. Destination URL: `/MyChart-PRD/app/release-of-information`

### Other relevant menu items

| Menu Item | Path | Notes |
|---|---|---|
| Sharing Hub | `/AccountManagement/ShareMyRecord` | For sharing with other providers |
| Share Everywhere | `/shareeverywhere` | Likely TEFCA/Carequality |
| Document Center | `/Documents` | Existing documents (not request form) |
| My Documents | `/Documents/ViewDocuments` | Download existing records |

## Form Structure

The form is **two steps**: fill → review/consent → submit.

### Step 1: Fill the Request (`/app/release-of-information`)

Page title: **"Request a Copy of Your Health Record"**

Introductory notes displayed:
- Cannot request records of another adult patient
- If organization not listed, contact their medical records department
- Imaging CDs must be requested through radiology
- Reports will be viewable in Patient Gateway once finalized

#### Field 1: Organization / Send to

- **Legend:** "Which organization's health records are you requesting?"
- **Type:** `<select>` dropdown, labeled "Send to"
- **Element ID:** `EID-2-selectedForm-input-id`
- **Options (as of 2026-06-07):**
  - Dana Farber Cancer Institute (value: 23)
  - Mass General Brigham (value: 15)
  - Visiting Nurses Association (VNA) & Hospice of Cooley Dickinson (value: 50)

#### Field 2: Recipient

- **Legend:** "Who should we send your health record to?"
- **Type:** Radio buttons
- **Options:**
  - Me (`EID-2e`)
  - Someone else (`EID-2f`)

#### Field 3: Date Range

- **Legend:** "What dates do you want information from?"
- **Type:** Two text inputs (not date pickers)
- **From:** `EID-2-startDate-input-id` — format: MM/DD/YYYY
- **To:** `EID-2-endDate-input-id` — format: MM/DD/YYYY

#### Field 4: Locations

- **Legend:** "Which location(s) health records are you requesting? (Please select one or more)."
- **Type:** Checkboxes
- **Name:** `EID-2-requestMadeTo-input-id`
- **Options (partial list):**
  - Brigham & Women's Hospital
  - Brigham & Women's Faulkner Hospital
  - Cooley Dickinson Hospital/Medical Group
  - Martha's Vineyard Hospital
  - Massachusetts Eye and Ear
  - Mass General Brigham Community Physicians
  - Mass General Brigham Integrated Care - Salem, NH
  - Mass General Brigham - Home Care
  - Mass General Brigham - Urgent Care
  - Massachusetts General Hospital
  - McLean Hospital
  - Nantucket Cottage Hospital
  - Newton-Wellesley Hospital
  - North Shore Medical Center - Union
  - Pentucket Medical Associates
  - Salem Hospital
  - Spaulding Hospital for Continuing Medical Care - Cambridge
  - Spaulding Rehabilitation Center - Brighton
  - Spaulding Rehabilitation Hospital - Boston
  - (and possibly more)

#### Field 5: Record Types (Include)

- **Legend:** "Click the 'Include' button next to each item you are requesting to disclose."
- **Type:** Checkboxes, each labeled "Include" with adjacent description text
- **Options:**

| ID | Record Type |
|---|---|
| EID-214 | Abstract |
| EID-216 | Allergies & Immunizations |
| EID-218 | Anesthesia Record |
| EID-21a | Cardiology Reports |
| EID-21c | Consult Notes |
| EID-21e | Discharge Summary |
| EID-220 | Emergency Department Notes |
| EID-222 | History and Physical |
| EID-224 | Inpatient Progress Notes |
| EID-226 | Laboratory Reports |
| EID-228 | Medications |
| EID-22a | Office Visit Notes |
| EID-22c | Operative Notes |
| EID-22e | Pathology Reports |
| EID-230 | Procedure Notes |
| EID-232 | Radiology Reports |
| EID-234 | Other (requires additional information) |

#### Continue Button

Becomes active once required fields are filled. Navigates to the review page.

### Step 2: Review and Consent (`/app/release-of-information/request-review`)

Page title: **"Request a Copy of Your Health Record"** (same)

Section heading: **"Consent for release of information"**

Displays a summary of all selections from Step 1, then consent language:
- "I understand the health records I have requested may include sensitive information and should be reviewed prior to sharing with others."
- "I understand and agree that: The site I am requesting records from cannot control how the recipient uses or shares the information..."
- Authorization language about confidentiality and expiration

#### Buttons

- **Send request** — submits the request (CRITICAL confirmation gate)
- **Back** — returns to Step 1

## Assistant Interaction Flow

Following the [prompt-before-act pattern](../interaction-patterns.md):

1. **Navigate:** "I'll open the menu to find the records request form." → click hamburger → click Request Records
2. **Confirm form:** "I found the request form at Menu → My Record → Request Records. This is the Release of Information page. Right place?"
3. **Organization:** "Select your organization from the dropdown below." → click dropdown → wait for patient
4. **Recipient:** "Who should the records be sent to?" → wait for patient
5. **Dates:** "I'll set the date range to [start] through [end]." → confirm → fill dates
6. **Locations:** "Select which locations to include." → wait for patient (or confirm pre-selection)
7. **Record types:** "I'll include all record types except Other. OK?" → confirm → check boxes
8. **Continue:** "Everything looks good. Ready to continue to the review page?" → click Continue
9. **Review:** "Here's a summary of your request: [summary]. The consent says [key points]. Ready to submit?" → wait for explicit confirmation
10. **Submit:** Only on explicit "yes" → click Send request → read confirmation
