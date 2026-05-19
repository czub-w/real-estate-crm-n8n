# Real Estate CRM — Automated Appointment & SMS Workflow

An n8n automation system built for a real estate agency. When an agent books a meeting with a client, the system automatically handles data storage, confirmation emails, calendar events, CRM sync, and scheduled SMS reminders — with zero manual input after the form is submitted.

## How it works

The system consists of two workflows:

### 1. `Agent_nieruchomosci_PUBLIC.json` — New Appointment Flow

Triggered by a webhook (e.g. from a contact form or internal tool). On each new submission:

1. **Saves client data to Airtable** — name, phone number, address, meeting date/time, agent email
2. **Sends a confirmation email to the agent** via Gmail with full meeting details
3. **Creates a Google Calendar event** with the client address as location and the agent as attendee
4. **Syncs the event to the client's CRM** via API
5. **Waits 5 minutes**, then sends an **introductory SMS** to the client via sms8.io

### 2. `SMS_workflow_PUBLIC.json` — Scheduled SMS Reminders

Runs on a schedule (triggers at 09:02 and 18:47 daily). Checks Airtable for meetings that haven't received their reminder SMS yet:

- **At 18:47** — sends an evening SMS to clients with a meeting booked for that day (links agent's Instagram as social proof)
- **At 09:02** — sends a morning reminder SMS with the meeting time and address
- After each SMS is sent, the corresponding checkbox in Airtable is marked to prevent duplicate sends

## Tech stack

- **n8n** — workflow automation
- **Airtable** — database / CRM backend
- **sms8.io** — SMS gateway
- **Gmail** — confirmation emails
- **Google Calendar** — calendar event creation
- **Client CRM API** — external CRM sync via HTTP

## Setup

### Prerequisites

- n8n instance (self-hosted or cloud)
- Airtable account with a base containing the following fields:
  - `Imie` (string)
  - `Numer telefonu` (string)
  - `Adres` (string)
  - `Data` (dateTime)
  - `Mail Agenta` (string)
  - `Sms 1845` (boolean)
  - `Sms 900` (boolean)
- sms8.io account with an Android device connected
- Gmail account connected via OAuth2 in n8n
- Google Calendar connected via OAuth2 in n8n

### Configuration

Replace all placeholder values in the JSON files before importing:

| Placeholder | Description |
|---|---|
| `YOUR_AIRTABLE_BASE_ID` | Your Airtable base ID |
| `YOUR_AIRTABLE_TABLE_ID` | Your Airtable table ID |
| `YOUR_SMS8_API_KEY` | API key from sms8.io |
| `YOUR_DEVICE_ID` | Device ID from sms8.io |
| `YOUR_GOOGLE_CALENDAR_ID` | Google Calendar ID |
| `YOUR_CLIENT_CRM_API_KEY` | API key for the target CRM |
| `YOUR_CLIENT_CRM_API_ENDPOINT` | API endpoint for the target CRM |

### Import

1. Open n8n
2. Go to **Workflows → Import**
3. Import `Agent_nieruchomosci_PUBLIC.json` first
4. Import `SMS_workflow_PUBLIC.json`
5. Set up credentials for Airtable, Gmail, and Google Calendar in n8n
6. Activate both workflows

## File structure

```
├── Agent_nieruchomosci_PUBLIC.json   # New appointment webhook flow
├── SMS_workflow_PUBLIC.json          # Scheduled SMS reminder flow
└── README.md
```
