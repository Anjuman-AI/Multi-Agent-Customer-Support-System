# Airtable Setup Guide

Complete guide to setting up Airtable as the database backend for the AI Customer Support System.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Step 1: Create Airtable Account](#step-1-create-airtable-account)
4. [Step 2: Create Base](#step-2-create-base)
5. [Step 3: Configure Support Tickets Table](#step-3-configure-support-tickets-table)
6. [Step 4: Configure Daily Metrics Table](#step-4-configure-daily-metrics-table)
7. [Step 5: Configure Optional Tables](#step-5-configure-optional-tables)
8. [Step 6: Get API Credentials](#step-6-get-api-credentials)
9. [Step 7: Configure Views](#step-7-configure-views)
10. [Step 8: Set Up Automations](#step-8-set-up-automations)
11. [Step 9: Configure n8n](#step-9-configure-n8n)
12. [Step 10: Test Integration](#step-10-test-integration)
13. [Data Import](#data-import)
14. [Troubleshooting](#troubleshooting)
15. [Advanced Configuration](#advanced-configuration)

---

## Overview

**What you're building:** A structured database in Airtable to store support tickets, metrics, customer history, and agent performance data with powerful views, automations, and integrations.

**Time required:** 30-45 minutes

**Airtable plan needed:** 
- Free: Works for most features (limited to 1,200 records/base)
- Plus: Recommended for production ($10/seat/month)
- Pro: For advanced features and more records

**Key capabilities:**
- Store all support tickets with rich metadata
- Track daily and historical metrics
- Monitor agent performance
- Manage customer relationships
- Automated workflows and notifications
- Integration with n8n, Slack, Gmail

---

## Prerequisites

### Required Access
- [ ] Airtable account (free or paid)
- [ ] Ability to create bases and tables
- [ ] Email for account registration
- [ ] n8n instance running

### Before You Start
- [ ] Read through entire guide
- [ ] Review database schema: `database/SCHEMA.md`
- [ ] Have .env file ready to update
- [ ] Understand Airtable basics (tables, fields, views)

### Airtable Limits by Plan

```yaml
Free Plan:
- Bases: Unlimited
- Records per base: 1,200
- Attachments: 2 GB per base
- Revision history: 2 weeks
- API calls: 5 requests/second

Plus Plan ($10/user/month):
- Records per base: 5,000
- Attachments: 5 GB per base
- Revision history: 6 months
- API calls: 5 requests/second
- Gantt & Timeline views

Pro Plan ($20/user/month):
- Records per base: 50,000
- Attachments: 20 GB per base
- Revision history: 1 year
- API calls: 5 requests/second
- Personal views & extensions

Enterprise:
- Records per base: 500,000+
- Unlimited attachments
- Custom contracts
- SAML SSO
- Advanced admin controls
```

---

## Step 1: Create Airtable Account

### 1.1 Sign Up for Airtable

```
1. Go to: https://airtable.com/signup
2. Choose sign-up method:
   - Email + Password
   - Google account (recommended)
   - Apple ID
3. Verify email if using email signup
4. Complete onboarding tutorial (optional but helpful)
```

### 1.2 Choose Plan

```
For testing/development:
- Start with Free plan
- Upgrade later if needed

For production:
- Plus plan recommended
- More records and features
- Better support
```

### 1.3 Create Workspace

```
1. Click workspace dropdown (top left)
2. Select "Create workspace"
3. Workspace name: "AI Support System"
   (or your company name)
4. Invite team members (optional)
5. Click "Create"
```

---

## Step 2: Create Base

### 2.1 Create New Base

```
1. In your workspace
2. Click "+ Add a base" or "Create a base"
3. Choose "Start from scratch"
4. Base name: "Support Tickets"
5. Icon: 🎫 (optional)
6. Click "Create base"
```

### 2.2 Initial Setup

```
Airtable creates a base with one table by default.
We'll rename and configure it in the next step.

You'll see:
- Default table name: "Table 1"
- Default fields: Name, Notes, Status, etc.

We'll replace these with our schema.
```

---

## Step 3: Configure Support Tickets Table

### 3.1 Rename Default Table

```
1. Click "Table 1" dropdown (top left)
2. Select "Rename table"
3. New name: "Support Tickets"
4. Press Enter
```

### 3.2 Delete Default Fields

```
We'll create custom fields. First, delete defaults:

1. Click field header (e.g., "Notes")
2. Click "..." menu
3. Select "Delete field"
4. Repeat for all default fields except the first one
5. Rename first field to "Ticket ID"
```

### 3.3 Add Required Fields

Add these fields one by one. Click "+ Add field" button after Ticket ID column:

#### Field 1: Ticket ID (Primary Field)
```
Field name: Ticket ID
Field type: Single line text
Description: Unique identifier (TKT-YYYYMMDD-TIMESTAMP)

✅ This is already your first field, just verify it's named correctly
```

#### Field 2: Status
```
Field name: Status
Field type: Single select
Options:
  ✅ Resolved (green)
  🚨 Escalated (red)
  ⏳ Pending (yellow)
  🔄 In Progress (blue)

Default: Pending
```

**How to configure:**
```
1. Click "+ Add field"
2. Select "Single select"
3. Name: Status
4. Add each option
5. Click color dot to change colors
6. Set default to "Pending"
7. Click "Create field"
```

#### Field 3: Customer Email
```
Field name: Customer Email
Field type: Email
Description: Customer's email address
```

#### Field 4: Customer Name
```
Field name: Customer Name
Field type: Single line text
Description: Customer's full name
```

#### Field 5: Channel
```
Field name: Channel
Field type: Single select
Options:
  💬 Slack (purple #7B68EE)
  📧 Email (blue #4A90E2)

Default: Slack
```

#### Field 6: Message
```
Field name: Message
Field type: Long text
Description: Original customer message
Enable rich text formatting: No
```

#### Field 7: Subject
```
Field name: Subject
Field type: Single line text
Description: Email subject or Slack message preview
```

#### Field 8: Category
```
Field name: Category
Field type: Single select
Options:
  🐛 Bug (red #E74C3C)
  💰 Billing (green #27AE60)
  ✨ Feature (purple #9B59B6)
  💬 General (blue #3498DB)

Default: General
```

#### Field 9: Priority
```
Field name: Priority
Field type: Single select
Options:
  🔴 URGENT (red #E74C3C)
  🟠 HIGH (orange #E67E22)
  🟡 MEDIUM (yellow #F39C12)
  🟢 LOW (green #27AE60)

Default: MEDIUM
```

#### Field 10: Sentiment
```
Field name: Sentiment
Field type: Single select
Options:
  😊 POSITIVE (green #27AE60)
  😐 NEUTRAL (gray #95A5A6)
  😞 NEGATIVE (red #E74C3C)

Default: NEUTRAL
```

#### Field 11: AI Agent
```
Field name: AI Agent
Field type: Single select
Options:
  🐛 Bug Handler
  💰 Billing Agent
  ✨ Feature Agent
  💬 General Agent
```

#### Field 12: AI Response
```
Field name: AI Response
Field type: Long text
Description: AI-generated response to customer
Enable rich text formatting: Yes
```

#### Field 13: Confidence Score
```
Field name: Confidence Score
Field type: Number
Format: Decimal (0.00)
Precision: 2 decimal places
Description: AI confidence (0.00-1.00)
```

#### Field 14: Needs Human
```
Field name: Needs Human
Field type: Checkbox
Description: Requires human agent intervention
Default: Unchecked
```

#### Field 15: Escalation Reason
```
Field name: Escalation Reason
Field type: Long text
Description: Why ticket was escalated
```

#### Field 16: Escalation Urgency
```
Field name: Escalation Urgency
Field type: Single select
Options:
  🔴 HIGH (red)
  🟡 MEDIUM (yellow)
  🟢 LOW (green)
```

#### Field 17: Assigned To
```
Field name: Assigned To
Field type: Single line text
Description: Human agent assigned to ticket
```

#### Field 18: Assigned Email
```
Field name: Assigned Email
Field type: Email
Description: Agent's email address
```

#### Field 19: Assigned At
```
Field name: Assigned At
Field type: Date
Include time: Yes
Time format: 24-hour
GMT: Use workspace timezone
```

#### Field 20: Response Time
```
Field name: Response Time
Field type: Number
Format: Integer
Description: Response time in seconds
```

#### Field 21: Created
```
Field name: Created
Field type: Created time
Date format: Local (M/D/YYYY)
Include time: Yes
Time format: 24-hour
```

#### Field 22: Last Modified
```
Field name: Last Modified
Field type: Last modified time
Date format: Local (M/D/YYYY)
Include time: Yes
Time format: 24-hour
```

#### Field 23: Thread ID
```
Field name: Thread ID
Field type: Single line text
Description: Slack thread or email thread ID
```

#### Field 24: Message ID
```
Field name: Message ID
Field type: Single line text
Description: Unique message identifier from channel
```

### 3.4 Add Optional Formula Fields

These calculate automatically based on other fields:

#### Formula 1: Age (Hours)
```
Field name: Age (Hours)
Field type: Formula
Formula:
ROUND(
  DATETIME_DIFF(NOW(), {Created}, 'hours'),
  1
)

Format: Decimal (0.0)
Description: Hours since ticket created
```

#### Formula 2: SLA Status
```
Field name: SLA Status
Field type: Formula
Formula:
IF(
  {Status} = "Resolved",
  "✅ Resolved",
  IF(
    AND({Priority} = "URGENT", {Age (Hours)} > 1),
    "⚠️ URGENT Overdue",
    IF(
      AND({Priority} = "HIGH", {Age (Hours)} > 4),
      "⚠️ HIGH Overdue",
      IF(
        AND({Priority} = "MEDIUM", {Age (Hours)} > 24),
        "⚠️ MEDIUM Overdue",
        IF(
          AND({Priority} = "LOW", {Age (Hours)} > 48),
          "⚠️ LOW Overdue",
          "✓ Within SLA"
        )
      )
    )
  )
)

Format: Single line text
Description: SLA compliance status
```

#### Formula 3: Resolution Time
```
Field name: Resolution Time
Field type: Formula
Formula:
IF(
  {Status} = "Resolved",
  ROUND(
    DATETIME_DIFF({Last Modified}, {Created}, 'hours'),
    1
  ),
  ""
)

Format: Decimal (0.0)
Description: Hours to resolve (blank if not resolved)
```

#### Formula 4: Customer Contact
```
Field name: Customer Contact
Field type: Formula
Formula:
{Customer Name} & " (" & {Customer Email} & ")"

Format: Single line text
Description: Formatted customer info
```

### 3.5 Verify Field Order

Drag fields to match this recommended order:
```
1. Ticket ID (Primary)
2. Status
3. Customer Name
4. Customer Email
5. Channel
6. Category
7. Priority
8. Sentiment
9. Subject
10. Message
11. AI Agent
12. AI Response
13. Confidence Score
14. Needs Human
15. Escalation Reason
16. Escalation Urgency
17. Assigned To
18. Assigned Email
19. Assigned At
20. Response Time
21. Created
22. Last Modified
23. Age (Hours)
24. SLA Status
25. Resolution Time
26. Thread ID
27. Message ID
28. Customer Contact
```

---

## Step 4: Configure Daily Metrics Table

### 4.1 Create Table

```
1. Click "+ Add or import" → "Create empty table"
2. Name: "Daily Metrics"
3. Click "Create table"
```

### 4.2 Configure Primary Field

```
Rename first field:
Field name: Date
Field type: Date
Format: Friendly (January 1, 2024)
Include time: No
```

### 4.3 Add Metric Fields

#### Performance Metrics
```
Total Tickets
  Type: Number, Integer

Resolved
  Type: Number, Integer

Escalated
  Type: Number, Integer

Pending
  Type: Number, Integer

Resolution Rate
  Type: Percent (0.00%)
  Formula: IF({Total Tickets} > 0, {Resolved} / {Total Tickets}, 0)

Escalation Rate
  Type: Percent (0.00%)
  Formula: IF({Total Tickets} > 0, {Escalated} / {Total Tickets}, 0)
```

#### Quality Metrics
```
Avg Confidence
  Type: Number, Decimal (0.00)

Avg Response Time
  Type: Number, Integer
  Description: Average seconds

Median Response Time
  Type: Number, Integer
  Description: Median seconds
```

#### Category Breakdown
```
Bug Count
  Type: Number, Integer

Billing Count
  Type: Number, Integer

Feature Count
  Type: Number, Integer

General Count
  Type: Number, Integer
```

#### Sentiment Analysis
```
Positive Sentiment
  Type: Number, Integer

Neutral Sentiment
  Type: Number, Integer

Negative Sentiment
  Type: Number, Integer
```

#### Channel Distribution
```
Slack Tickets
  Type: Number, Integer

Email Tickets
  Type: Number, Integer
```

#### Financial Metrics
```
Cost Savings
  Type: Currency ($)
  Precision: 2 decimals
  Formula: 
    ({Resolved} * 8.33) - ({Total Tickets} * 0.03)
  Description: Traditional cost - AI cost
```

#### Performance Grade
```
Performance Grade
  Type: Single select
  Options:
    🌟 A (green) - Resolution rate ≥ 85%
    ✅ B (blue) - Resolution rate 75-84%
    ⚠️ C (yellow) - Resolution rate 65-74%
    ❌ D (red) - Resolution rate < 65%
```

#### Notes
```
Notes
  Type: Long text
  Description: Additional context or observations
```

### 4.4 Add Optional Formula Fields

```
Week Number
  Type: Formula
  Formula: WEEKNUM({Date})
  Format: Integer

Month
  Type: Formula
  Formula: DATETIME_FORMAT({Date}, 'MMMM YYYY')
  Format: Single line text

Day of Week
  Type: Formula
  Formula: DATETIME_FORMAT({Date}, 'dddd')
  Format: Single line text

Total Categories
  Type: Formula
  Formula: {Bug Count} + {Billing Count} + {Feature Count} + {General Count}
  Format: Integer

Sentiment Score
  Type: Formula
  Formula:
    IF(
      {Total Tickets} > 0,
      (
        ({Positive Sentiment} * 1) + 
        ({Neutral Sentiment} * 0) + 
        ({Negative Sentiment} * -1)
      ) / {Total Tickets},
      0
    )
  Format: Decimal (0.00)
  Description: -1 to +1 scale
```

---

## Step 5: Configure Optional Tables

### 5.1 Agent Performance Table (Optional)

```
Create table: "Agent Performance"

Fields:
1. Agent Name (Primary)
   Type: Single line text

2. Date
   Type: Date

3. Tickets Handled
   Type: Number, Integer

4. Resolved
   Type: Number, Integer

5. Escalated
   Type: Number, Integer

6. Resolution Rate
   Type: Percent
   Formula: IF({Tickets Handled} > 0, {Resolved} / {Tickets Handled}, 0)

7. Avg Confidence
   Type: Number, Decimal (0.00)

8. Avg Response Time
   Type: Number, Integer (seconds)

9. Bug Tickets
   Type: Number, Integer

10. Billing Tickets
    Type: Number, Integer

11. Feature Tickets
    Type: Number, Integer

12. General Tickets
    Type: Number, Integer
```

### 5.2 Customer History Table (Optional)

```
Create table: "Customer History"

Fields:
1. Customer Email (Primary)
   Type: Email

2. Customer Name
   Type: Single line text

3. First Contact
   Type: Date
   Include time: Yes

4. Last Contact
   Type: Date
   Include time: Yes

5. Total Tickets
   Type: Number, Integer

6. Resolved Tickets
   Type: Number, Integer

7. Escalated Tickets
   Type: Number, Integer

8. Avg Satisfaction
   Type: Number, Decimal (0.0)
   Description: 1-5 scale

9. Preferred Channel
   Type: Single select
   Options: Slack, Email

10. VIP Status
    Type: Checkbox

11. Company
    Type: Single line text

12. Tags
    Type: Multiple select
    Options: High-value, Frequent, At-risk, Champion

13. Notes
    Type: Long text
```

### 5.3 Knowledge Base Table (Optional)

```
Create table: "Knowledge Base"

Fields:
1. Article ID (Primary)
   Type: Autonumber
   Format: KB-00001

2. Title
   Type: Single line text

3. Category
   Type: Single select
   Options: Bug, Billing, Feature, General, Getting Started

4. Content
   Type: Long text
   Enable rich text: Yes

5. Keywords
   Type: Multiple select
   Add common search terms

6. Times Referenced
   Type: Number, Integer

7. Last Used
   Type: Date

8. Helpful Count
   Type: Number, Integer

9. Not Helpful Count
   Type: Number, Integer

10. Status
    Type: Single select
    Options: Published, Draft, Archived

11. Created By
    Type: Single line text

12. Last Updated
    Type: Last modified time
```

---

## Step 6: Get API Credentials

### 6.1 Generate Personal Access Token

```
1. Click your profile icon (top right)
2. Select "Account"
3. Navigate to "Developer" section
4. Click "Personal access tokens"
5. Click "Create new token"
```

### 6.2 Configure Token

```
Token name: AI Support System Integration

Scopes:
☑ data.records:read - Read records
☑ data.records:write - Create/update/delete records
☑ schema.bases:read - Read base schema
☑ schema.bases:write - Modify base schema (optional)

Access:
☑ Support Tickets (your base name)

Click "Create token"
```

### 6.3 Save Token

```
IMPORTANT: Copy token immediately!
Format: pat**********************************

You won't be able to see it again.

Save to .env:
AIRTABLE_API_KEY=pat**********************************
```

### 6.4 Get Base ID

```
Method 1: From URL
1. Open your base
2. Look at URL: https://airtable.com/[BASE_ID]/...
3. BASE_ID format: app******************
4. Copy base ID

Method 2: From API docs
1. Go to: https://airtable.com/api
2. Select your base
3. Base ID shown at top of page

Save to .env:
AIRTABLE_BASE_ID=app******************
```

### 6.5 Get Table IDs

```
Method 1: From URL
1. Open table
2. URL: https://airtable.com/[BASE_ID]/[TABLE_ID]/...
3. TABLE_ID format: tbl******************

Method 2: Via API
GET https://api.airtable.com/v0/meta/bases/[BASE_ID]/tables
Authorization: Bearer [YOUR_TOKEN]

Returns JSON with all table IDs.

Save to .env:
AIRTABLE_TICKETS_TABLE_ID=tbl******************
AIRTABLE_METRICS_TABLE_ID=tbl******************
AIRTABLE_AGENT_PERFORMANCE_TABLE_ID=tbl******************
AIRTABLE_CUSTOMER_HISTORY_TABLE_ID=tbl******************
```

### 6.6 Complete .env Configuration

```bash
# Airtable Configuration
AIRTABLE_API_KEY=pat**********************************
AIRTABLE_BASE_ID=app******************
AIRTABLE_TICKETS_TABLE_ID=tbl******************
AIRTABLE_METRICS_TABLE_ID=tbl******************
AIRTABLE_AGENT_PERFORMANCE_TABLE_ID=tbl******************
AIRTABLE_CUSTOMER_HISTORY_TABLE_ID=tbl******************
```

---

## Step 7: Configure Views

See `database/VIEWS.md` for complete view configurations. Here are the essential ones:

### 7.1 Support Tickets Views

#### All Tickets (Default)
```
View type: Grid
Sort: Created (newest first), Priority (URGENT → LOW)
Filter: None
Show all fields
```

#### Unassigned Escalations (Critical!)
```
View type: Grid
Filter:
  Status = "Escalated"
  AND
  Assigned To is empty

Sort: 
  Priority (URGENT first)
  Created (oldest first)

Color: By Priority

Note: n8n uses this view for auto-assignment!
```

#### In Progress
```
View type: Kanban
Group by: Assigned To
Filter: Status = "In Progress"
Sort: Priority, Created
Card fields:
  - Ticket ID
  - Customer Name
  - Priority
  - Category
  - Sentiment
  - Age (Hours)
  - Message (preview)
```

#### Today's Tickets
```
View type: Grid
Filter: Created is today
Group by: Category
Sort: Created (newest first)
Color: By Sentiment
```

#### Overdue Tickets
```
View type: Grid
Filter: SLA Status contains "Overdue"
Sort: Age (Hours) descending, Priority
Color: By Priority
Conditional formatting:
  - Age > 24h: Red
  - Age > 12h: Orange
```

### 7.2 Daily Metrics Views

#### Last 30 Days
```
View type: Grid
Filter: Date is within last 30 days
Sort: Date (newest first)
Show summary row:
  - Avg Resolution Rate
  - Avg Escalation Rate
  - Total Tickets (sum)
```

#### Performance Trends
```
View type: Timeline
X-axis: Date
Y-axis: Resolution Rate
Show: 90 days
Color by: Performance Grade
```

---

## Step 8: Set Up Automations

See `database/AUTOMATIONS.md` for detailed automation setup. Essential automations:

### 8.1 Stale Ticket Alert

```
Trigger: Every day at 9 AM (weekdays only)
Conditions:
  - Status = "In Progress" OR "Escalated"
  - Created > 7 days ago

Actions:
  1. Find records (Overdue Tickets view)
  2. Run script to format list
  3. Send to Slack #escalations
```

### 8.2 VIP Customer Notification

```
Trigger: When record created
Conditions:
  - Customer Email contains VIP domains
  OR Customer Name contains "VIP"

Actions:
  1. Send Slack message to #escalations
     With @channel mention
  2. Update record:
     - Priority → HIGH
     - Add "VIP" tag
```

### 8.3 High Escalation Rate Alert

```
Trigger: When Daily Metrics record created
Conditions:
  - Escalation Rate > 30%

Actions:
  1. Send email to support lead
  2. Send Slack to #system-alerts
  3. Create investigation task
```

---

## Step 9: Configure n8n

### 9.1 Add Airtable Credentials in n8n

```
1. n8n → Settings → Credentials
2. Click "Add Credential"
3. Search "Airtable"
4. Select "Airtable Personal Access Token API"
```

### 9.2 Configure Credential

```
Credential name: Airtable Support System
Personal Access Token: [Your token from Step 6]

Click "Create"
```

### 9.3 Test Credential

```
1. In credential screen, click "Test"
2. Should return: "Credential test successful"
3. If error, verify:
   - Token is correct
   - Token has required scopes
   - Token has access to your base
```

### 9.4 Update Workflows

For each workflow (01-06):

```
1. Open workflow
2. Find Airtable nodes
3. Click each Airtable node
4. Update settings:
   - Credential: Select "Airtable Support System"
   - Base: Select "Support Tickets"
   - Table: Select appropriate table
5. Save workflow
```

### 9.5 Configure Specific Nodes

#### Create Ticket Node
```
Operation: Append
Table: Support Tickets
Fields to Send:
  - Ticket ID: {{$json.ticketId}}
  - Customer Email: {{$json.email}}
  - Customer Name: {{$json.name}}
  - Channel: {{$json.channel}}
  - Message: {{$json.message}}
  - Category: {{$json.category}}
  - Priority: {{$json.priority}}
  - Sentiment: {{$json.sentiment}}
  - AI Agent: {{$json.agent}}
  - AI Response: {{$json.response}}
  - Confidence Score: {{$json.confidence}}
  - Status: Resolved (or Escalated)
  - Created: {{$now}}
```

#### Update Ticket Node
```
Operation: Update
Table: Support Tickets
Record ID: {{$json.recordId}}
Fields to Update:
  - Status: {{$json.newStatus}}
  - Assigned To: {{$json.assignedAgent}}
  - Assigned At: {{$now}}
```

#### Query Unassigned Escalations
```
Operation: List
Table: Support Tickets
View: Unassigned Escalations
Max Records: 10
Sort: Priority (descending)
```

---

## Step 10: Test Integration

### 10.1 Manual Record Creation

```
1. Open Support Tickets table
2. Click "+ Add record"
3. Fill in fields:
   Ticket ID: TEST-001
   Customer Email: test@example.com
   Customer Name: Test User
   Channel: Slack
   Category: General
   Priority: MEDIUM
   Message: "This is a test message"
   Status: Pending
4. Click away to save
```

### 10.2 Test n8n Create

```
1. n8n → Open workflow 01-slack-support.json
2. Click "Execute Workflow" (manual test)
3. Or send test message in Slack #support
4. Check execution:
   ✅ Workflow runs successfully
   ✅ No errors in nodes
5. Check Airtable:
   ✅ New record appears
   ✅ All fields populated correctly
   ✅ Formulas calculate
```

### 10.3 Test Filtering

```
1. Create test records with different statuses
2. Open "Unassigned Escalations" view
3. Verify:
   ✅ Only escalated tickets show
   ✅ Only unassigned tickets show
   ✅ Sorted by priority
4. Open "Today's Tickets" view
5. Verify:
   ✅ Only today's records
   ✅ Grouped by category
```

### 10.4 Test Automation

```
1. Create VIP test record:
   Customer Email: ceo@vip-company.com
2. Wait 5-10 seconds
3. Check Slack #escalations
4. Verify:
   ✅ VIP alert sent
5. Check record in Airtable:
   ✅ Priority elevated
   ✅ VIP tag added
```

### 10.5 Test Formula Fields

```
1. Open any ticket record
2. Check calculated fields:
   ✅ Age (Hours) shows correct hours
   ✅ SLA Status shows correct status
   ✅ Customer Contact formatted correctly
3. Create resolved ticket
4. Wait a moment
5. Check:
   ✅ Resolution Time calculated
```

---

## Data Import

### Import from CSV

```
1. Prepare CSV file with headers matching field names:
   Ticket ID, Customer Email, Customer Name, etc.

2. In Airtable:
   Click "..." menu on table → Import data → CSV file

3. Map columns:
   - CSV column → Airtable field
   - Verify mappings
   - Handle date/time formats

4. Import settings:
   - Create new records: Yes
   - Update existing records: Optional
   - Match on: Ticket ID

5. Click "Import"
```

### Sample CSV Template

```csv
Ticket ID,Customer Email,Customer Name,Channel,Category,Priority,Status,Message
TKT-20240109-001,john@example.com,John Doe,Email,Bug,HIGH,Resolved,"App crashes on export"
TKT-20240109-002,jane@example.com,Jane Smith,Slack,Billing,MEDIUM,Resolved,"Question about invoice"
TKT-20240109-003,bob@example.com,Bob Johnson,Email,Feature,LOW,Pending,"Request dark mode"
```

### Import from API

```javascript
// Bulk import via API
const records = [
  {
    fields: {
      "Ticket ID": "TKT-20240109-004",
      "Customer Email": "alice@example.com",
      "Customer Name": "Alice Brown",
      "Channel": "Slack",
      "Category": "General",
      "Priority": "MEDIUM",
      "Status": "Resolved",
      "Message": "How do I export data?"
    }
  },
  // ... more records
];

// Batch create (max 10 records per request)
const response = await fetch(
  `https://api.airtable.com/v0/${BASE_ID}/${TABLE_ID}`,
  {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ records })
  }
);
```

---

## Troubleshooting

### Issue: Can't Create Personal Access Token

**Symptoms:**
- "Create token" button disabled
- Don't see Personal Access Tokens section

**Solutions:**
```
1. Verify account type:
   - Free accounts CAN create tokens
   - Check you're logged in

2. Check permissions:
   - Must be workspace owner or creator
   - Collaborators may have restrictions

3. Use legacy API key (deprecated):
   - Account → Generate API key
   - Note: Less secure, will be phased out
```

---

### Issue: API Returns 401 Unauthorized

**Symptoms:**
- "Authentication required" errors
- n8n can't connect

**Solutions:**
```
1. Verify token:
   - Copy-paste error?
   - Token starts with "pat"
   - No extra spaces

2. Check scopes:
   - data.records:read ✓
   - data.records:write ✓
   - Token has access to base ✓

3. Test manually:
   curl -H "Authorization: Bearer pat..." \
        https://api.airtable.com/v0/meta/bases

4. Regenerate token if needed
```

---

### Issue: Field Not Found

**Symptoms:**
- "Field xyz not found" errors
- n8n workflow fails

**Solutions:**
```
1. Check field name:
   - Exact match required
   - Case-sensitive
   - "Customer Email" ≠ "customer email"

2. Verify field exists:
   - Open table in Airtable
   - Check spelling
   - Field not deleted

3. Update workflow:
   - Use exact field name from Airtable
   - Match spaces and capitalization

4. Check field type compatibility:
   - Sending text to number field fails
   - Sending number to single-select fails
```

---

### Issue: Rate Limit Exceeded

**Symptoms:**
- 429 "Rate limit exceeded" errors
- Requests fail intermittently

**Solutions:**
```
1. Understand limits:
   - 5 requests per second per base
   - Applies to all API calls

2. Implement rate limiting in n8n:
   - Add delay between requests
   - Batch operations
   - Use "Wait" node (200ms between calls)

3. Optimize workflow:
   - Reduce unnecessary API calls
   - Cache frequently accessed data
   - Use views to pre-filter

4. Upgrade plan:
   - Free/Plus/Pro: 5 req/s
   - Enterprise: Higher limits
```

---

### Issue: Records Not Appearing

**Symptoms:**
- n8n says success
- Record not in Airtable

**Solutions:**
```
1. Check view filters:
   - Record created but filtered out
   - Switch to "All Tickets" view
   - Verify record exists

2. Verify table ID:
   - n8n using correct table?
   - Check table ID in workflow
   - Compare with Airtable URL

3. Check required fields:
   - Primary field must have value
   - Required fields can't be empty

4. Look in trash:
   - Record might be deleted
   - Check trash for recovery
```

---

### Issue: Formula Not Calculating

**Symptoms:**
- Formula field shows #ERROR!
- Formula returns unexpected value

**Solutions:**
```
1. Check field references:
   - {Field Name} must match exactly
   - Case-sensitive
   - Field exists

2. Verify data types:
   - Can't do math on text
   - Date functions need date fields
   - Check field type compatibility

3. Test formula step by step:
   - Simplify formula
   - Test each part
   - Add complexity gradually

4. Common errors:
   - Division by zero
   - NULL values
   - Wrong function syntax

5. Check Airtable formula docs:
   https://support.airtable.com/hc/en-us/articles/203255215
```

---

## Advanced Configuration

### Linked Records

Connect tables with relationships:

```
In Support Tickets:
1. Add field "Customer"
   Type: Link to another record
   Table: Customer History
   
2. Link by: Customer Email
   - Auto-links on match
   - Shows customer history

In Customer History:
1. Add field "Tickets"
   Type: Link to another record
   Table: Support Tickets
   - Shows all customer tickets

Benefits:
- See full customer context
- Track customer lifetime
- Identify patterns
```

### Rollup Fields

Aggregate data from linked records:

```
In Customer History:
1. Add field "Total Resolved"
   Type: Rollup
   Linked record field: Tickets
   Field to rollup: Status
   Rollup function: COUNTALL(values = "Resolved")

2. Add field "Avg Response Time"
   Type: Rollup
   Linked record field: Tickets
   Field to rollup: Response Time
   Rollup function: AVERAGE(values)

Shows:
- Total resolved tickets per customer
- Average response time per customer
```

### Lookup Fields

Pull data from linked records:

```
In Support Tickets:
1. Add field "Customer VIP Status"
   Type: Lookup
   Linked record field: Customer
   Field to lookup: VIP Status
   
Shows VIP status directly in ticket view
```

### Conditional Formatting

Visual indicators for important data:

```
1. Click "..." on view → Customize field options
2. Select field (e.g., Priority)
3. Click "Customize field type"
4. Enable "Color by"
5. Set colors per option

Or use automations:
- When Priority = URGENT → Color cell red
- When Age > 24 hours → Highlight row
- When SLA Status = Overdue → Bold text
```

### Sync with External Sources

```
Airtable can sync with:
- Google Sheets
- Excel
- Salesforce
- Jira
- PostgreSQL
- MySQL

Setup:
1. Click "+ Add or import"
2. Select "Sync data from external source"
3. Choose source
4. Configure sync
5. Set sync frequency
```

### Interface Designer

Create custom interfaces:

```
1. Click "Interfaces" (top menu)
2. Click "Create interface"
3. Choose layout:
   - Dashboard
   - Portal
   - Timeline
   - Gallery

4. Add elements:
   - Record list
   - Record details
   - Charts
   - Buttons
   - Forms

5. Configure permissions:
   - Who can view
   - Who can edit
   - Share link

Use cases:
- Customer portal (submit tickets)
- Agent dashboard (manage queue)
- Executive dashboard (metrics)
- Public form (collect feedback)
```

### Extensions

Enhance Airtable with apps:

```
1. Click "Extensions" (right sidebar)
2. Click "+ Add extension"
3. Popular extensions:
   - Page Designer (reports)
   - Chart (visualizations)
   - Scripting (custom logic)
   - Pivot Table (analysis)

4. Configure extension
5. Add to views
```

---

## Performance Optimization

### Indexing Strategy

```
Airtable automatically indexes:
- Primary fields
- Linked record fields
- Created time / Last modified

Optimize:
1. Use views with filters (pre-filtered)
2. Limit records returned (maxRecords)
3. Request only needed fields
4. Use pagination for large datasets
```

### Batch Operations

```javascript
// Instead of creating one at a time:
for (let record of records) {
  await createRecord(record);  // ❌ Slow, many API calls
}

// Batch create (up to 10 records):
const batches = chunk(records, 10);
for (let batch of batches) {
  await createRecords(batch);  // ✅ Fast, fewer API calls
  await sleep(200);  // Rate limit friendly
}
```

### Caching

```javascript
// Cache frequently accessed data
const cache = {
  labelIds: {},
  fieldIds: {},
  viewIds: {}
};

// Fetch once, use many times
if (!cache.labelIds[labelName]) {
  cache.labelIds[labelName] = await getLabelId(labelName);
}

const labelId = cache.labelIds[labelName];
```

### View Optimization

```
Fast views:
✅ Filter on indexed fields
✅ Limit visible fields
✅ Use "Load more" instead of showing all
✅ Simple sorts (single field)

Slow views:
❌ Complex formulas in every row
❌ Many linked record fields
❌ Rollups with large linked tables
❌ Multiple sorts
```

---

## Data Management

### Backup Strategy

```
Method 1: CSV Export
1. Open table
2. Click "..." → Download CSV
3. Schedule weekly backups
4. Store in secure location

Method 2: API Backup
- Script to export all records
- Run daily via cron job
- Version control backups

Method 3: Sync to Database
- Use n8n to sync to PostgreSQL
- Real-time backup
- Query with SQL
```

### Archiving Old Records

```
Strategy 1: Archive Table
1. Create "Support Tickets - Archive" table
2. Move records > 90 days old
3. Keep main table performant

Strategy 2: Status Change
1. Add "Archived" status
2. Filter out archived in views
3. Keep in same table

Strategy 3: Separate Base
1. Create "Support Archive" base
2. Move old data annually
3. Link if needed
```

### Data Retention Policy

```yaml
Recommended:
- Active tickets: Keep in main base
- Resolved < 90 days: Keep in main table
- Resolved > 90 days: Archive table
- Resolved > 1 year: Separate base or delete

GDPR Compliance:
- Customer requests deletion
- Remove PII within 30 days
- Keep anonymized metrics
- Document retention policy
```

---

## Security Best Practices

### Access Control

```yaml
✅ Use Personal Access Tokens (not legacy API keys)
✅ Minimum required scopes
✅ Separate tokens for dev/staging/prod
✅ Rotate tokens quarterly
✅ Revoke unused tokens
✅ Audit token usage
✅ Use workspace permissions
✅ Collaborator view/edit/comment permissions
```

### Data Protection

```yaml
✅ Enable 2FA on Airtable account
✅ Strong password policy
✅ Don't share tokens via email/Slack
✅ Store tokens in .env (git-ignored)
✅ Encrypt sensitive fields
✅ Audit logs for data access
✅ Regular backups
✅ Test restore process
```

### Compliance

```yaml
GDPR:
✅ Data processing agreement with Airtable
✅ Customer data deletion workflow
✅ Export customer data on request
✅ Document data flows
✅ Privacy policy

SOC 2:
✅ Airtable is SOC 2 Type II certified
✅ Access controls documented
✅ Audit logs enabled
✅ Encryption in transit and at rest
```

---

## Monitoring & Alerts

### Key Metrics to Track

```yaml
Daily:
- Total records in base
- API call count
- Error rate
- New tickets today
- Unassigned escalations

Weekly:
- Growth rate (records/week)
- Storage used
- Automation run count
- View usage

Monthly:
- Collaborator activity
- Plan usage (records vs limit)
- Performance trends
- Cost per ticket
```

### Set Up Alerts

```javascript
// In n8n, create monitoring workflow:

1. Schedule: Every hour
2. Check Airtable:
   - Unassigned escalations count
   - Overdue tickets count
   - API error rate
3. If threshold exceeded:
   - Send Slack alert
   - Email on-call engineer
   - Create incident ticket
```

---

## Checklist

### Pre-Launch Checklist

- [ ] Airtable account created
- [ ] Base created with correct name
- [ ] Support Tickets table configured (24 fields)
- [ ] Daily Metrics table configured (21 fields)
- [ ] Optional tables created (if needed)
- [ ] All fields have correct types
- [ ] Single-select options configured
- [ ] Formula fields working correctly
- [ ] Personal Access Token generated
- [ ] Token scopes correct (read + write)
- [ ] Token saved to .env
- [ ] Base ID saved to .env
- [ ] Table IDs saved to .env
- [ ] Essential views created
- [ ] Unassigned Escalations view configured
- [ ] Automations set up
- [ ] n8n credentials configured
- [ ] n8n workflows updated with IDs
- [ ] Test record created successfully
- [ ] n8n integration tested
- [ ] Filtering tested
- [ ] Formulas calculating correctly
- [ ] Backup strategy defined
- [ ] Data retention policy documented
- [ ] Team trained on Airtable
- [ ] Documentation updated

---

## Next Steps

After successful Airtable setup:

1. **Configure n8n Workflows**
   - See: `workflows/n8n/README.md`
   - Update all Airtable nodes
   - Test each workflow

2. **Set Up Slack Integration**
   - See: `config/slack-setup.md`
   - Link to Airtable automations

3. **Configure Gmail**
   - See: `config/gmail-setup.md`
   - Create tickets from emails

4. **Create Dashboard**
   - See: `docs/google-sheets-template.md`
   - Sync Airtable data

5. **Train Your Team**
   - Share Airtable base
   - Define views each role uses
   - Set permissions appropriately

---

## Support Resources

### Official Documentation
- Airtable Support: https://support.airtable.com/
- API Docs: https://airtable.com/api
- Formula Reference: https://support.airtable.com/hc/en-us/articles/203255215
- Scripting: https://airtable.com/developers/scripting

### Community
- Community Forum: https://community.airtable.com/
- YouTube Channel: Airtable official tutorials
- Template Library: Pre-built bases

### Learning Resources
- Airtable Universe: Example bases
- Airtable Academy: Video courses
- Webinars: Weekly topics

---

## Version

**Version**: 1.0.0  
**Last Updated**: 2024-01-09  
**Compatible with**: Airtable Web, API v0

---

**Setup Complete!** 📊

Your Airtable database is now ready to power your AI Customer Support System. For questions or issues, refer to the [Troubleshooting](#troubleshooting) section or consult the additional database documentation in the `/database` folder.
