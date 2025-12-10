# Airtable Database Schema

Complete database structure for the AI Multi-Agent Customer Support System.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Base Configuration](#base-configuration)
3. [Tables](#tables)
   - [Support Tickets](#1-support-tickets-table)
   - [Daily Metrics](#2-daily-metrics-table)
   - [Agent Performance](#3-agent-performance-table-optional)
   - [Customer History](#4-customer-history-table-optional)
4. [Relationships](#relationships)
5. [Views](#views)
6. [Automations](#automations)
7. [Setup Instructions](#setup-instructions)

---

## Overview

**Base Name:** `AI Customer Support System`

**Purpose:** Centralized database for tracking customer support tickets, AI agent performance, escalations, and analytics.

**Tables:** 2 required, 2 optional
- ✅ Support Tickets (required)
- ✅ Daily Metrics (required)
- ⭐ Agent Performance (optional)
- ⭐ Customer History (optional)

**Integrations:**
- n8n workflows (read/write)
- Slack (notifications)
- Gmail (email tracking)
- API access (webhooks)

---

## Base Configuration

### Access & Permissions

```
Base Settings:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Workspace: Your Company Workspace
✅ Collaborators: Support team
✅ API Access: Enabled
✅ Personal Access Token: Required for n8n
```

### API Configuration

```bash
# Required for n8n integration
1. Go to: https://airtable.com/create/tokens
2. Create token with scopes:
   - data.records:read
   - data.records:write
   - schema.bases:read
3. Add to n8n credentials
```

---

## Tables

## 1. Support Tickets Table

**Table Name:** `Support Tickets`
**Primary Use:** Main ticket tracking and management

### Fields Configuration

| Field Name | Type | Configuration | Description |
|-----------|------|--------------|-------------|
| **Ticket ID** | Single line text | Primary field | Unique identifier (TKT-YYYYMMDD-TIMESTAMP) |
| **Status** | Single select | 4 options | Current ticket status |
| **Customer Email** | Email | — | Customer's email address |
| **Customer Name** | Single line text | — | Customer's name |
| **Channel** | Single select | 2 options | Source channel (Slack/Email) |
| **Message** | Long text | Enable rich text | Original customer message |
| **Subject** | Single line text | — | Email subject or message title |
| **Category** | Single select | 4 options | Ticket category |
| **Priority** | Single select | 4 options | Urgency level |
| **Sentiment** | Single select | 3 options | Customer emotional state |
| **AI Agent** | Single select | 4 options | Which agent handled it |
| **AI Response** | Long text | Enable rich text | AI-generated response |
| **Confidence Score** | Number | Precision: 2, Format: Decimal | AI confidence (0.00-1.00) |
| **Needs Human** | Checkbox | — | Requires human escalation |
| **Escalation Reason** | Long text | — | Why escalated |
| **Escalation Urgency** | Single select | 3 options | Escalation priority level |
| **Assigned To** | Single line text | — | Human agent assigned |
| **Assigned Email** | Email | — | Agent's email |
| **Assigned At** | Date | Include time | When assigned to human |
| **Response Time** | Number | Format: Duration (s) | Time to respond (seconds) |
| **Created** | Created time | — | Auto-generated |
| **Last Modified** | Last modified time | — | Auto-generated |
| **Thread ID** | Single line text | — | Slack thread or email thread |
| **Message ID** | Single line text | — | Unique message identifier |

### Status Options

```
Single Select: Status
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Resolved (green)
🚨 Escalated (red)
⏳ Pending (yellow)
🔄 In Progress (blue)
```

### Category Options

```
Single Select: Category
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🐛 Bug (red)
💰 Billing (green)
✨ Feature (purple)
💬 General (blue)
```

### Priority Options

```
Single Select: Priority
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔴 URGENT (red)
🟠 HIGH (orange)
🟡 MEDIUM (yellow)
🟢 LOW (green)
```

### Sentiment Options

```
Single Select: Sentiment
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
😊 POSITIVE (green)
😐 NEUTRAL (gray)
😞 NEGATIVE (red)
```

### AI Agent Options

```
Single Select: AI Agent
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🐛 Bug Handler (red)
💰 Billing Agent (green)
✨ Feature Agent (purple)
💬 General Agent (blue)
```

### Channel Options

```
Single Select: Channel
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💬 Slack (purple)
📧 Email (blue)
```

### Escalation Urgency Options

```
Single Select: Escalation Urgency
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔴 HIGH (red)
🟡 MEDIUM (yellow)
🟢 LOW (green)
```

### Formula Fields (Optional but Recommended)

```javascript
// Age in Hours
Field: Age (Hours)
Type: Formula
Formula: DATETIME_DIFF(NOW(), {Created}, 'hours')

// SLA Status
Field: SLA Status
Type: Formula
Formula: 
IF(
  {Priority} = "URGENT",
  IF({Age (Hours)} > 1, "⚠️ Overdue", "✅ On Time"),
  IF(
    {Priority} = "HIGH",
    IF({Age (Hours)} > 4, "⚠️ Overdue", "✅ On Time"),
    IF(
      {Priority} = "MEDIUM",
      IF({Age (Hours)} > 24, "⚠️ Overdue", "✅ On Time"),
      IF({Age (Hours)} > 48, "⚠️ Overdue", "✅ On Time")
    )
  )
)

// Resolution Time (if resolved)
Field: Resolution Time
Type: Formula
Formula: 
IF(
  {Status} = "Resolved",
  DATETIME_DIFF({Last Modified}, {Created}, 'hours') & " hours",
  ""
)

// Customer Full Contact
Field: Customer Contact
Type: Formula
Formula: {Customer Name} & " <" & {Customer Email} & ">"
```

---

## 2. Daily Metrics Table

**Table Name:** `Daily Metrics`
**Primary Use:** Historical analytics and trend tracking

### Fields Configuration

| Field Name | Type | Configuration | Description |
|-----------|------|--------------|-------------|
| **Date** | Date | Primary field, Format: Local | Report date |
| **Total Tickets** | Number | Format: Integer | Total tickets that day |
| **Resolved** | Number | Format: Integer | Auto-resolved tickets |
| **Escalated** | Number | Format: Integer | Human-escalated tickets |
| **Pending** | Number | Format: Integer | Unresolved tickets |
| **Resolution Rate** | Number | Precision: 1, Format: Percent | Percentage auto-resolved |
| **Escalation Rate** | Number | Precision: 1, Format: Percent | Percentage escalated |
| **Avg Confidence** | Number | Precision: 2, Format: Decimal | Average AI confidence |
| **Avg Response Time** | Number | Precision: 1, Format: Duration (s) | Average response (seconds) |
| **Median Response Time** | Number | Precision: 1, Format: Duration (s) | Median response (seconds) |
| **Bug Count** | Number | Format: Integer | Bug category tickets |
| **Billing Count** | Number | Format: Integer | Billing category tickets |
| **Feature Count** | Number | Format: Integer | Feature category tickets |
| **General Count** | Number | Format: Integer | General category tickets |
| **Positive Sentiment** | Number | Format: Integer | Positive sentiment count |
| **Neutral Sentiment** | Number | Format: Integer | Neutral sentiment count |
| **Negative Sentiment** | Number | Format: Integer | Negative sentiment count |
| **Slack Tickets** | Number | Format: Integer | Tickets from Slack |
| **Email Tickets** | Number | Format: Integer | Tickets from Email |
| **Cost Savings** | Currency | Format: USD | Daily cost savings |
| **Performance Grade** | Single select | 4 options | Daily grade (A/B/C/D) |
| **Notes** | Long text | — | Manual notes or insights |
| **Created** | Created time | — | Auto-generated |

### Performance Grade Options

```
Single Select: Performance Grade
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🌟 A (green) - 80%+ resolution
✅ B (yellow) - 70-79% resolution
⚠️ C (orange) - 60-69% resolution
❌ D (red) - <60% resolution
```

### Formula Fields (Optional)

```javascript
// Week Number
Field: Week
Type: Formula
Formula: WEEKNUM({Date})

// Month
Field: Month
Type: Formula
Formula: DATETIME_FORMAT({Date}, 'MMMM YYYY')

// Day of Week
Field: Day of Week
Type: Formula
Formula: DATETIME_FORMAT({Date}, 'dddd')

// Total Category Tickets
Field: Total Categories
Type: Formula
Formula: {Bug Count} + {Billing Count} + {Feature Count} + {General Count}

// Sentiment Score (weighted)
Field: Sentiment Score
Type: Formula
Formula: 
ROUND(
  (({Positive Sentiment} * 1) + ({Neutral Sentiment} * 0) + ({Negative Sentiment} * -1)) / 
  {Total Tickets} * 100,
  1
)
```

---

## 3. Agent Performance Table (Optional)

**Table Name:** `Agent Performance`
**Primary Use:** Track individual AI agent metrics

### Fields Configuration

| Field Name | Type | Configuration | Description |
|-----------|------|--------------|-------------|
| **Agent Name** | Single select | Primary field, 4 options | AI agent name |
| **Date** | Date | Format: Local | Performance date |
| **Tickets Handled** | Number | Format: Integer | Total tickets |
| **Resolved** | Number | Format: Integer | Successfully resolved |
| **Escalated** | Number | Format: Integer | Escalated to human |
| **Avg Confidence** | Number | Precision: 2, Format: Decimal | Average confidence |
| **Avg Response Time** | Number | Precision: 1, Format: Duration (s) | Average response time |
| **Resolution Rate** | Number | Precision: 1, Format: Percent | Success rate |
| **Positive Feedback** | Number | Format: Integer | Positive customer responses |
| **Negative Feedback** | Number | Format: Integer | Negative customer responses |
| **Notes** | Long text | — | Performance notes |

### Agent Name Options

```
Single Select: Agent Name
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🐛 Bug Handler
💰 Billing Agent
✨ Feature Agent
💬 General Agent
```

---

## 4. Customer History Table (Optional)

**Table Name:** `Customer History`
**Primary Use:** Track customer interactions over time

### Fields Configuration

| Field Name | Type | Configuration | Description |
|-----------|------|--------------|-------------|
| **Customer Email** | Email | Primary field | Unique customer identifier |
| **Customer Name** | Single line text | — | Customer name |
| **First Contact** | Date | Include time | First ticket date |
| **Last Contact** | Date | Include time | Most recent ticket |
| **Total Tickets** | Number | Format: Integer | Lifetime ticket count |
| **Resolved Tickets** | Number | Format: Integer | Successfully resolved |
| **Escalated Tickets** | Number | Format: Integer | Required human help |
| **Avg Satisfaction** | Number | Precision: 1, Format: Decimal (1-5) | Customer satisfaction score |
| **Most Common Category** | Single line text | — | Primary issue type |
| **VIP Status** | Checkbox | — | High-value customer |
| **Tags** | Multiple select | — | Customer segments/notes |
| **Linked Tickets** | Link to another record | Link to: Support Tickets | All tickets |

---

## Relationships

### Table Relationships

```
Support Tickets ←→ Customer History
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: One-to-Many
Link: Customer Email
Use: Track customer ticket history

Daily Metrics ←→ Support Tickets
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Computed (via date filtering)
Link: Created date
Use: Aggregate daily statistics

Agent Performance ←→ Support Tickets
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Computed (via filtering)
Link: AI Agent + Date
Use: Calculate agent metrics
```

---

## Views

### Support Tickets Table Views

#### 1. All Tickets (Default)
```
View: All Tickets
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Grid
Sort: Created (newest first)
Fields: All
Filter: None
```

#### 2. Unassigned Escalations
```
View: Unassigned Escalations
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Grid
Filter: Status = "Escalated" AND Assigned To is empty
Sort: Priority (URGENT first), Created (oldest first)
Color: Priority field
Use: For auto-assignment workflow
```

#### 3. In Progress
```
View: In Progress
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Kanban
Group by: Assigned To
Filter: Status = "In Progress"
Sort: Priority, Created
Use: Human agent dashboard
```

#### 4. Overdue (with Formula)
```
View: Overdue
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Grid
Filter: SLA Status contains "Overdue"
Sort: Age (Hours) (descending)
Color: Priority field
Use: SLA monitoring
```

#### 5. Today's Tickets
```
View: Today
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Grid
Filter: Created is today
Sort: Created (newest first)
Group by: Category
Use: Daily monitoring
```

#### 6. Negative Sentiment
```
View: Negative Sentiment
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Grid
Filter: Sentiment = "NEGATIVE"
Sort: Priority, Created
Color: Priority field
Use: Customer satisfaction monitoring
```

#### 7. Low Confidence
```
View: Low Confidence
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Grid
Filter: Confidence Score < 0.75
Sort: Confidence Score (ascending)
Use: AI training and improvement
```

#### 8. By Category
```
View: By Category
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Kanban
Group by: Category
Filter: Status != "Resolved"
Sort: Priority, Created
Use: Category-based workflow
```

### Daily Metrics Table Views

#### 1. All Metrics (Default)
```
View: All Metrics
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Grid
Sort: Date (newest first)
Fields: All
```

#### 2. Last 30 Days
```
View: Last 30 Days
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Grid
Filter: Date is within the last 30 days
Sort: Date (newest first)
```

#### 3. Performance Trends
```
View: Performance Trends
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Timeline
Filter: Date is within the last 90 days
Color by: Performance Grade
Use: Trend visualization
```

#### 4. Monthly Summary
```
View: Monthly Summary
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Type: Grid
Group by: Month (formula field)
Fields: Date, Total Tickets, Resolution Rate, Cost Savings
Use: Monthly reporting
```

---

## Automations

### Recommended Airtable Automations

#### 1. Stale Ticket Alert
```
Automation: Stale Ticket Alert
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Trigger: When record matches conditions
  - Status is "In Progress"
  - Age > 7 days

Action: Send Slack message
  - Channel: #escalations
  - Message: "Ticket {Ticket ID} is 7+ days old"
```

#### 2. VIP Customer Notification
```
Automation: VIP Alert
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Trigger: When record created
  - Customer Email in VIP list

Action: Send Slack message
  - Channel: #escalations
  - Message: "VIP customer ticket: {Ticket ID}"
```

#### 3. High Escalation Day Alert
```
Automation: High Escalation Alert
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Trigger: Daily Metrics record created
  - Escalation Rate > 30%

Action: Send email
  - To: support-lead@company.com
  - Subject: "High escalation rate: {Escalation Rate}%"
```

---

## Setup Instructions

### Step 1: Create Base

```bash
1. Go to: https://airtable.com
2. Click "Add a base" → "Start from scratch"
3. Name: "AI Customer Support System"
4. Delete default tables
```

### Step 2: Create Support Tickets Table

```bash
1. Click "+ Add or import" → "Table"
2. Name: "Support Tickets"
3. Add fields following the schema above
4. Configure Single Select options exactly as listed
5. Add formula fields (optional but recommended)
6. Create views listed above
```

### Step 3: Create Daily Metrics Table

```bash
1. Click "+ Add or import" → "Table"
2. Name: "Daily Metrics"
3. Add fields following the schema above
4. Configure Single Select options
5. Add formula fields (optional)
6. Create views
```

### Step 4: Get API Credentials

```bash
1. Go to: https://airtable.com/create/tokens
2. Click "Create new token"
3. Name: "n8n Integration"
4. Add scopes:
   ✅ data.records:read
   ✅ data.records:write
   ✅ schema.bases:read
5. Add your base
6. Click "Create token"
7. Copy token (save securely!)
```

### Step 5: Get Base and Table IDs

```bash
# Base ID
1. Open your base
2. Look at URL: airtable.com/app[BASE_ID]/...
3. Copy BASE_ID (starts with "app")
4. Format: appXXXXXXXXXXXXXX

# Table ID
1. Open table
2. Right-click table name → Copy table URL
3. Extract table ID from URL (starts with "tbl")
4. Format: tblXXXXXXXXXXXXXX

# Or use API:
curl "https://api.airtable.com/v0/meta/bases/YOUR_BASE_ID/tables" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Step 6: Configure n8n

```bash
1. Open n8n
2. Settings → Credentials
3. Add "Airtable Token API"
4. Enter:
   - Name: "Airtable Support DB"
   - Token: [Your token]
5. Test connection
6. Update all workflows with:
   - Base ID: appXXXXXXXXXXXXXX
   - Support Tickets Table ID: tblXXXXXXXXXXXXXX
   - Daily Metrics Table ID: tblYYYYYYYYYYYYYY
```

### Step 7: Test Integration

```bash
1. Import workflow 01-slack-support.json
2. Update Airtable node with your IDs
3. Send test Slack message
4. Verify:
   ✅ Record created in Support Tickets
   ✅ All fields populated correctly
   ✅ Status, Category, Priority set
```

---

## Data Import

### Import Sample Data (Optional)

```csv
# sample-tickets.csv
Ticket ID,Status,Customer Email,Customer Name,Channel,Message,Category,Priority,Sentiment,AI Agent,Confidence Score,Response Time,Created
TKT-20240109-001,Resolved,john@example.com,John Doe,Slack,"App crashes on submit",Bug,HIGH,NEGATIVE,Bug Handler,0.95,3.2,2024-01-09 09:15:00
TKT-20240109-002,Escalated,jane@example.com,Jane Smith,Email,"Need refund for double charge",Billing,URGENT,NEGATIVE,Billing Agent,0.92,2.8,2024-01-09 09:30:00
TKT-20240109-003,Resolved,bob@example.com,Bob Johnson,Slack,"Add dark mode please",Feature,LOW,POSITIVE,Feature Agent,0.98,2.1,2024-01-09 10:00:00
```

**Import Steps:**
1. Save CSV file
2. Open Support Tickets table
3. Click "..." → Import data → CSV
4. Map columns to fields
5. Click "Import"

---

## Best Practices

### Data Integrity

```
✅ DO:
- Always include Ticket ID
- Set Status for every ticket
- Fill Customer Email and Name
- Record AI Agent used
- Track Confidence Score
- Log Response Time

❌ DON'T:
- Manually edit Ticket IDs
- Skip Created timestamps
- Leave Status empty
- Forget to link workflows
```

### Performance

```
Optimization Tips:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Use filters in views (don't load all records)
✅ Limit formula field complexity
✅ Archive old records (>90 days) to separate base
✅ Use linked records instead of duplicating data
✅ Enable caching in n8n workflows
✅ Batch operations when possible
```

### Backup

```
Backup Strategy:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Weekly CSV export (automated)
✅ Monthly base snapshot
✅ Version control schema changes
✅ Document all modifications
✅ Test restore procedures
```

---

## Troubleshooting

### Common Issues

#### ❌ "Invalid table ID"
```
Solution:
1. Verify table ID format (starts with "tbl")
2. Check base ID is correct
3. Ensure token has access to base
4. Re-authenticate n8n credential
```

#### ❌ "Field not found"
```
Solution:
1. Check field name matches exactly (case-sensitive)
2. Verify field exists in Airtable
3. Update workflow if field renamed
4. Check field type compatibility
```

#### ❌ "Rate limit exceeded"
```
Solution:
1. Airtable API: 5 requests/second per base
2. Add delays between batch operations
3. Use Airtable batch operations
4. Consider upgrading Airtable plan
```

#### ❌ "Records not appearing"
```
Solution:
1. Check view filters
2. Verify record matches filter conditions
3. Refresh Airtable
4. Check workflow execution logs
```

---

## Advanced Configuration

### Custom Fields

Add these optional fields for advanced features:

```
Additional Fields:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• Customer Company (Single line text)
• Customer Tier (Single select: Free/Pro/Enterprise)
• Product Area (Single select: Web/Mobile/API/Other)
• Bug Severity (Single select: Critical/High/Medium/Low)
• CSAT Score (Number: 1-5)
• First Response Time (Duration)
• Time to Resolution (Duration)
• Reopened Count (Number)
• Related Tickets (Link to records)
• Internal Notes (Long text)
• Attachments (Attachment)
```

### Webhooks

```
Setup Webhooks:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Enable in Base settings
2. Configure webhook URL
3. Select trigger events:
   - Record created
   - Record updated
   - Record deleted
4. Use in external integrations
```

---

## Migration & Scaling

### Archive Strategy

```
When to Archive:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• Records > 50,000
• Performance degradation
• 90+ days old resolved tickets

How to Archive:
1. Create "Archive" base
2. Copy old records
3. Verify integrity
4. Delete from main base
5. Keep Daily Metrics
```

### Scaling Considerations

```
As Volume Grows:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• Upgrade Airtable plan (Enterprise)
• Implement archiving (90 days)
• Consider data warehouse (Snowflake, BigQuery)
• Use Airtable Sync for reporting
• Optimize view filters
• Batch API operations
```

---

## Schema Version

```
Version: 1.0.0
Created: January 9, 2024
Last Updated: January 9, 2024
Compatible with:
  - n8n workflows v1.0
  - Airtable API v0
  - AI Support System v1.0
```

---

## Support

For issues or questions:
- 📧 Email: support@yourcompany.com
- 💬 Slack: #support-automation
- 📖 Docs: https://docs.yourcompany.com/airtable

---

**End of Schema Documentation** 🎉
