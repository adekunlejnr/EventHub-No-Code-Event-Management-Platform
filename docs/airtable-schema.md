# Airtable Schema — EventHub

This document describes the full Airtable base structure used to power the EventHub project. The base contains three linked tables: **Events**, **Registrations**, and **Feedback**.

---

## 1. Events

The source table for all event listings shown on the Softr portal.

| Field Name | Type | Notes |
|---|---|---|
| Name | Single line text | Primary field |
| Date | Date | Includes time |
| Location | Single line text | Displayed on Event Details page |
| Description | Long text | Displayed on Home and Event Details pages |
| Capacity | Number | Used to determine Confirmed vs Waitlisted status |
| Status | Single select | Options: `Upcoming`, `Past` — controls feedback form visibility |
| Image URL | URL | Cover image shown on event cards |

---

## 2. Registrations

Captures every attendee sign-up and links back to both the Events table and (indirectly) the Feedback table via shared event/email.

| Field Name | Type | Notes |
|---|---|---|
| Attendee Name | Single line text | Captured from Softr Register form |
| Email | Email | Captured from Softr Register form |
| Event | Linked record → Events | Selected via dropdown on Register page |
| Status | Single select | Options: `Confirmed`, `Waitlisted` |
| Registration Date | Date/time | Auto-set to current date/time on submission |
| Event Name | Lookup ← Events.Name | Used in confirmation emails |
| Event Date | Lookup ← Events.Date | Used in confirmation emails |
| Event Location | Lookup ← Events.Location | Used in confirmation emails |
| Last Modified | Last Modified Time (system field) | Tracks all fields — used by n8n to detect new/changed records |
| Email Sent | Checkbox | Set to `true` by n8n after a confirmation/waitlist email is sent, to prevent duplicates |
| Notes | Long text | Optional internal notes (organiser use) |

**Important:** The `Last Modified` field must be a true Airtable "Last Modified Time" field type (not a manually entered date). This is what n8n's Schedule Trigger + Search Records workflow polls against.

---

## 3. Feedback

Stores post-event feedback submitted by attendees, used by the AI summary report workflow.

| Field Name | Type | Notes |
|---|---|---|
| Attendee Email | Email | Identifies who submitted the feedback |
| Event | Linked record → Events | Selected on the Feedback Form |
| Rating | Number | 1–5 scale |
| Enjoyed | Long text | "What did you enjoy?" |
| Improve | Long text | "What could be improved?" |
| Return | Single select | "Would you attend again?" |
| Event Name | Lookup ← Events.Name | Used so reports can group/identify feedback per event without "Unknown Event" errors |

---

## Relationships Diagram (conceptual)

```
Events (1) ────────< Registrations (many)
   │
   └──────────────< Feedback (many)
```

- One Event can have many Registrations
- One Event can have many Feedback entries
- Registrations and Feedback are not directly linked to each other, but both reference the same Event and (where applicable) Email — this is how Workflow 2 correlates feedback per event.

---

## Why Lookup Fields Matter

Initially, Registrations and Feedback only stored the linked Event *record*, which displayed as an unreadable record ID in automation outputs (e.g. n8n showed "Unknown Event"). Adding **lookup fields** (`Event Name`, `Event Date`, `Event Location`) solved this by surfacing human-readable values directly on each record, which n8n and Softr could reference without extra lookups.
