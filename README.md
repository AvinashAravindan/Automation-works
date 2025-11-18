# 🚀 Booking Automation Workflow — RaphaCure

Automated Workflow for Booking Updates → Google Sheets → Email Notifications

This project automates the full lifecycle of bookings triggered from a platform webhook, processing them through Google Apps Script, storing them in Google Sheets, and sending different email alerts (New / Updated / Cancelled) based on booking activity.

This README documents the full workflow setup, API structure, Apps Script logic, and Google Sheets integration.

## 📌 Project Overview

This automation pipeline performs:

1️⃣ Incoming Webhook

The platform sends booking events to a workflow (POST JSON).

2️⃣ Workflow → Apps Script API Call

The workflow captures booking data and posts it to a Google Apps Script Web App.

3️⃣ Google Apps Script Logic

Apps Script:

Normalizes booking types (e.g., ctmri → “Radiology Booking”)

Writes new bookings to Google Sheets

Updates existing rows (status, slot, etc.)

Handles cancellations

Tracks updated fields (changed fields array)

Never creates new unwanted sheet tabs

4️⃣ Workflow Routing

Based on api_response.action:

appended → New Booking email

updated → Updated Booking email

cancelled → Cancelled Booking email

5️⃣ Google Sheets as Database

Each booking type is written to its own tab:

OPD Consultation

Diagnostic Booking

Virtual Consultation

Package Booking

Radiology Booking

Misc (fallback)

## 🧩 Architecture Diagram
Platform Webhook  
      ↓  
Workflow Trigger  
      ↓  
API Node → Sends structured JSON → Apps Script  
      ↓  
Apps Script → Google Sheets (append/update)  
      ↓  
Workflow Logic Router  
      ├── New → Gmail Node    
      └── Cancelled → Gmail Node  

## 🔗 Workflow API Node Configuration
URL:
(https://script.google.com/macros/s/AKfycbyCmOtyrQbQ8wCLEco0oT1MpcJu32iAs0mjMzIZd6MlFRxz-7cKydFucqrOyhYT42vo/exec)

Method:
POST

Auth:
None

Headers:
Content-Type: application/json

Request Body:
{
  "booking_id": "{{body.id}}",
  "type": "{{body.type}}",
  "status": "{{body.status}}",
  "priority": "{{body.priority}}",
  "scheduled_date": "{{body.collection_1_date}}",

  "name": "{{body.user.first_name}} {{body.user.last_name}}",
  "age": "{{body.user.age}}",
  "gender": "{{body.user.gender}}",
  "phone": "{{body.user.phone}}",
  "email": "{{body.user.email}}",

  "doctor": "{{body.doctor}}",
  "vendor": "{{body.vendor}}",
  "package": "{{body.package}}",
  "client": "{{body.client.name}}",
  "assigned_to": "{{body.assignedTo.name}}",

  "changed_by": "{{body.changedBy.first_name}} {{body.changedBy.last_name}}"
}

## 🧠 Apps Script Logic (Core Features)

✔ Normalizes booking type (supports values like opd_consultation, package_booking, ctmri)
✔ Ensures sheet mapping consistency
✔ Prevents unwanted sheet creation
✔ Tracks old vs new values
✔ Writes timestamps (created_at, updated_at)
✔ Returns clear response JSON:

Example Response
{
  "ok": true,
  "action": "updated",
  "row": 15,
  "resolved_sheet": "Radiology Booking",
  "changed": [
    { "field": "status", "old": "Open", "new": "Cancelled" }
  ]
}

Includes cancellation details & user who triggered it

## 📊 Google Sheet Structure

Each tab has columns:

| booking_id | type | visit_type | doctor | vendor | name | age | gender | phone | scheduled_date | scheduled_slot | status | client | priority | assigned_to | created_at | updated_at | changed_fields |

Automated entries are appended or updated based on booking_id.

## 🛠️ Tech Stack

Google Apps Script (Web App)

Google Sheets

Custom Workflow Builder (API, Logic Router, Gmail Node)

Postman (for testing)

JavaScript / JSON

## 🧪 Testing Instructions

Use Postman to send:

New Booking
{
  "booking_id": "TEST-001",
  "type": "opd_consultation",
  "status": "Open"
}

Update Booking
{
  "booking_id": "TEST-001",
  "status": "Confirmed"
}

Cancel Booking
{
  "booking_id": "TEST-001",
  "status": "Cancelled"
}

## 📦 Folder Structure (recommended)
/project-root
│
├── README.md
├── apps_script/
│   └── code.gs
│
├── workflow/
│   ├── api_config.json
│   ├── logic_router_rules.md
│   └── email_templates/
│       ├── new_booking.html
│       ├── updated_booking.html
│       └── cancelled_booking.html
│
└── samples/
    ├── sample_payload.json
    ├── sample_responses.json
    └── postman_collection.json

## 🧑‍💻 Author

Avinash Ara
Automation Engineer — RaphaCure
Tech Stack: Google Apps Script, Workflows, Webhooks, API Automation, Cloud Integration
