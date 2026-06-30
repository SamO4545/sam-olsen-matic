# Cal.com Automated Email Notification System
## Product Requirements Document

**Owner:** Sam Olsen — Matic  
**Email:** samolsen@maticusa.com  
**Date:** June 30, 2026  
**Status:** Draft

---

## 1. Overview

This document defines requirements for an automated email notification system tied to Sam Olsen's Cal.com account. When Sam manually schedules a contact for a Matic call, the system automatically sends that contact a sequence of emails — from booking confirmation through meeting start — with no manual follow-up required. The system also ensures Sam's Cal.com booking page is discoverable on Google when someone searches "Sam Olsen Matic."

---

## 2. Problem Statement

Sam is scheduling prospects and clients for Matic calls. The current setup requires manual follow-up to remind attendees about upcoming meetings, which creates risk of no-shows and wastes time. Without automated touchpoints:

- Attendees forget about meetings scheduled days in advance
- There is no RSVP confirmation loop — Sam doesn't know if the person intends to show up
- Sam's booking page is not easily findable via Google search

The goal is to eliminate all manual follow-up through automated emails that keep attendees engaged from signup to meeting start.

---

## 3. Goals

| Goal | Metric |
|------|--------|
| Zero-touch email sequence for every booking | All 5 emails fire automatically for 100% of bookings |
| Attendee RSVP confirmation | YES/NO response buttons in booking confirmation email |
| Reduced no-show rate | Target: <15% no-show rate after system is live |
| Google discoverability | Cal.com page in top 5 results for "Sam Olsen Matic" within 4 weeks |

---

## 4. Email Notification Sequence

Five automated emails fire for every contact Sam adds to Cal.com.

### Email 1 — Booking Confirmation (Immediate)

**Trigger:** Contact is added/meeting is booked in Cal.com  
**Timing:** Sent immediately  
**Purpose:** Confirm the meeting, give full details, prompt RSVP

**Content:**
- Subject: `You're Confirmed: [Meeting Name] with Sam Olsen — [Date] at [Time]`
- Matic logo and branded header
- Meeting details (date, time, timezone, video/call link)
- **Large YES button** — "Yes, I'll be there" (confirms attendance)
- **Large NO button** — "No, I can't make it" (triggers reschedule flow or cancellation)
- One-click Add to Calendar links (Google Calendar, Apple Calendar, Outlook)
- Sam's contact info and short Matic intro

---

### Email 2 — 48-Hour Follow-Up Nudge (Conditional)

**Trigger:** 48 hours after booking is created  
**Condition:** Only send if the meeting is NOT scheduled for tomorrow or sooner  
**Purpose:** Re-engage, confirm they've added it to their calendar

**Content:**
- Subject: `Reminder: Your call with Sam Olsen is coming up — [Date]`
- Brief reminder of meeting purpose
- Meeting date/time/link repeated
- Repeat YES / NO RSVP buttons
- "Add to Calendar" links

**Technical note:** Cal.com's native workflows cannot evaluate the condition "is the meeting tomorrow?" at send time. This email requires Zapier or Make.com with a conditional filter. See Section 7.

---

### Email 3 — 24-Hour Reminder

**Trigger:** 24 hours before meeting start time  
**Purpose:** Final heads-up, ensure they're prepared

**Content:**
- Subject: `Tomorrow: Your call with Sam at Matic — [Time]`
- Meeting time and video/call link (prominent)
- One-sentence meeting agenda reminder
- YES / NO confirmation buttons

---

### Email 4 — 1-Hour Reminder

**Trigger:** 60 minutes before meeting start time  
**Purpose:** Near-term alert so the contact wraps up what they're doing

**Content:**
- Subject: `Starting in 1 hour: Your call with Sam Olsen`
- Meeting link front and center
- "Join now" button (links directly to video/call)
- Meeting time in bold

---

### Email 5 — 10–15 Minute Final Alert

**Trigger:** 10–15 minutes before meeting start time  
**Purpose:** "Meeting is starting now" nudge

**Content:**
- Subject: `Your call with Sam starts in 10 minutes — Join now`
- Single large "Join Meeting" button
- Meeting link
- Minimal text — mobile-optimized

---

## 5. Email Template Requirements

### Visual Design
- Matic logo in header
- Matic brand colors throughout
- Clean, minimal layout — readable on mobile in <5 seconds
- Font size: 16px minimum body text

### YES / NO RSVP Buttons
- Displayed in **Emails 1, 2, and 3** only
- Button size: minimum 180px wide × 60px tall
- YES button: green background, white text, bold — "YES, I'll be there"
- NO button: red background, white text, bold — "NO, I can't make it"
- Buttons must be linked:
  - YES → Cal.com confirmation URL or a thank-you landing page
  - NO → Cal.com reschedule/cancel page

### Add to Calendar
- Google Calendar link
- Apple Calendar (.ics download)
- Outlook Calendar link
- Include in Emails 1 and 2

### Compliance
- Unsubscribe link in every email footer (required by CAN-SPAM)
- Matic physical address in footer
- "You're receiving this because Sam Olsen at Matic scheduled a call with you" in footer

---

## 6. Cal.com Configuration

### Native Workflows (no third-party tools needed)

Cal.com's Workflows feature handles 4 of the 5 emails natively. Create or verify these workflows are active and applied to all event types:

| Workflow Name | Trigger | Action |
|---------------|---------|--------|
| Booking Confirmation | New booking created | Send email immediately |
| 24hr Reminder | 24 hours before event | Send email |
| 1hr Reminder | 1 hour before event | Send email |
| 15min Reminder | 15 minutes before event | Send email |

**How to create/edit a workflow in Cal.com:**
1. Go to Settings → Workflows (or the Workflows tab)
2. Click "+ New" to create or click the pencil icon to edit
3. Set the trigger (when it fires)
4. Add action: "Send email to attendee"
5. Paste the email template (HTML or rich text)
6. Under "Active on," select all relevant event types
7. Save and enable the toggle

---

## 7. Conditional 48-Hour Email — Automation Setup

### Why Third-Party Automation Is Needed

Cal.com workflows fire on fixed schedules relative to booking or event time. They cannot evaluate a condition like "only send if the meeting is more than 24 hours away." The 48-hour follow-up requires a conditional automation layer.

### Recommended: Zapier

**Estimated cost:** $0/mo on free tier for low booking volumes; $20/mo (Starter) for higher volume

**Steps to set up:**

1. Create a free Zapier account at zapier.com
2. Click **+ Create Zap**
3. **Trigger:** Search for "Cal.com" → select trigger "New Booking"
   - Connect your Cal.com account (OAuth)
   - Test trigger with a real/dummy booking
4. **Filter step:** Add a "Filter" action
   - Condition: `Meeting Start Time` is more than 1 day after `Created At`
   - Zapier field: `(meeting_start - booking_created) > 86400 seconds`
   - If condition is FALSE → Zap stops (no email sent for same-day/next-day meetings)
5. **Delay step:** Add a "Delay" action → "Delay for" → 48 hours
6. **Email step:** Add action → "Gmail" (or SendGrid) → "Send Email"
   - To: `{{attendee_email}}`
   - Subject: `Reminder: Your call with Sam Olsen is coming up — {{meeting_date}}`
   - Body: paste Email 2 template (HTML)
7. Turn the Zap on

### Alternative: Make.com

Make.com (formerly Integromat) offers the same capability with more advanced logic:
- Cost: $0–$9/mo
- Use the Cal.com module + Router + Delay module + Email module
- Slightly steeper to configure but more powerful for future expansions

---

## 8. SEO & Google Discoverability

**Goal:** When someone searches "Sam Olsen Matic" on Google, the Cal.com booking page appears in results.

### Step 1 — Cal.com Profile Optimization

In Cal.com Settings → Profile:
- **Username:** `sam-olsen-matic` (URL becomes `cal.com/sam-olsen-matic`)
- **Name:** Sam Olsen — Matic
- **Bio:** "I'm Sam Olsen at Matic. Book a call with me to learn how Matic can help your business with websites, online ordering, and AI automation."
- **Avatar:** Professional headshot (helps click-through rate in search results)

### Step 2 — Event Type Optimization

Each event type title and description should naturally include relevant keywords:
- Use descriptive titles (already done: "Matic Websites Onboarding," "Matic AI Discovery," etc.)
- Descriptions should mention Sam Olsen and Matic explicitly in the first sentence

### Step 3 — Custom Domain (Optional but Recommended)

Cal.com supports custom domains on paid plans:
- Set up: `cal.maticusa.com` instead of `cal.com/sam-olsen-matic`
- Improves brand authority and Google ranking for branded searches
- Requires adding a CNAME DNS record pointing to Cal.com's servers

### Step 4 — Google Business Profile

1. Go to google.com/business and search for "Matic" or create a new listing
2. Add Sam Olsen as a contact or point-of-contact
3. In the "Website" or "Booking" field, add the Cal.com URL
4. Add the Cal.com link to the business description

### Step 5 — LinkedIn Backlink

1. Go to Sam Olsen's LinkedIn profile
2. Add the Cal.com URL in the "Contact info" section as a website
3. In the "About" section, mention "Book a call at [cal.com/sam-olsen-matic]"
4. LinkedIn pages are indexed by Google — this creates a high-authority backlink

### Step 6 — Submit to Google Search Console (Optional)

If Matic has a website:
1. Add the Cal.com booking URL to Google Search Console as a property
2. Submit it for indexing
3. This accelerates how quickly Google finds and ranks the page

**Timeline:** Expect the Cal.com page to appear in Google results for "Sam Olsen Matic" within 2–4 weeks after completing Steps 1–4.

---

## 9. Implementation Phases

### Phase 1 — Cal.com Native Workflows (Day 1, ~2 hours)

- [ ] Verify or create Booking Confirmation workflow with custom email template
- [ ] Verify or update 24hr Reminder email template (add YES/NO buttons)
- [ ] Verify or update 1hr Reminder email template
- [ ] Verify or update 15min Reminder email template
- [ ] Apply all workflows to all active event types
- [ ] Send test booking to samolsen@maticusa.com and verify emails fire

### Phase 2 — Zapier Conditional 48hr Email (Days 2–3, ~2 hours)

- [ ] Create Zapier account
- [ ] Build Zap: Cal.com New Booking → Filter → Delay 48hrs → Send Email
- [ ] Test with a booking dated more than 2 days out (email should send)
- [ ] Test with a booking dated tomorrow (email should NOT send)
- [ ] Enable Zap

### Phase 3 — SEO Setup (Days 3–4, ~1 hour)

- [ ] Update Cal.com username to `sam-olsen-matic`
- [ ] Update Cal.com bio with keyword-rich description
- [ ] (Optional) Set up `cal.maticusa.com` custom domain
- [ ] Add Cal.com link to Google Business Profile
- [ ] Add Cal.com link to LinkedIn profile

### Phase 4 — Testing & Go-Live (Day 5)

- [ ] Create a test booking for a meeting 3+ days out
- [ ] Verify Email 1 (confirmation) fires immediately
- [ ] Verify Email 2 (48hr nudge) fires 48 hours later
- [ ] Verify Emails 3, 4, 5 fire at correct times before the meeting
- [ ] Check emails on mobile (iPhone + Android) for layout
- [ ] Check YES/NO buttons are clickable and link correctly
- [ ] Check "Add to Calendar" links open correctly
- [ ] Go live — apply to all real bookings

---

## 10. Success Metrics

| Metric | Target |
|--------|--------|
| All 5 emails fire for every booking | 100% |
| Email open rate | >40% |
| Attendee no-show rate | <15% |
| RSVP response rate (YES/NO) | >30% of recipients |
| Cal.com in Google top 10 for "Sam Olsen Matic" | Within 4 weeks |

---

## 11. Out of Scope (Future Phases)

- SMS/text reminders
- CRM sync (HubSpot, Salesforce)
- Automated rescheduling when attendee clicks NO
- Group meeting / webinar flows
- Post-meeting follow-up emails

---

## 12. Key Tools & Links

| Tool | Purpose | URL |
|------|---------|-----|
| Cal.com | Booking + native workflows | cal.com |
| Zapier | Conditional 48hr email | zapier.com |
| Make.com | Alternative to Zapier | make.com |
| Google Business | SEO + business listing | google.com/business |
| Google Search Console | Index submission | search.google.com/search-console |
