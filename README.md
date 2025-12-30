# n8n Workflow Examples

A collection of **n8n automation workflow examples** ready to import and use. This repository serves as a reference library of practical workflows demonstrating different n8n capabilities and integrations.

> **📌 This is an n8n project!**  
> [n8n](https://n8n.io/) is a powerful workflow automation tool that allows you to connect different services and automate tasks without writing code. This repository contains exportable workflows that you can import directly into your n8n instance.

![n8n](https://img.shields.io/badge/n8n-workflow-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white)
![Examples](https://img.shields.io/badge/Examples-8-blue?style=for-the-badge)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=for-the-badge&logo=google-sheets&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Discord](https://img.shields.io/badge/Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI-412991?style=for-the-badge&logo=openai&logoColor=white)

---

## 📚 Available Workflows

| # | Workflow | File | Description |
|---|----------|------|-------------|
| 1 | [Pokemon Scraper](#-pokemon-scraper) | `Pokemon Scraper.json` | Fetches Pokémon data from PokéAPI and stores it in Google Sheets |
| 2 | [Forms - T-Shirt](#-forms---t-shirt) | `Forms - T-Shirt.json` | Manages t-shirt orders from Google Forms with inventory control (Google Sheets) |
| 3 | [T-Shirts - PostgreSQL](#-t-shirts---postgresql) | `T-Shirts - PostgreSQL.json` | Manages t-shirt orders with PostgreSQL database for inventory |
| 4 | [Time Off](#-time-off) | `Time Off.json` | Employee vacation request system with HR approval via Discord (Form Trigger) |
| 5 | [Time Off Webhook](#-time-off-webhook) | `Time Off Webhook.json` | Employee vacation request system with HTTP Webhook API trigger |
| 6 | [Scraping - Course](#-scraping---curso) | `Scraping - Course.json` | Web scraping with AI extraction for course data from websites |
| 7 | [Google Maps - Scraping](#-google-maps---scraping) | `Google Maps - Scraping.json` | Business lead generation from Google Maps using Apify |
| 8 | [Wikipedia - Agent](#-wikipedia---agent) | `Wikipedia - Agent.json` + `n8n - chatbot/` | AI chatbot with Wikipedia search using LangChain agents |

---

## 🚀 How to Import a Workflow

1. Open n8n
2. Go to **Workflows** → **Import from File**
3. Select the desired `.json` file
4. Click on each node showing a credential error and configure them
5. Save and activate the workflow

---

# 🔴 Pokemon Scraper

An **n8n automation workflow** that extracts Pokémon data from the [PokéAPI](https://pokeapi.co/) and automatically stores it in Google Sheets, sending an email summary upon completion.

![Pokemon](https://img.shields.io/badge/PokéAPI-FFCB05?style=for-the-badge&logo=pokemon&logoColor=black)

## 📋 Description

This n8n workflow automates the following process:

1. **Read Pokémon IDs** from a Google Sheets document
2. **Filter** only records that don't have complete information
3. **Query the PokéAPI** to fetch detailed data for each Pokémon
4. **Extract relevant information**: ID, name, type, and sprites (images)
5. **Update Google Sheets** with the obtained data
6. **Send an email** with a summary of processed Pokémon

## 🔄 Workflow Flow

```
┌─────────┐    ┌──────────────┐    ┌────────────┐    ┌──────────────┐
│  Click  │───▶│ Google Sheet │───▶│   Filter   │───▶│  HTTP Request│
│ (Manual)│    │  (Read IDs)  │    │ (No data)  │    │  (PokéAPI)   │
└─────────┘    └──────────────┘    └────────────┘    └──────────────┘
                                                            │
                                                            ▼
┌─────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│ Send Email  │◀───│    Group     │◀───│ Google Sheet │◀───│  Set Fields  │
│   (Gmail)   │    │ (Aggregate)  │    │   (Update)   │    │  (Extract)   │
└─────────────┘    └──────────────┘    └──────────────┘    └──────────────┘
```

## 📊 Google Sheet Structure

Your spreadsheet must have the following columns:

| Column | Description |
|--------|-------------|
| `ID` | Numeric Pokémon ID (1, 2, 3, etc.) |
| `Name` | Pokémon name (will be auto-filled) |
| `Type` | Primary Pokémon type (will be auto-filled) |
| `Sprite Front` | Front image URL (will be auto-filled) |
| `Sprite Back` | Back image URL (will be auto-filled) |

## ⚙️ Credential Configuration

### 🔐 Required Credentials

| Parameter | Placeholder | Description |
|-----------|-------------|-------------|
| **Google Sheet ID** | `YOUR_GOOGLE_SHEET_ID` | Your Google Sheets document ID* |
| **Google Sheets Credential ID** | `YOUR_CREDENTIAL_ID` | Your Google Sheets OAuth2 credential ID |
| **Google Sheets Email** | `your-email@example.com` | Email associated with the credential |
| **Gmail Credential ID** | `YOUR_GMAIL_CREDENTIAL_ID` | Your Gmail OAuth2 credential ID |
| **Gmail Email** | `your-email@example.com` | Email associated with Gmail credential |
| **Recipient Email** | `your-email@example.com` | Email where you'll receive the summary |
| **Webhook ID** | `YOUR_WEBHOOK_ID` | Webhook ID (generated by n8n) |
| **Instance ID** | `YOUR_INSTANCE_ID` | Your n8n instance ID |

> **\*** The Google Sheet ID can be found in the document URL:  
> `https://docs.google.com/spreadsheets/d/`**`THIS_IS_THE_ID`**`/edit`

## 📦 Data Extracted per Pokémon

```json
{
  "id": 25,
  "name": "pikachu",
  "type": "electric",
  "photos": {
    "front": "https://raw.githubusercontent.com/.../25.png",
    "back": "https://raw.githubusercontent.com/.../back/25.png"
  }
}
```

---

# 👕 Forms - T-Shirt

An **n8n automation workflow** that manages t-shirt orders from a Google Form, verifies available stock using Google Sheets, and sends email notifications.

## 📋 Description

This n8n workflow automates the following process:

1. **Detect new responses** in a Google Form (Google Sheets Trigger)
2. **Read t-shirt requests** from Google Sheets
3. **Filter** unverified records
4. **Get current inventory** of t-shirt stock from Google Sheets
5. **Process orders** by checking availability per size
6. **Update responses** marking orders as verified
7. **Update inventory** reducing stock
8. **Send email** with a summary of changes made

## 🔄 Workflow Flow

```
┌──────────────────┐    ┌───────────────────┐    ┌────────────────┐    ┌──────────────┐
│ Google Sheets    │───▶│ Get T-Shirt       │───▶│ Filter No Data │───▶│  Aggregate   │
│ Trigger          │    │ Requests          │    │ Entries        │    │  Requests    │
└──────────────────┘    └───────────────────┘    └────────────────┘    └──────────────┘
                                                                              │
                                                                              ▼
┌──────────────────┐    ┌───────────────────┐    ┌────────────────┐    ┌──────────────┐
│ Update Stock     │◀───│ Aggregate Stock   │◀───│ Get Stock      │◀───│              │
│ (Code)           │    │                   │    │                │    │              │
└──────────────────┘    └───────────────────┘    └────────────────┘    └──────────────┘
        │
        ├───────────────────────────────────┐
        ▼                                   ▼
┌──────────────────┐                 ┌──────────────┐
│ Verify Order     │                 │ Update Stock │
│ (Google Sheets)  │                 │ (Inventory)  │
└──────────────────┘                 └──────────────┘
        │                                   │
        └───────────────┬───────────────────┘
                        ▼
              ┌──────────────────┐    ┌──────────────┐
              │ Merge Branches   │───▶│ Send Email   │
              │                  │    │ (Gmail)      │
              └──────────────────┘    └──────────────┘
```

## 📊 Google Sheets Structure

### Responses Sheet (T-Shirt - Responses)

| Column | Description |
|--------|-------------|
| `Marca temporal` | Form submission timestamp |
| `Dirección de correo electrónico` | Requester's email |
| `Name` | Requester's name |
| `Shirt size` | Requested size (S, M, L, XL, etc.) |
| `Aditional comments` | Additional comments |
| `Verified` | Verification status (TRUE/FALSE) |
| `Verification date` | Verification date |

### Inventory Sheet (T-Shirt Stock)

| Column | Description |
|--------|-------------|
| `Product` | Product name |
| `Size` | Size (S, M, L, XL, etc.) |
| `Stock` | Available quantity |

## ⚙️ Credential Configuration

### 🔐 Required Credentials

| Parameter | Placeholder | Description |
|-----------|-------------|-------------|
| **Google Sheet Responses ID** | `YOUR_GOOGLE_SHEET_RESPONSES_ID` | Form responses document ID* |
| **Google Sheet Stock ID** | `YOUR_GOOGLE_SHEET_STOCK_ID` | Inventory document ID* |
| **Google Sheets Credential ID** | `YOUR_GOOGLE_SHEETS_CREDENTIAL_ID` | Google Sheets OAuth2 credential ID |
| **Google Sheets Trigger Credential ID** | `YOUR_GOOGLE_SHEETS_TRIGGER_CREDENTIAL_ID` | Trigger OAuth2 credential ID |
| **Gmail Credential ID** | `YOUR_GMAIL_CREDENTIAL_ID` | Gmail OAuth2 credential ID |
| **Email** | `your-email@example.com` | Email for credentials and recipient |
| **Webhook ID** | `YOUR_WEBHOOK_ID` | Webhook ID (generated by n8n) |
| **Instance ID** | `YOUR_INSTANCE_ID` | Your n8n instance ID |

> **\*** The Google Sheet ID can be found in the document URL:  
> `https://docs.google.com/spreadsheets/d/`**`THIS_IS_THE_ID`**`/edit`

---

# 🐘 T-Shirts - PostgreSQL

An **n8n automation workflow** that manages t-shirt orders from a Google Form, verifies available stock using a **PostgreSQL database**, and sends email notifications. This is an alternative to the Google Sheets version, demonstrating database integration.

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)

## 📋 Description

This n8n workflow automates the following process:

1. **Detect new responses** in a Google Form (Google Sheets Trigger)
2. **Filter** unverified records
3. **Loop over each request** processing them one by one
4. **Query PostgreSQL** to check inventory by size
5. **Verify stock availability** and branch accordingly
6. **Update PostgreSQL** reducing stock for successful orders
7. **Update Google Sheets** marking orders as verified or rejected
8. **Send email** with a summary of processed requests

## 🔄 Workflow Flow

```
┌──────────────────┐    ┌────────────────┐    ┌──────────────────┐
│ Google Sheets    │───▶│ Filter No Data │───▶│ Loop Over New    │
│ Trigger          │    │ Entries        │    │ Requests         │
└──────────────────┘    └────────────────┘    └──────────────────┘
                                                      │
                        ┌─────────────────────────────┼─────────────────────┐
                        │                             │                     │
                        ▼                             ▼                     │
              ┌──────────────────┐         ┌──────────────────┐            │
              │ Aggregate Work   │         │ Get Inventory    │            │
              │                  │         │ By Size (SQL)    │            │
              └──────────────────┘         └──────────────────┘            │
                        │                             │                     │
                        ▼                             ▼                     │
              ┌──────────────────┐         ┌──────────────────┐            │
              │ Send Email       │         │ Have Stock?      │            │
              │ (Gmail)          │         │ (IF Condition)   │            │
              └──────────────────┘         └──────────────────┘            │
                                                  │    │                   │
                                    ┌─────────────┘    └──────────┐        │
                                    ▼                             ▼        │
                          ┌──────────────────┐         ┌──────────────────┐│
                          │ Subtract Stock   │         │ Update Answers   ││
                          │ (Set Node)       │         │ Without Stock    ││
                          └──────────────────┘         └──────────────────┘│
                                    │                             │        │
                                    ▼                             │        │
                          ┌──────────────────┐                    │        │
                          │ Update Stock DB  │                    │        │
                          │ (PostgreSQL)     │                    │        │
                          └──────────────────┘                    │        │
                                    │                             │        │
                                    ▼                             │        │
                          ┌──────────────────┐                    │        │
                          │ Update Answers   │                    │        │
                          │ (Google Sheets)  │                    │        │
                          └──────────────────┘                    │        │
                                    │                             │        │
                                    ▼                             ▼        │
                          ┌──────────────────┐◀───────────────────┘        │
                          │ Loop End         │─────────────────────────────┘
                          └──────────────────┘
```

## 📊 Data Structures

### Google Sheets - Responses (T-Shirt - Responses)

| Column | Description |
|--------|-------------|
| `Marca temporal` | Form submission timestamp |
| `Dirección de correo electrónico` | Requester's email |
| `Name` | Requester's name |
| `Shirt size` | Requested size (S, M, L, XL, etc.) |
| `Aditional comments` | Additional comments |
| `Verified` | Verification status (TRUE/FALSE) |
| `Verification date` | Verification date |

### PostgreSQL - Inventory Table

```sql
CREATE TABLE inventory (
    id SERIAL PRIMARY KEY,
    product_name VARCHAR(255),
    size VARCHAR(10),
    in_stock INTEGER
);
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `product_name` | VARCHAR | Product name |
| `size` | VARCHAR | Size (S, M, L, XL, etc.) |
| `in_stock` | INTEGER | Available quantity |

## ⚙️ Credential Configuration

### 🔐 Required Credentials

| Parameter | Placeholder | Description |
|-----------|-------------|-------------|
| **Google Sheet Responses ID** | `YOUR_GOOGLE_SHEET_RESPONSES_ID` | Form responses document ID* |
| **Google Sheets Credential ID** | `YOUR_GOOGLE_SHEETS_CREDENTIAL_ID` | Google Sheets OAuth2 credential ID |
| **Google Sheets Trigger Credential ID** | `YOUR_GOOGLE_SHEETS_TRIGGER_CREDENTIAL_ID` | Trigger OAuth2 credential ID |
| **PostgreSQL Credential ID** | `YOUR_POSTGRES_CREDENTIAL_ID` | PostgreSQL credential ID |
| **PostgreSQL Database Name** | `your-database-name` | Name for your PostgreSQL connection |
| **Gmail Credential ID** | `YOUR_GMAIL_CREDENTIAL_ID` | Gmail OAuth2 credential ID |
| **Email** | `your-email@example.com` | Email for credentials and recipient |
| **Webhook ID** | `YOUR_WEBHOOK_ID` | Webhook ID (generated by n8n) |
| **Instance ID** | `YOUR_INSTANCE_ID` | Your n8n instance ID |

> **\*** The Google Sheet ID can be found in the document URL:  
> `https://docs.google.com/spreadsheets/d/`**`THIS_IS_THE_ID`**`/edit`

### PostgreSQL Connection

To configure PostgreSQL in n8n, you'll need:
- **Host**: Your database host (e.g., `db.neon.tech`)
- **Database**: Database name
- **User**: Database username
- **Password**: Database password
- **Port**: Usually `5432`
- **SSL**: Enable if required (recommended for cloud databases)

---

# 🏖️ Time Off

An **n8n automation workflow** that manages employee vacation/time-off requests with a complete approval flow. Features a custom form, multiple validations, Discord notifications to HR, and automatic calendar event creation.

![Discord](https://img.shields.io/badge/Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Google Calendar](https://img.shields.io/badge/Google%20Calendar-4285F4?style=for-the-badge&logo=google-calendar&logoColor=white)

## 📋 Description

This n8n workflow automates the complete vacation request process:

1. **Employee submits request** via n8n Form Trigger (custom styled form)
2. **Validate request timing** (must be 7+ days in advance)
3. **Validate date range** (end date must be after start date)
4. **Check available days** in PostgreSQL database
5. **Notify HR via Discord** with request details and approval link
6. **Wait for HR response** (48-hour timeout with approval form)
7. **If approved**: Create Google Calendar event + Update database + Send confirmation email
8. **If rejected**: Send rejection email with reason

## 🔄 Workflow Flow

```
┌──────────────────┐    ┌────────────────────┐    ┌────────────────────┐
│ Form Trigger     │───▶│ Get Request        │───▶│ Is It Higher Than  │
│ (Time Request)   │    │ Variables          │    │ 7 Days?            │
└──────────────────┘    └────────────────────┘    └────────────────────┘
                                                          │
                                    ┌─────────────────────┴─────────────────────┐
                                    │ YES                                   NO  │
                                    ▼                                           ▼
                          ┌──────────────────┐                    ┌──────────────────┐
                          │ Dates Ok?        │                    │ Add Error -      │
                          │ (End > Start)    │                    │ Request Time     │
                          └──────────────────┘                    └──────────────────┘
                                    │                                           │
                    ┌───────────────┴───────────────┐                          │
                    │ YES                       NO  │                          │
                    ▼                               ▼                          │
          ┌──────────────────┐         ┌──────────────────┐                   │
          │ Get Free Days    │         │ Add Error -      │                   │
          │ (PostgreSQL)     │         │ Wrong Dates      │                   │
          └──────────────────┘         └──────────────────┘                   │
                    │                               │                          │
                    ▼                               │                          │
          ┌──────────────────┐                     │                          │
          │ Have Enough      │                     │                          │
          │ Vacations?       │                     │                          │
          └──────────────────┘                     │                          │
                    │                               │                          │
        ┌───────────┴───────────┐                  │                          │
        │ YES               NO  │                  │                          │
        ▼                       ▼                  │                          │
┌──────────────┐    ┌──────────────────┐          │                          │
│ Discord      │    │ Add Error -      │          │                          │
│ (Notify HR)  │    │ Not Enough Days  │          │                          │
└──────────────┘    └──────────────────┘          │                          │
        │                       │                  │                          │
        ▼                       └──────────────────┴──────────────────────────┤
┌──────────────────┐                                                          │
│ Wait HR Response │                                                          │
│ (48 Hours Form)  │                                                          │
└──────────────────┘                                                          │
        │                                                                     │
        ▼                                                                     │
┌──────────────────┐                                                          │
│ Request          │                                                          │
│ Approved?        │                                                          │
└──────────────────┘                                                          │
        │                                                                     │
    ┌───┴───────────────┐                                                     │
    │ YES           NO  │                                                     │
    ▼                   ▼                                                     │
┌────────────┐  ┌──────────────┐                                             │
│ Create     │  │ Add Error -  │                                             │
│ Calendar   │  │ Rejected     │                                             │
│ Event      │  └──────────────┘                                             │
└────────────┘          │                                                     │
    │                   ▼                                                     │
    ▼           ┌──────────────┐                    ┌──────────────────┐     │
┌────────────┐  │ Send HR      │◀───────────────────│ Send Reject      │◀────┘
│ Update     │  │ Reject Email │                    │ Message          │
│ Vacation   │  └──────────────┘                    └──────────────────┘
│ Days (DB)  │
└────────────┘
    │
    ▼
┌────────────┐
│ Send HR    │
│ Approve    │
│ Email      │
└────────────┘
```

## 📊 Data Structures

### n8n Form Fields (Request Form)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `Full Name` | Text | Yes | Employee's full name |
| `Email` | Email | Yes | Employee's email address |
| `Start date` | Date | Yes | First day of time off |
| `End date` | Date | Yes | Last day of time off |
| `Comments` | Textarea | No | Additional request comments |

### PostgreSQL - Days Off Table

```sql
CREATE TABLE days_off (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE,
    vacation_days INTEGER,
    sick_days INTEGER
);
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `email` | VARCHAR | Employee's email (unique) |
| `vacation_days` | INTEGER | Available vacation days |
| `sick_days` | INTEGER | Available sick days |

## ⚙️ Credential Configuration

### 🔐 Required Credentials

| Parameter | Placeholder | Description |
|-----------|-------------|-------------|
| **PostgreSQL Credential ID** | `YOUR_POSTGRES_CREDENTIAL_ID` | PostgreSQL credential ID |
| **PostgreSQL Database Name** | `your-database-name` | Name for your PostgreSQL connection |
| **Gmail Credential ID** | `YOUR_GMAIL_CREDENTIAL_ID` | Gmail OAuth2 credential ID |
| **Google Calendar Credential ID** | `YOUR_GOOGLE_CALENDAR_CREDENTIAL_ID` | Google Calendar OAuth2 credential ID |
| **Calendar Email** | `your-email@example.com` | Calendar to create events in |
| **Form Webhook ID** | `YOUR_FORM_WEBHOOK_ID` | Form Trigger webhook ID |
| **Discord Webhook ID** | `YOUR_DISCORD_WEBHOOK_ID` | Discord notification webhook ID |
| **Wait Webhook ID** | `YOUR_WAIT_WEBHOOK_ID` | Wait node webhook ID |
| **Gmail Webhook ID** | `YOUR_GMAIL_WEBHOOK_ID` | Gmail webhook ID |
| **Instance ID** | `YOUR_INSTANCE_ID` | Your n8n instance ID |

### Discord Configuration

This workflow uses Discord webhook authentication. To set it up:
1. Go to your Discord server settings
2. Navigate to **Integrations** → **Webhooks**
3. Create a new webhook for your HR channel
4. Copy the webhook URL and configure it in the Discord node

### Validation Rules

The workflow includes these built-in validations:
- ⏰ Request must be submitted at least **7 days** before start date
- 📅 End date must be **after** start date
- 🏖️ Employee must have **enough vacation days** available
- ⏳ HR has **48 hours** to respond before request times out

---

# 🔗 Time Off Webhook

An **n8n automation workflow** similar to Time Off, but triggered via an **HTTP Webhook API** instead of a form. This version is ideal for integrating vacation requests from external systems, mobile apps, or custom frontends.

![Webhook](https://img.shields.io/badge/Webhook-API-orange?style=for-the-badge)
![Discord](https://img.shields.io/badge/Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Google Calendar](https://img.shields.io/badge/Google%20Calendar-4285F4?style=for-the-badge&logo=google-calendar&logoColor=white)

## 📋 Description

This n8n workflow provides an API endpoint for vacation requests:

1. **Receive POST request** via HTTP Webhook with header authentication
2. **Validate request body** using JavaScript (name, email, dates format)
3. **Return HTTP response** (200 OK or 400 Bad Request with errors)
4. **Validate request timing** (must be 7+ days in advance)
5. **Validate date range** (end date must be after start date)
6. **Check available days** in PostgreSQL database
7. **Notify HR via Discord** with request details and approval link
8. **Wait for HR response** (48-hour timeout via webhook callback)
9. **If approved**: Create Google Calendar event + Update database + Send confirmation email
10. **If rejected**: Send rejection email with reason

## 🔄 Workflow Flow

```
┌──────────────────┐    ┌────────────────────┐    ┌────────────────────┐
│ Webhook POST     │───▶│ Validate Webhook   │───▶│ Has Necessary      │
│ (API Trigger)    │    │ Data (JavaScript)  │    │ Fields?            │
└──────────────────┘    └────────────────────┘    └────────────────────┘
                                                          │
                                    ┌─────────────────────┴─────────────────────┐
                                    │ YES                                   NO  │
                                    ▼                                           ▼
                          ┌──────────────────┐                    ┌──────────────────┐
                          │ Respond 200 OK   │                    │ Respond 400      │
                          │                  │                    │ Bad Request      │
                          └──────────────────┘                    └──────────────────┘
                                    │
                                    ▼
                          ┌──────────────────┐
                          │ Get Request      │
                          │ Variables        │
                          └──────────────────┘
                                    │
                                    ▼
                    (Same flow as Time Off workflow)
                                    │
                                    ▼
                          ┌──────────────────┐
                          │ Validations →    │
                          │ HR Approval →    │
                          │ Calendar + Email │
                          └──────────────────┘
```

## 📊 API Specification

### Endpoint

```
POST /webhook/vacation/ask/form/001
```

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `Authorization` | Yes | Header authentication token |

### Request Body

```json
{
  "Full Name": "John Doe",
  "Email": "john.doe@company.com",
  "Start date": "2025-01-15",
  "End date": "2025-01-20",
  "Comments": "Family vacation (optional)"
}
```

### Response - Success (200)

```json
{
  "valid": true,
  "status": 200,
  "data": {
    "fullName": "John Doe",
    "email": "john.doe@company.com",
    "startDate": "2025-01-15",
    "endDate": "2025-01-20",
    "comments": "Family vacation"
  }
}
```

### Response - Error (400)

```json
{
  "valid": false,
  "status": 400,
  "errors": [
    "Name is required.",
    "Start date needs to have a format: YYYY-MM-DD."
  ]
}
```

### Validation Rules (API Level)

- ✅ `Full Name` - Required, non-empty
- ✅ `Email` - Required, valid email format
- ✅ `Start date` - Required, format YYYY-MM-DD
- ✅ `End date` - Required, format YYYY-MM-DD, must be after start date
- ✅ `Comments` - Optional, max 500 characters

## 📊 Data Structures

### PostgreSQL - Days Off Table

```sql
CREATE TABLE days_off (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE,
    vacation_days INTEGER,
    sick_days INTEGER
);
```

## ⚙️ Credential Configuration

### 🔐 Required Credentials

| Parameter | Placeholder | Description |
|-----------|-------------|-------------|
| **Header Auth Credential ID** | `YOUR_HEADER_AUTH_CREDENTIAL_ID` | HTTP Header authentication credential |
| **PostgreSQL Credential ID** | `YOUR_POSTGRES_CREDENTIAL_ID` | PostgreSQL credential ID |
| **PostgreSQL Database Name** | `your-database-name` | Name for your PostgreSQL connection |
| **Gmail Credential ID** | `YOUR_GMAIL_CREDENTIAL_ID` | Gmail OAuth2 credential ID |
| **Google Calendar Credential ID** | `YOUR_GOOGLE_CALENDAR_CREDENTIAL_ID` | Google Calendar OAuth2 credential ID |
| **Calendar Email** | `your-email@example.com` | Calendar to create events in |
| **HTTP Webhook ID** | `YOUR_HTTP_WEBHOOK_ID` | Main webhook endpoint ID |
| **Form Webhook ID** | `YOUR_FORM_WEBHOOK_ID` | Form Trigger webhook ID (disabled) |
| **Discord Webhook ID** | `YOUR_DISCORD_WEBHOOK_ID` | Discord notification webhook ID |
| **Wait Webhook ID** | `YOUR_WAIT_WEBHOOK_ID` | Wait node webhook ID |
| **Gmail Webhook ID** | `YOUR_GMAIL_WEBHOOK_ID` | Gmail webhook ID |
| **Instance ID** | `YOUR_INSTANCE_ID` | Your n8n instance ID |

### Header Authentication Setup

1. In n8n, go to **Credentials** → **New**
2. Select **Header Auth**
3. Configure:
   - **Name**: `Header Auth Secret`
   - **Header Name**: Your header name (e.g., `X-API-Key`)
   - **Header Value**: Your secret token

### Differences from Time Off (Form Version)

| Feature | Time Off | Time Off Webhook |
|---------|----------|------------------|
| **Trigger** | n8n Form | HTTP POST Webhook |
| **Input Validation** | n8n Form validation | JavaScript validation |
| **Response** | Form submission page | JSON response |
| **Authentication** | None (public form) | Header authentication |
| **HR Approval Wait** | Form-based | Webhook callback |
| **Use Case** | Employee self-service | System integration |

---

# 🕷️ Scraping - Course

An **n8n automation workflow** that performs **web scraping with AI-powered data extraction** for online courses. It scrapes course websites, uses **OpenAI** to extract structured information, fetches additional data from Udemy using **Firecrawl**, and stores everything in **Google Sheets** and **Google Docs**.

![OpenAI](https://img.shields.io/badge/OpenAI-412991?style=for-the-badge&logo=openai&logoColor=white)
![Firecrawl](https://img.shields.io/badge/Firecrawl-FF6B35?style=for-the-badge)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=for-the-badge&logo=google-sheets&logoColor=white)
![Google Docs](https://img.shields.io/badge/Google%20Docs-4285F4?style=for-the-badge&logo=google-docs&logoColor=white)

## 📋 Description

This n8n workflow automates the process of extracting course information from websites:

1. **Read websites list** from Google Sheets (can run on schedule or manually)
2. **Filter websites** that have valid URLs
3. **Fetch HTML** from each website
4. **Convert to Markdown** for easier AI processing
5. **Send to OpenAI** (o3-mini) to extract structured course data
6. **Parse and aggregate** the extracted information
7. **Update Google Sheets** with course details (name, instructor, URLs, description)
8. **Scrape Udemy** using Firecrawl for additional course details
9. **Create Google Doc** for each course syllabus
10. **Use OpenAI** again to format syllabus content
11. **Update Google Doc** with formatted syllabus

## 🔄 Workflow Flow

```
┌──────────────────┐    ┌──────────────────┐    ┌────────────────┐
│ Schedule/Manual  │───▶│ Get Websites     │───▶│ Filter Valid   │
│ Trigger          │    │ (Google Sheets)  │    │ URLs           │
└──────────────────┘    └──────────────────┘    └────────────────┘
                                                        │
                                                        ▼
┌──────────────────┐    ┌──────────────────┐    ┌────────────────┐
│ OpenAI Extract   │◀───│ Convert to       │◀───│ HTTP Request   │
│ Course Data      │    │ Markdown         │    │ (Get HTML)     │
└──────────────────┘    └──────────────────┘    └────────────────┘
        │
        ▼
┌──────────────────┐    ┌──────────────────┐    ┌────────────────┐
│ Parse JSON &     │───▶│ Split Courses    │───▶│ Update Google  │
│ Aggregate        │    │ (Loop)           │    │ Sheets         │
└──────────────────┘    └──────────────────┘    └────────────────┘
                                                        │
                                                        ▼
┌──────────────────┐    ┌──────────────────┐    ┌────────────────┐
│ OpenAI Format    │◀───│ Create Google    │◀───│ Firecrawl      │
│ Syllabus         │    │ Doc              │    │ (Scrape Udemy) │
└──────────────────┘    └──────────────────┘    └────────────────┘
        │
        ▼
┌──────────────────┐    ┌──────────────────┐
│ Update Google    │───▶│ Update Syllabus  │
│ Doc Content      │    │ URL in Sheets    │
└──────────────────┘    └──────────────────┘
```

## 📊 Data Structures

### Google Sheets - Websites to Search

| Column | Description |
|--------|-------------|
| `Website` | URL of the website to scrape |

### Google Sheets - Results

| Column | Description |
|--------|-------------|
| `Course name` | Name of the course |
| `Teacher` | Instructor's name |
| `DevTalles` | DevTalles course URL |
| `Udemy` | Udemy course URL |
| `Description` | Course description |
| `GoogleDocs - Syllabus` | Link to generated syllabus document |

### AI-Extracted Data Structure

```json
{
  "courseName": "Course Title",
  "instructor": "Instructor Name",
  "devtallesUrl": "https://devtalles.com/...",
  "udemyUrl": "https://udemy.com/...",
  "description": "Course description...",
  "category": "Programming",
  "additionalInformation": "Extra details..."
}
```

## ⚙️ Credential Configuration

### 🔐 Required Credentials

| Parameter | Placeholder | Description |
|-----------|-------------|-------------|
| **Google Sheet ID** | `YOUR_GOOGLE_SHEET_ID` | Google Sheets document ID* |
| **Google Sheets Credential ID** | `YOUR_GOOGLE_SHEETS_CREDENTIAL_ID` | Google Sheets OAuth2 credential ID |
| **OpenAI Credential ID** | `YOUR_OPENAI_CREDENTIAL_ID` | OpenAI API credential ID |
| **Firecrawl Credential ID** | `YOUR_FIRECRAWL_CREDENTIAL_ID` | Firecrawl API credential ID |
| **Firecrawl API Key** | `YOUR_FIRECRAWL_API_KEY` | Firecrawl API key (for HTTP requests) |
| **Google Docs Credential ID** | `YOUR_GOOGLE_DOCS_CREDENTIAL_ID` | Google Docs OAuth2 credential ID |
| **Google Drive Folder ID** | `YOUR_GOOGLE_DRIVE_FOLDER_ID` | Folder ID for created documents |
| **Instance ID** | `YOUR_INSTANCE_ID` | Your n8n instance ID |

> **\*** The Google Sheet ID can be found in the document URL:  
> `https://docs.google.com/spreadsheets/d/`**`THIS_IS_THE_ID`**`/edit`

### OpenAI Configuration

This workflow uses the **o3-mini** model for:
1. Extracting structured course data from scraped content
2. Formatting syllabus content for Google Docs

Configure in n8n:
1. Go to **Credentials** → **New**
2. Select **OpenAI**
3. Add your OpenAI API key

### Firecrawl Configuration

[Firecrawl](https://firecrawl.dev/) is used to scrape Udemy pages (which have anti-scraping protection):

1. Sign up at [firecrawl.dev](https://firecrawl.dev/)
2. Get your API key
3. Configure in n8n Credentials

### Google Drive Folder

The workflow creates Google Docs for each course syllabus. Configure:
- Create a folder in Google Drive
- Get the folder ID from the URL: `https://drive.google.com/drive/folders/`**`FOLDER_ID`**
- Update the `folderId` parameter

### Features

- 🕒 **Scheduled execution** - Can run automatically on a schedule
- 🔄 **Manual trigger** - Also supports manual execution
- 🤖 **AI-powered extraction** - Uses OpenAI for intelligent data extraction
- 🛡️ **Anti-scraping bypass** - Firecrawl handles protected sites like Udemy
- 📄 **Document generation** - Automatically creates formatted Google Docs

---

# 🗺️ Google Maps - Scraping

An **n8n automation workflow** that scrapes **Google Maps** for business leads using **Apify**, extracts contact information from business websites using **Firecrawl**, and stores all data in **Google Sheets**. Perfect for lead generation and market research.

![Apify](https://img.shields.io/badge/Apify-00C7B7?style=for-the-badge)
![Firecrawl](https://img.shields.io/badge/Firecrawl-FF6B35?style=for-the-badge)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=for-the-badge&logo=google-sheets&logoColor=white)
![Google Maps](https://img.shields.io/badge/Google%20Maps-4285F4?style=for-the-badge&logo=google-maps&logoColor=white)

## 📋 Description

This n8n workflow automates business lead generation from Google Maps:

1. **Read search queries** from Google Sheets (Query + Location)
2. **Start Apify scraping job** using the Google Places Crawler
3. **Wait for job completion** with polling loop
4. **Check scraping status** until SUCCEEDED
5. **Fetch scraped results** from Apify dataset
6. **Save business data** to Google Sheets (title, category, address, phone, website)
7. **Filter businesses** that have websites
8. **Scrape websites** using Firecrawl for contact information
9. **Extract contact details** (emails, social media, etc.)
10. **Save contact information** to Google Sheets

## 🔄 Workflow Flow

```
┌──────────────────┐    ┌──────────────────┐    ┌────────────────────┐
│ Schedule/Manual  │───▶│ Read Pending     │───▶│ Start Apify        │
│ Trigger          │    │ Queries          │    │ Scraping Job       │
└──────────────────┘    └──────────────────┘    └────────────────────┘
                                                         │
                                                         ▼
┌──────────────────┐    ┌──────────────────┐    ┌────────────────────┐
│ Check Scraping   │◀───│ Wait for Job     │◀───│                    │
│ Status           │    │ to Succeed       │    │                    │
└──────────────────┘    └──────────────────┘    └────────────────────┘
        │
        ▼
┌──────────────────┐         NO
│ Loop Until       │─────────────────┐
│ Complete?        │                 │
└──────────────────┘                 │
        │ YES                        │
        ▼                            │
┌──────────────────┐                 │
│ Fetch Scraped    │◀────────────────┘
│ Results          │
└──────────────────┘
        │
        ▼
┌──────────────────┐    ┌──────────────────┐    ┌────────────────────┐
│ Save Business    │───▶│ Filter Businesses│───▶│ Batch Processing   │
│ Data             │    │ with Websites    │    │ Logic              │
└──────────────────┘    └──────────────────┘    └────────────────────┘
                                                         │
                                                         ▼
┌──────────────────┐    ┌──────────────────┐    ┌────────────────────┐
│ Save Contact     │◀───│ Extract Contact  │◀───│ Firecrawl Scrape   │
│ Details          │    │ Information      │    │ Website            │
└──────────────────┘    └──────────────────┘    └────────────────────┘
```

## 📊 Data Structures

### Google Sheets - Query Sheet

| Column | Description |
|--------|-------------|
| `Query` | Search term (e.g., "restaurants", "dentists") |
| `Location` | Geographic location (e.g., "New York, NY") |

### Google Sheets - Data Sheet (Results)

| Column | Description |
|--------|-------------|
| `searchString` | Original search query + location |
| `title` | Business name |
| `categoryName` | Business category |
| `address` | Full address |
| `phone` | Phone number |
| `website` | Business website URL |
| `status` | Scraping job status |

### Google Sheets - Contacts Sheet

| Column | Description |
|--------|-------------|
| `business_name` | Business name |
| `email` | Extracted email address |
| `social_media` | Social media links |
| `additional_contacts` | Other contact information |

## ⚙️ Credential Configuration

### 🔐 Required Credentials

| Parameter | Placeholder | Description |
|-----------|-------------|-------------|
| **Google Sheet ID** | `YOUR_GOOGLE_SHEET_ID` | Google Sheets document ID* |
| **Google Sheets Credential ID** | `YOUR_GOOGLE_SHEETS_CREDENTIAL_ID` | Google Sheets OAuth2 credential ID |
| **Apify Credential ID** | `YOUR_APIFY_CREDENTIAL_ID` | Apify Bearer Auth credential ID |
| **Firecrawl Credential ID** | `YOUR_FIRECRAWL_CREDENTIAL_ID` | Firecrawl Bearer Auth credential ID |
| **Webhook ID** | `YOUR_WEBHOOK_ID` | Wait node webhook ID |
| **Instance ID** | `YOUR_INSTANCE_ID` | Your n8n instance ID |

> **\*** The Google Sheet ID can be found in the document URL:  
> `https://docs.google.com/spreadsheets/d/`**`THIS_IS_THE_ID`**`/edit`

### Apify Configuration

[Apify](https://apify.com/) is used to scrape Google Maps:

1. Sign up at [apify.com](https://apify.com/)
2. Get your API token from Settings → Integrations
3. Create a Bearer Auth credential in n8n with your token
4. The workflow uses the `compass~crawler-google-places` actor

### Firecrawl Configuration

[Firecrawl](https://firecrawl.dev/) is used to scrape business websites:

1. Sign up at [firecrawl.dev](https://firecrawl.dev/)
2. Get your API key
3. Create a Bearer Auth credential in n8n

### Apify Actor Settings

The workflow uses these Apify settings:
- **Actor**: `compass~crawler-google-places`
- **Max places per search**: 15
- **Language**: English
- **Max images**: 0 (disabled for speed)

### Features

- 🕒 **Scheduled execution** - Runs every 30 minutes by default
- 🔄 **Polling mechanism** - Waits for Apify job to complete
- 📍 **Location-based search** - Search businesses by query and location
- 🌐 **Website scraping** - Extracts contact info from business websites
- 📊 **Multi-sheet storage** - Organizes data across multiple sheets

---

# 🤖 Wikipedia - Agent

An **n8n AI Agent workflow** that creates an intelligent chatbot assistant named **Miranda** that can search and retrieve information from **Wikipedia**. The workflow includes a standalone HTML chat interface for easy deployment.

![LangChain](https://img.shields.io/badge/LangChain-Agent-00A67E?style=for-the-badge)
![OpenAI](https://img.shields.io/badge/OpenAI-412991?style=for-the-badge&logo=openai&logoColor=white)
![Gemini](https://img.shields.io/badge/Google%20Gemini-8E75B2?style=for-the-badge&logo=google&logoColor=white)
![Ollama](https://img.shields.io/badge/Ollama-Local%20LLM-000000?style=for-the-badge)
![Wikipedia](https://img.shields.io/badge/Wikipedia-000000?style=for-the-badge&logo=wikipedia&logoColor=white)

## 📋 Description

This n8n workflow creates an AI-powered chatbot with:

1. **Chat Trigger** - Receives messages via webhook (public endpoint)
2. **AI Agent** - LangChain-based agent that processes queries
3. **Language Model** - Supports multiple LLM providers:
   - OpenAI (o3-mini)
   - Google Gemini
   - Ollama (local LLM)
4. **Simple Memory** - Buffer window memory (10 messages context)
5. **Wikipedia Tool** - Searches and retrieves Wikipedia articles

The assistant "Miranda" responds with concise information (2 paragraphs max) and includes article references as links.

## 🔄 Workflow Flow

```
┌────────────────────┐    ┌────────────────────┐
│ Chat Trigger       │───▶│ AI Agent           │
│ (Webhook)          │    │ (LangChain)        │
└────────────────────┘    └────────────────────┘
                                   │
                    ┌──────────────┼──────────────┐
                    ▼              ▼              ▼
            ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
            │ OpenAI      │ │ Gemini      │ │ Ollama      │
            │ o3-mini     │ │             │ │ gpt-oss     │
            └─────────────┘ └─────────────┘ └─────────────┘
                    │              │              │
                    └──────────────┼──────────────┘
                                   ▼
                    ┌────────────────────────────┐
                    │      Simple Memory         │
                    │   (10 message context)     │
                    └────────────────────────────┘
                                   │
                                   ▼
                    ┌────────────────────────────┐
                    │     Wikipedia Tool         │
                    │  (Search & Retrieve)       │
                    └────────────────────────────┘
```

## 📁 Project Structure

```
n8n_processes/
├── Wikipedia - Agent.json      # n8n workflow definition
└── n8n - chatbot/
    └── index.html              # Standalone chat UI
```

## 🎨 Chat Interface (`n8n - chatbot/index.html`)

A beautiful, pastel-themed chat interface built with the official n8n Chat widget:

- **Color Scheme**: Soft blue pastel theme
- **Responsive**: Adjustable chat window (400x600px default)
- **Initial Messages**: Greeting from Miranda
- **Custom CSS Variables**: Fully customizable styling

### Running the Chat Interface

1. Open the `index.html` file in a browser
2. Ensure the n8n workflow is active and running
3. Update the `webhookUrl` if your n8n instance is not on `localhost:5678`

## ⚙️ Credential Configuration

### 🔐 Required Credentials

| Parameter | Placeholder | Description |
|-----------|-------------|-------------|
| **Chat Webhook ID** | `YOUR_CHAT_WEBHOOK_ID` | Chat trigger webhook ID |
| **OpenAI Credential ID** | `YOUR_OPENAI_CREDENTIAL_ID` | OpenAI API credential |
| **Google Gemini Credential ID** | `YOUR_GOOGLE_GEMINI_CREDENTIAL_ID` | Google AI API credential |
| **Ollama Credential ID** | `YOUR_OLLAMA_CREDENTIAL_ID` | Ollama local API credential |
| **Instance ID** | `YOUR_INSTANCE_ID` | Your n8n instance ID |

### LLM Provider Options

The workflow supports three LLM providers (choose one):

| Provider | Model | Credential Type | Notes |
|----------|-------|-----------------|-------|
| **OpenAI** | o3-mini | OpenAI API | Cloud-based, requires API key |
| **Google Gemini** | Default | Google PaLM API | Cloud-based, requires API key |
| **Ollama** | gpt-oss:latest | Ollama API | Local, requires Ollama server |

### Chat Interface Configuration

Update the webhook URL in `index.html`:

```javascript
createChat({
    webhookUrl: 'http://localhost:5678/webhook/YOUR_CHAT_WEBHOOK_ID/chat',
    // ...
});
```

### Agent System Prompt

The AI agent (Miranda) uses this system prompt:

```
## Objective
You are a kind assistant, your name is Miranda, you help to look up 
for information in Wikipedia.

## Rules
- Always return responses in two paragraphs or less.
- Always specify the references of the articles that you search as a link.

## Tools
- You have a tool to connect to wikipedia and get information, 
  you can use to confirm information.
```

### Features

- 🧠 **Multi-LLM support** - Switch between OpenAI, Gemini, or local Ollama
- 💬 **Conversational memory** - Remembers last 10 messages
- 📚 **Wikipedia integration** - Real-time article search
- 🔗 **Reference links** - Provides source links for information
- 🎨 **Custom chat UI** - Beautiful standalone HTML interface
- 🌐 **Public endpoint** - Accessible webhook for integrations

---

# 🛠️ General Requirements

- [n8n](https://n8n.io/) (self-hosted or cloud)
- Google account with access to:
  - Google Sheets API
  - Gmail API
  - Google Calendar API (for Time Off workflows)
  - Google Docs API (for Scraping - Curso workflow)
  - Google Drive API (for Scraping - Curso workflow)
- OAuth2 credentials configured in n8n
- PostgreSQL database (for T-Shirts - PostgreSQL and Time Off workflows)
- Discord webhook (for Time Off workflows)
- OpenAI API key (for Scraping - Course workflow)
- Firecrawl API key (for Scraping - Course and Google Maps - Scraping workflows)
- Apify API token (for Google Maps - Scraping workflow)
- Google Gemini API key (for Wikipedia - Agent workflow, optional)
- Ollama local server (for Wikipedia - Agent workflow, optional)

---

## 🤔 What is n8n?

[n8n](https://n8n.io/) (pronounced "n-eight-n") is a free and open-source workflow automation tool. It allows you to:

- 🔗 Connect 400+ apps and services
- 🤖 Automate repetitive tasks
- 📊 Build complex workflows visually
- 💻 Self-host for complete data control

These workflows demonstrate n8n's capability to integrate multiple services (Google Sheets, external APIs, Gmail, PostgreSQL, Discord, Google Calendar, OpenAI, Firecrawl, Google Docs, Apify, Google Maps, LangChain Agents, Wikipedia, Google Gemini, Ollama) into seamless automation pipelines.

---

## 📄 License

This project is available under the MIT License. Feel free to use, modify, and share it.

---

## 🙏 Credits

- Pokémon data provided by [PokéAPI](https://pokeapi.co/)
- Workflows built with [n8n](https://n8n.io/)

---
