# Call-Rail-Inbound-Calls-to-Update-into-GHL-CRM-N8N

This n8n workflow automates inbound call handling by sending call data into **GoHighLevel (GHL)** and managing:

- Contact creation or update
- Contact notes
- Opportunity creation or update

It is designed for businesses and agencies that want to automatically log inbound calls into their CRM pipeline.

---

## Overview

When an inbound call event is received through a **Webhook**, the workflow:

1. Receives the inbound call payload
2. Looks up a contact in **GoHighLevel**
3. Checks whether the contact exists / should be updated
4. Creates or updates the contact
5. Adds a note to the contact
6. Checks whether the contact already has an opportunity
7. Updates the existing opportunity or creates a new one

---

## Main Use Case

This automation is useful for:

- Call tracking systems like **CallRail**
- Sales pipelines
- Lead capture workflows
- CRM automation
- Inbound lead qualification systems
- Agency client onboarding automations

---

## Workflow Logic

## 1. Webhook Trigger

The workflow starts with a **Webhook** node.

### Purpose:
Receives inbound call data from an external system such as **CallRail**.

### Expected behavior:
When a call event is sent to this webhook, the workflow begins processing the caller information.

---

## 2. Get Contact from GoHighLevel

The workflow then checks **GoHighLevel** to retrieve an existing contact.

### Purpose:
Find the lead / caller inside GHL before deciding whether to update or create.

---

## 3. IF Condition: Contact Exists / Data Valid

An **IF** node determines whether the workflow should:

- update an existing contact
- or create a new one

### Logic:
If relevant contact data exists, it continues through the **update path**  
Otherwise, it moves through the **create path**

---

## 4A. Update Existing Contact

If the contact already exists, the workflow updates fields like:

- Email
- First Name
- Last Name
- Full Name
- Phone
- Tags

### Purpose:
Keeps CRM contact records fresh and accurate after each inbound call.

---

## 4B. Create or Update Contact

If the contact is not found or needs to be created, the workflow uses the **Create or Update Contact** node.

### Purpose:
Ensures every inbound caller gets saved in GHL.

---

## 5. Add Contact Note

After the contact is handled, the workflow sends an HTTP request to add a **note** inside the contact record.

### Purpose:
Store call-related notes directly in GoHighLevel.

### Example use cases:
- inbound call summary
- call source
- lead qualification notes
- sales team context

---

## 6. Fetch Existing Opportunities

The workflow then retrieves all opportunities from GHL.

### Purpose:
Check whether the contact already has an existing opportunity in the pipeline.

---

## 7. JavaScript Matching Logic

A **Code (JavaScript)** node compares:

- the current contact ID
- against all fetched opportunities

### Purpose:
Determine whether the contact already has an associated opportunity.

### Output:
The script returns:

- `matched: true` if an opportunity exists
- `matched: false` if no opportunity exists

It also extracts:

- opportunity ID
- pipeline ID
- stage ID
- assigned user
- opportunity status

---

## 8. IF Condition: Opportunity Exists

Another **IF** node checks whether a matching opportunity was found.

### If matched:
➡ update the existing opportunity

### If not matched:
➡ create a new opportunity

---

## 9A. Update Existing Opportunity

If the contact already has an opportunity, the workflow updates:

- Assigned user
- Opportunity name
- Pipeline
- Stage
- Status

### Purpose:
Keeps the pipeline accurate without creating duplicate opportunities.

---

## 9B. Create New Opportunity

If no opportunity exists, the workflow creates a brand-new one in GoHighLevel.

### Purpose:
Ensure every inbound lead gets inserted into the sales pipeline.

---

## Extra Opportunity Path

There is also an alternate **Create Opportunity** path after the contact creation branch.

### Purpose:
If a new contact is created, the workflow can immediately create a related opportunity for that lead.

---

# Tech Stack

This workflow uses:

- **n8n**
- **Webhook**
- **GoHighLevel (HighLevel)**
- **HTTP Request**
- **JavaScript Code Node**

---

# Requirements

Before using this workflow, make sure you have:

- an **n8n** instance
- a **GoHighLevel** account
- valid **HighLevel OAuth credentials**
- an external call tracking system (like **CallRail**) sending webhook data
- API access enabled for note creation

---

# Setup Instructions

## 1. Import the Workflow

- Open your n8n dashboard
- Click **Import**
- Upload the provided JSON file
- Save the workflow

---

## 2. Connect Credentials

Make sure these credentials are configured:

- **HighLevel OAuth2**
- any required **HTTP authorization headers**
- webhook source integration

---

## 3. Configure the Webhook

Copy the webhook URL from n8n and connect it to your inbound call source.

### Example integrations:
- CallRail webhook
- custom phone system
- telephony automation platform
- call event forwarding tool

---

## 4. Update Hardcoded Values

Before production use, replace the hardcoded values inside the workflow.

### Review and update:

- contact IDs
- pipeline IDs
- stage IDs
- opportunity naming logic
- note content
- authorization tokens

---

## 5. Test the Workflow

Run a test inbound call payload and confirm:

- webhook receives data
- contact is found or created
- note is added successfully
- opportunity is created or updated

---

## 6. Activate the Workflow

Once fully tested, activate the workflow in n8n.

---

# Important Security Warning

## Hardcoded Bearer Token Found

This workflow currently contains a **hardcoded Bearer token** inside HTTP Request nodes.

### You should immediately replace this with:
- n8n credentials
- environment variables
- secure header references

### Do NOT keep tokens directly inside workflow JSON when uploading to GitHub.

---

# Important Cleanup Recommendations

Before publishing this workflow publicly, you should remove or replace:

- access tokens
- real contact IDs
- real pipeline IDs
- internal GHL references
- private notes / placeholder values

---

# Example Workflow Flow

```text
Inbound Call Webhook
        ↓
Get Contact from GHL
        ↓
IF contact exists?
   ↙                ↘
Update Contact     Create / Update Contact
   ↓                      ↓
Add Note              Add Note
   ↓                      ↓
Get Opportunities   Create Opportunity
   ↓
Match Opportunity via JavaScript
   ↓
IF opportunity exists?
   ↙                ↘
Update Opportunity   Create Opportunity
