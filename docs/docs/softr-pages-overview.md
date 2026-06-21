# Softr Pages Overview — EventHub

This document details each page in the EventHub Softr portal: its purpose, visibility rules, data source, and key configuration notes.

---

## 1. Home

**Purpose:** Public landing page listing all events.

**Visibility:** Everyone (public)

**Data Source:** Events table

**Key Blocks:**
- Search bar with **Status** filter only (Upcoming / Past)
- Event cards showing Image, Name, Description, Status
- "View Event" button linking to Event Details

**Notes:** Registrations and Feedback filters were originally (incorrectly) added to this page's search bar, which exposed other attendees' names and emails. These were removed — Home only shows public event data.

---

## 2. Events

**Purpose:** Full directory/listing page for browsing events (supplementary to Home).

**Visibility:** Everyone (public)

**Data Source:** Events table

---

## 3. Event Details

**Purpose:** Shows full information for a single event and provides the entry point for registration and feedback.

**Visibility:** Everyone (public)

**Data Source:** Events table (single record, via record ID in URL)

**Key Blocks:**
- Item Details block showing Name, Description, Date, Status, Capacity
- **Register Now** button → links to `/register` page
- **Feedback Form** block → visibility condition: `Status = Past`

---

## 4. Register

**Purpose:** One-click registration form for the logged-in user.

**Visibility:** Logged-in users only

**Form Settings:**
- "Who can submit this form" → Logged-in users only
- "A logged-in user can only submit once" → enabled (per event, scoped appropriately)

**Form Fields:**
| Field | Visibility | Source |
|---|---|---|
| Attendee Name | Visible (short text) | User types it, or pre-filled from Logged-in user > Name |
| Email | Visible, pre-filled | Default value: Logged-in user > Email |
| Event | Visible (dropdown) | Selected from Events table |
| Status | Hidden | Static value: `Confirmed` |
| Registration Date | Hidden | Default value: Current date & time |

**On Submit:** Redirects to **My Tickets**

**Key Lesson:** Hidden fields relying on "Logged-in user" session data were unreliable when tested from the Softr editor preview. Always test from the **published, live URL** in an incognito window, signed in with a real test account.

---

## 5. My Tickets

**Purpose:** Shows only the logged-in user's own registrations.

**Visibility:** Logged-in users only

**Data Source:** Registrations table

**Filter:** `Email` is `Logged-in user > Email` — critical for preventing one user from seeing another user's tickets.

**Search/Filter Bar:** Event + Status only (Registrations/Feedback filters removed — they leaked other users' data).

**Actions:** No "Add Record" button — registrations only happen via the Register page, never directly on this page.

---

## 6. Organiser Dashboard

**Purpose:** Lets the organiser view and manage all registrations across all events.

**Visibility:** Logged-in users → restricted to the **Organiser** user group only

**Data Source:** Registrations table — no filter (organiser sees everything)

**Actions:** Edit button on each row opens an "Update Record" modal allowing the organiser to change the `Status` field (e.g. Confirmed ↔ Waitlisted).

**Navigation:** The sidebar link to this page is also hidden from non-Organiser users.

---

## 7. Feedback Form

**Purpose:** Collects post-event feedback from attendees.

**Visibility:** Logged-in users only

**Embed Location:** Shown inside the Event Details page, conditionally visible only when the event's `Status = Past`.

**Form Fields:** Overall Rating, What did you enjoy?, What could be improved?, Would you attend again?

---

## User Groups

### Organiser
- **Type:** Condition-based
- **Rule:** `Email` is `[organiser's email address]`
- Used to restrict access to the Organiser Dashboard page and its navigation link.

---

## Authentication Notes

- Users are created through Softr's built-in Users system (Users tab), not manually inserted into Airtable — this is required for "Logged-in user" fields (Name, Email) to populate correctly in forms.
- Always **Publish** after making changes — the Softr editor preview does not reliably reflect live, authenticated behaviour.
