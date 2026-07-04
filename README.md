
#  Smart Bulk Email Sender — Loop, Wait & Summarize (n8n Workflow)

Most people build bulk email workflows and wonder why they crash halfway through.

The reason? They send everything at once. No control. No pacing. Just a flood of requests hitting Gmail's API — and then nothing works.

This workflow is built differently.

---

##  The Problem This Solves

When you loop over 100+ rows in n8n and fire emails back to back with no delay, three things happen:

- Gmail API gets overwhelmed and throws rate limit errors
- n8n execution crashes midway
- Some emails go out, some don't — and you have no idea which ones

This workflow fixes all three.

---

##  Workflow Overview

| Node | Type | Purpose |
|------|------|---------|
| When clicking 'Execute workflow' | Manual Trigger | Starts the workflow on demand |
| Get row(s) in sheet | Google Sheets | Reads all rows from the sheet |
| Loop Over Items | Loop | Processes one row at a time |
| Send a message | Gmail | Sends individual email per row |
| Wait | Delay | Pauses between each email (rate control) |
| Summarize | Data | Aggregates all sent records |
| Send a message1 | Gmail | Final summary email after all done |

---

##  Flow Diagram

```
Manual Trigger
      ↓
Get Rows from Google Sheet (4 items)
      ↓
Loop Over Items
  ↓ (loop — one item at a time)
  Send Email → Wait → back to loop
  ↓ (done — after all items)
Summarize
      ↓
Send Final Summary Email
```

<img width="1356" height="618" alt="image" src="https://github.com/user-attachments/assets/4021f86d-aed8-4af8-8f53-ccdccbb8728b" />


---

##  What Makes This Different

Most bulk email workflows skip the **Wait node** — that's the mistake.

The Wait node acts as a **cooldown period** between each email. It tells n8n: "Send one email, pause, then move to the next." This keeps Gmail API happy, prevents crashes, and ensures every single email goes out successfully.

The **Summarize node** then collects everything that was sent and passes a clean summary to the final email — so you know exactly what happened.

---

##  Features

-  Processes rows **one at a time** — no data overload
-  **Wait node** prevents Gmail API rate limit errors
-  Loop handles any number of rows automatically
-  Final **summary email** after all items are processed
- Execution time: **22.526s** for 4 items
- Successfully tested — all 4 emails sent ✔

---

## Tech Stack

- **n8n** — Workflow Automation
- **Google Sheets** — Data Source
- **Gmail** — Email Sending
- **Loop Over Items** — Iterative processing
- **Wait Node** — Rate limiting / delay control
- **Summarize Node** — Data aggregation

---

##  Input Data (Google Sheets)

| Field | Example |
|-------|---------|
| row_number | 2 |
| name | alina |
| email | alinazubair353@gmail.com |

---

##  Output

- Individual Gmail sent per row → `labelIds: SENT`
- Final summary email with all records combined
- Logs show each step: Loop → Send → Wait → repeat

---

##  How to Use

1. Import this workflow into your n8n instance
2. Connect **Google Sheets** via OAuth2
3. Connect **Gmail** via OAuth2
4. Set your Google Sheet ID in the "Get row(s)" node
5. Adjust the **Wait** node delay as needed (default: 1-2 seconds)
6. Click **Execute Workflow** — done!

---

##  Key Concept: Why the Wait Node Matters

```
Without Wait:        With Wait:
Row1 → Email ✓      Row1 → Email ✓
Row2 → Email ✓      [pause 1s]
Row3 → Email ✗      Row2 → Email ✓
Row4 → CRASH 💥     [pause 1s]
                    Row3 → Email ✓
                    [pause 1s]
                    Row4 → Email ✓ 🎉
```

---

## Author

**Alina Zubair**
AI Automation Developer | BS Computer Science (AI)
University of Agriculture, Gujrat, Pakistan

[![GitHub](https://img.shields.io/badge/GitHub-alinazubair353--AI-black?logo=github)](https://github.com/alinazubair353-AI)

---
