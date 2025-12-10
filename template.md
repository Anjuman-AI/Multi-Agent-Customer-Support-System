# Google Sheets Dashboard Template

Complete Google Sheets setup for AI Customer Support System analytics and reporting.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Sheet Structure](#sheet-structure)
3. [Data Import Setup](#data-import-setup)
4. [Dashboard Design](#dashboard-design)
5. [Formulas & Functions](#formulas--functions)
6. [Charts & Visualizations](#charts--visualizations)
7. [Pivot Tables](#pivot-tables)
8. [Conditional Formatting](#conditional-formatting)
9. [Setup Instructions](#setup-instructions)
10. [Template Access](#template-access)

---

## Overview

**Purpose:** Real-time analytics dashboard for support metrics, agent performance, and customer insights.

**Features:**
- 📊 Live data sync from Airtable
- 📈 Interactive charts and graphs
- 🎯 KPI tracking
- 👥 Agent performance leaderboard
- 📅 Trend analysis
- 💰 Cost savings calculator
- 🚨 Alert thresholds
- 📱 Mobile-friendly

**Update Frequency:** Every 15 minutes (Airtable sync)

---

## Sheet Structure

### Sheet 1: Dashboard (Main View)

**Purpose:** Executive summary with key metrics

**Layout:**
```
┌─────────────────────────────────────────────────────────────┐
│  AI CUSTOMER SUPPORT DASHBOARD                 [Last Updated]│
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │  TODAY   │  │   THIS   │  │   THIS   │  │   COST   │   │
│  │  TICKETS │  │   WEEK   │  │  MONTH   │  │ SAVINGS  │   │
│  │    45    │  │   217    │  │  1,350   │  │ $8,300   │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
│                                                              │
│  RESOLUTION RATE                    ESCALATION RATE         │
│  ▓▓▓▓▓▓▓▓░░ 80%                    ▓▓░░░░░░░░ 20%          │
│                                                              │
│  ┌────────────────────────┐  ┌───────────────────────────┐ │
│  │  7-DAY TICKET TREND    │  │   CATEGORY BREAKDOWN      │ │
│  │  [Line Chart]          │  │   [Pie Chart]             │ │
│  │                        │  │                           │ │
│  └────────────────────────┘  └───────────────────────────┘ │
│                                                              │
│  ┌────────────────────────┐  ┌───────────────────────────┐ │
│  │  AGENT LEADERBOARD     │  │   SENTIMENT ANALYSIS      │ │
│  │  1. Sarah - 95%        │  │   [Stacked Bar Chart]     │ │
│  │  2. Mike - 92%         │  │                           │ │
│  └────────────────────────┘  └───────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

**Dimensions:** 1920 x 1080 (HD optimized)

---

### Sheet 2: Raw Data (Tickets)

**Purpose:** Import all ticket data from Airtable

**Columns (A-U):**
```
A: Ticket ID
B: Status
C: Customer Email
D: Customer Name
E: Channel
F: Message
G: Subject
H: Category
I: Priority
J: Sentiment
K: AI Agent
L: AI Response
M: Confidence Score
N: Needs Human
O: Escalation Reason
P: Assigned To
Q: Response Time (s)
R: Created
S: Last Modified
T: Thread ID
U: Message ID
```

**Data Source:** Airtable API (via Apps Script or Zapier)

---

### Sheet 3: Raw Data (Daily Metrics)

**Purpose:** Historical metrics data

**Columns (A-R):**
```
A: Date
B: Total Tickets
C: Resolved
D: Escalated
E: Pending
F: Resolution Rate
G: Escalation Rate
H: Avg Confidence
I: Avg Response Time
J: Bug Count
K: Billing Count
L: Feature Count
M: General Count
N: Positive Sentiment
O: Neutral Sentiment
P: Negative Sentiment
Q: Cost Savings
R: Performance Grade
```

---

### Sheet 4: Calculations

**Purpose:** Derived metrics and aggregations

**Sections:**
```yaml
A. Today's Metrics (Rows 2-20)
   - Total tickets today
   - Resolved count
   - Resolution rate
   - Category breakdown
   - Sentiment summary

B. This Week Metrics (Rows 22-40)
   - Weekly totals
   - Averages
   - Trends vs last week

C. This Month Metrics (Rows 42-60)
   - Monthly totals
   - MoM comparison
   - Projections

D. Agent Performance (Rows 62-80)
   - Per-agent stats
   - Rankings
   - Success rates

E. Cost Analysis (Rows 82-100)
   - Daily savings
   - Weekly/monthly totals
   - Annual projections
```

---

### Sheet 5: Agent Leaderboard

**Purpose:** Ranked agent performance

**Columns:**
```
A: Rank
B: Agent Name
C: Tickets Handled
D: Resolved
E: Resolution Rate
F: Avg Confidence
G: Avg Response Time
H: Score (calculated)
I: Badge (emoji)
```

**Ranking Formula:** See [Formulas & Functions](#formulas--functions)

---

### Sheet 6: Trends

**Purpose:** Time-series analysis

**Sections:**
- 7-day ticket volume
- 30-day resolution rate trend
- Monthly comparison chart
- Hour-of-day heatmap
- Day-of-week analysis

---

### Sheet 7: Config

**Purpose:** Dashboard configuration and settings

**Settings:**
```yaml
Refresh Interval: 15 minutes
Airtable Base ID: appXXXXXXXXXXXXXX
Airtable API Key: [Hidden]
Alert Thresholds:
  - Low Resolution Rate: 70%
  - High Escalation Rate: 30%
  - Slow Response Time: 5s
Color Scheme: Custom brand colors
Dashboard Title: "AI Support Analytics"
Company Logo URL: https://...
```

---

## Data Import Setup

### Method 1: Google Sheets Add-on (Recommended)

**Using Airtable Add-on:**

```bash
1. Install "Airtable" add-on
   Extensions → Add-ons → Get add-ons
   Search "Airtable"
   Install and authorize

2. Configure connection:
   Extensions → Airtable → Setup
   Enter API key
   Select base
   Select tables

3. Auto-sync setup:
   Extensions → Airtable → Auto-refresh
   Set interval: 15 minutes
   Select sheets to sync

4. Initial import:
   Extensions → Airtable → Import data
   Map columns
   Import to "Raw Data (Tickets)"
```

---

### Method 2: Apps Script (Advanced)

**Custom import script:**

```javascript
// File: Code.gs

// Configuration
const AIRTABLE_API_KEY = 'YOUR_API_KEY';
const AIRTABLE_BASE_ID = 'appXXXXXXXXXXXXXX';
const TICKETS_TABLE_ID = 'tblXXXXXXXXXXXXXX';
const METRICS_TABLE_ID = 'tblYYYYYYYYYYYYYY';

/**
 * Import tickets from Airtable
 */
function importTickets() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Raw Data (Tickets)');
  
  // Clear existing data (keep headers)
  const lastRow = sheet.getLastRow();
  if (lastRow > 1) {
    sheet.getRange(2, 1, lastRow - 1, sheet.getLastColumn()).clearContent();
  }
  
  // Fetch from Airtable
  const url = `https://api.airtable.com/v0/${AIRTABLE_BASE_ID}/${TICKETS_TABLE_ID}`;
  const options = {
    'method': 'get',
    'headers': {
      'Authorization': `Bearer ${AIRTABLE_API_KEY}`
    }
  };
  
  let allRecords = [];
  let offset = null;
  
  // Paginate through all records
  do {
    const requestUrl = offset ? `${url}?offset=${offset}` : url;
    const response = UrlFetchApp.fetch(requestUrl, options);
    const data = JSON.parse(response.getContentText());
    
    allRecords = allRecords.concat(data.records);
    offset = data.offset;
  } while (offset);
  
  // Transform records to rows
  const rows = allRecords.map(record => {
    const fields = record.fields;
    return [
      fields['Ticket ID'] || '',
      fields['Status'] || '',
      fields['Customer Email'] || '',
      fields['Customer Name'] || '',
      fields['Channel'] || '',
      fields['Message'] || '',
      fields['Subject'] || '',
      fields['Category'] || '',
      fields['Priority'] || '',
      fields['Sentiment'] || '',
      fields['AI Agent'] || '',
      fields['AI Response'] || '',
      fields['Confidence Score'] || 0,
      fields['Needs Human'] || false,
      fields['Escalation Reason'] || '',
      fields['Assigned To'] || '',
      fields['Response Time'] || 0,
      fields['Created'] || '',
      fields['Last Modified'] || '',
      fields['Thread ID'] || '',
      fields['Message ID'] || ''
    ];
  });
  
  // Write to sheet
  if (rows.length > 0) {
    sheet.getRange(2, 1, rows.length, rows[0].length).setValues(rows);
  }
  
  // Update timestamp
  const configSheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Config');
  configSheet.getRange('B2').setValue(new Date());
  
  Logger.log(`Imported ${rows.length} tickets`);
}

/**
 * Import daily metrics from Airtable
 */
function importMetrics() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Raw Data (Daily Metrics)');
  
  // Similar structure to importTickets()
  // ... implementation
}

/**
 * Refresh all data
 */
function refreshAll() {
  importTickets();
  importMetrics();
  SpreadsheetApp.getActiveSpreadsheet().toast('Data refreshed successfully!', 'Success', 3);
}

/**
 * Set up automatic refresh
 */
function setupAutoRefresh() {
  // Delete existing triggers
  const triggers = ScriptApp.getProjectTriggers();
  triggers.forEach(trigger => ScriptApp.deleteTrigger(trigger));
  
  // Create new trigger (every 15 minutes)
  ScriptApp.newTrigger('refreshAll')
    .timeBased()
    .everyMinutes(15)
    .create();
  
  Logger.log('Auto-refresh configured for every 15 minutes');
}

/**
 * Create custom menu
 */
function onOpen() {
  const ui = SpreadsheetApp.getUi();
  ui.createMenu('Airtable Sync')
    .addItem('Refresh Now', 'refreshAll')
    .addItem('Setup Auto-Refresh', 'setupAutoRefresh')
    .addSeparator()
    .addItem('Import Tickets', 'importTickets')
    .addItem('Import Metrics', 'importMetrics')
    .addToUi();
}
```

**Setup:**
```bash
1. Open Google Sheet
2. Extensions → Apps Script
3. Paste code above
4. Update configuration (API key, Base ID)
5. Save project
6. Run "onOpen" function (authorize)
7. Use menu: Airtable Sync → Setup Auto-Refresh
8. Test: Airtable Sync → Refresh Now
```

---

### Method 3: Zapier/Make Integration

**Using Zapier:**

```yaml
Trigger:
  App: Airtable
  Event: New Record in View
  View: Support Tickets > All Tickets
  Polling: Every 15 minutes

Action:
  App: Google Sheets
  Event: Create Spreadsheet Row
  Spreadsheet: AI Support Dashboard
  Worksheet: Raw Data (Tickets)
  Map Fields: All columns
```

---

## Dashboard Design

### Cell Ranges & Formatting

**Header Section (Rows 1-3):**
```
A1:J1 (Merged): "AI CUSTOMER SUPPORT DASHBOARD"
  Font: Roboto, 24pt, Bold
  Background: #1a73e8 (blue)
  Text: White
  Alignment: Center, Middle

K1:L1 (Merged): "Last Updated"
K2:L2: =Config!B2 (timestamp)
  Format: "MM/dd/yyyy hh:mm AM/PM"
```

---

### KPI Cards (Row 5-8)

**Card 1: Today's Tickets (B5:C8)**
```
B5:C5 (Merged): "TODAY"
  Font: 12pt, Bold
  Background: #f1f3f4
  Border: All sides

B6:C8 (Merged): =Calculations!B2 (ticket count)
  Font: 48pt, Bold
  Color: #1a73e8
  Alignment: Center, Middle
  
Conditional Formatting:
  If > Yesterday: Green
  If < Yesterday: Orange
  If = Yesterday: Blue
```

**Card 2: This Week (E5:F8)**
```
E5:F5 (Merged): "THIS WEEK"
E6:F8 (Merged): =Calculations!B22
  Font: 48pt, Bold
  Color: #34a853 (green)
```

**Card 3: This Month (H5:I8)**
```
H5:I5 (Merged): "THIS MONTH"
H6:I8 (Merged): =Calculations!B42
  Font: 48pt, Bold
  Color: #fbbc04 (yellow)
```

**Card 4: Cost Savings (K5:L8)**
```
K5:L5 (Merged): "COST SAVINGS"
K6:L8 (Merged): ="$" & TEXT(Calculations!B82,"#,##0")
  Font: 48pt, Bold
  Color: #0f9d58 (money green)
```

---

### Progress Bars (Row 10-13)

**Resolution Rate Bar (B10:F11)**
```
B10: "RESOLUTION RATE"
  Font: 14pt, Bold

B11:F11: Progress bar
  Formula: =REPT("█",ROUNDUP(Calculations!B4*10,0)) & REPT("░",10-ROUNDUP(Calculations!B4*10,0)) & " " & TEXT(Calculations!B4,"0%")
  Font: Monospace, 16pt
  
Conditional Formatting:
  ≥80%: Green
  70-79%: Yellow
  60-69%: Orange
  <60%: Red
```

**Escalation Rate Bar (H10:L11)**
```
H10: "ESCALATION RATE"
H11:L11: =REPT("█",ROUNDUP(Calculations!B6*10,0)) & REPT("░",10-ROUNDUP(Calculations!B6*10,0)) & " " & TEXT(Calculations!B6,"0%")

Conditional Formatting (reversed):
  <20%: Green
  20-30%: Yellow
  30-40%: Orange
  >40%: Red
```

---

### Charts Section (Rows 15-35)

**Chart 1: 7-Day Trend (B15:F35)**

**Chart Configuration:**
```yaml
Chart Type: Line Chart
Data Range: Trends!A2:B8 (Date, Ticket Count)

Customize:
  Chart Title: "7-Day Ticket Trend"
  X-axis: Date
  Y-axis: Tickets
  Line Color: #1a73e8 (blue)
  Line Width: 3px
  Show Points: Yes
  Smooth Line: Yes
  
  Secondary Series: Resolved Count
  Line Color: #34a853 (green)
  
Legend: Bottom
Grid Lines: Horizontal only
Background: White
```

**Chart 2: Category Breakdown (H15:L25)**

**Chart Configuration:**
```yaml
Chart Type: Pie Chart
Data Range: Calculations!B10:C13

Customize:
  Chart Title: "Category Breakdown"
  Slice Colors:
    - Bug: #ea4335 (red)
    - Billing: #34a853 (green)
    - Feature: #9c27b0 (purple)
    - General: #1a73e8 (blue)
  
  Show Labels: Value & Percentage
  3D: No
  Donut Hole: 40%
```

**Chart 3: Agent Leaderboard (B37:F55)**

**Format:**
```
B37: "🏆 AGENT LEADERBOARD"
  Font: 18pt, Bold

B39:F39 (Header Row):
  "Rank" | "Agent" | "Tickets" | "Resolved" | "Rate"
  Background: #1a73e8
  Text: White
  Bold

B40:F49 (Data):
  =QUERY('Agent Leaderboard'!A2:I,"SELECT A,B,C,D,E ORDER BY A LIMIT 10")
  
Conditional Formatting (E column):
  ≥95%: Gold background
  90-94%: Silver background
  85-89%: Bronze background
```

**Chart 4: Sentiment Analysis (H27:L35)**

**Chart Configuration:**
```yaml
Chart Type: Stacked Bar Chart
Data Range: Calculations!B15:D15 (Positive, Neutral, Negative)

Customize:
  Chart Title: "Sentiment Analysis"
  Orientation: Horizontal
  Stack: 100%
  
  Colors:
    - Positive: #34a853 (green)
    - Neutral: #9e9e9e (gray)
    - Negative: #ea4335 (red)
  
  Show Values: Yes (percentage)
  Legend: Bottom
```

---

## Formulas & Functions

### Today's Metrics

**Cell: Calculations!B2 (Total Tickets Today)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!R:R,"<"&TODAY()+1)
```

**Cell: Calculations!B3 (Resolved Today)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!B:B,"Resolved")
```

**Cell: Calculations!B4 (Resolution Rate Today)**
```excel
=IF(B2=0,0,B3/B2)
```

**Cell: Calculations!B5 (Escalated Today)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!B:B,"Escalated")
```

**Cell: Calculations!B6 (Escalation Rate Today)**
```excel
=IF(B2=0,0,B5/B2)
```

---

### Category Breakdown

**Cell: Calculations!B10 (Bug Count)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!H:H,"Bug")
```

**Cell: Calculations!B11 (Billing Count)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!H:H,"Billing")
```

**Cell: Calculations!B12 (Feature Count)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!H:H,"Feature")
```

**Cell: Calculations!B13 (General Count)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!H:H,"General")
```

---

### Sentiment Analysis

**Cell: Calculations!B15 (Positive Sentiment)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!J:J,"POSITIVE")
```

**Cell: Calculations!C15 (Neutral Sentiment)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!J:J,"NEUTRAL")
```

**Cell: Calculations!D15 (Negative Sentiment)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&TODAY(),'Raw Data (Tickets)'!J:J,"NEGATIVE")
```

---

### Agent Performance

**Cell: Agent Leaderboard!B2 (Agent Name)**
```excel
=UNIQUE(FILTER('Raw Data (Tickets)'!K:K,'Raw Data (Tickets)'!K:K<>""))
```

**Cell: Agent Leaderboard!C2 (Tickets Handled)**
```excel
=COUNTIF('Raw Data (Tickets)'!K:K,B2)
```

**Cell: Agent Leaderboard!D2 (Resolved)**
```excel
=COUNTIFS('Raw Data (Tickets)'!K:K,B2,'Raw Data (Tickets)'!B:B,"Resolved")
```

**Cell: Agent Leaderboard!E2 (Resolution Rate)**
```excel
=IF(C2=0,0,D2/C2)
```

**Cell: Agent Leaderboard!F2 (Avg Confidence)**
```excel
=AVERAGEIF('Raw Data (Tickets)'!K:K,B2,'Raw Data (Tickets)'!M:M)
```

**Cell: Agent Leaderboard!G2 (Avg Response Time)**
```excel
=AVERAGEIF('Raw Data (Tickets)'!K:K,B2,'Raw Data (Tickets)'!Q:Q)
```

**Cell: Agent Leaderboard!H2 (Performance Score)**
```excel
=(E2*0.6)+(F2*0.3)+(1/(G2+1)*0.1)
```

**Cell: Agent Leaderboard!A2 (Rank)**
```excel
=RANK(H2,H:H,0)
```

**Cell: Agent Leaderboard!I2 (Badge)**
```excel
=IF(A2=1,"🥇",IF(A2=2,"🥈",IF(A2=3,"🥉",IF(E2>=0.9,"⭐","✓"))))
```

---

### Cost Savings

**Cell: Calculations!B82 (Daily Savings)**
```excel
=(B3*8.33)-(B2*0.03)
```

**Cell: Calculations!B83 (Weekly Savings)**
```excel
=B82*7
```

**Cell: Calculations!B84 (Monthly Savings)**
```excel
=B82*30
```

**Cell: Calculations!B85 (Annual Projection)**
```excel
=B82*365
```

---

### Trend Analysis

**Cell: Trends!A2 (Date - 7 days ago)**
```excel
=TODAY()-6
```

**Cell: Trends!B2 (Ticket Count)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&A2,'Raw Data (Tickets)'!R:R,"<"&A2+1)
```

**Cell: Trends!C2 (Resolved Count)**
```excel
=COUNTIFS('Raw Data (Tickets)'!R:R,">="&A2,'Raw Data (Tickets)'!B:B,"Resolved")
```

**Drag formulas down to row 8 (7 days)**

---

## Pivot Tables

### Pivot Table 1: Tickets by Category & Priority

**Location:** New sheet "Pivot - Category Priority"

**Configuration:**
```yaml
Source Data: 'Raw Data (Tickets)'!A:U

Rows:
  - Category
  - Priority

Values:
  - Count of Ticket ID

Columns:
  - Status

Filter:
  - Created: Last 30 days

Sort:
  - Category: A to Z
  - Priority: URGENT → LOW
```

---

### Pivot Table 2: Agent Performance Over Time

**Location:** New sheet "Pivot - Agent Time"

**Configuration:**
```yaml
Source Data: 'Raw Data (Tickets)'!A:U

Rows:
  - AI Agent
  - Date (Created) - Group by Week

Values:
  - Count of Ticket ID
  - Average of Confidence Score
  - Average of Response Time

Filter:
  - Created: Last 90 days
```

---

## Conditional Formatting

### Resolution Rate Gradient

**Apply to:** Dashboard!E column (all resolution rates)

**Rules:**
```yaml
Rule 1 (Green):
  Format cells if: Greater than or equal to
  Value: 0.8
  Background: #d4edda
  Text: #155724

Rule 2 (Yellow):
  Format cells if: Between
  Values: 0.7 and 0.8
  Background: #fff3cd
  Text: #856404

Rule 3 (Orange):
  Format cells if: Between
  Values: 0.6 and 0.7
  Background: #ffe5cc
  Text: #cc6600

Rule 4 (Red):
  Format cells if: Less than
  Value: 0.6
  Background: #f8d7da
  Text: #721c24
```

---

### Priority Color Coding

**Apply to:** Raw Data (Tickets)!I column (Priority)

**Rules:**
```yaml
URGENT:
  Background: #ea4335 (red)
  Text: White
  Bold: Yes

HIGH:
  Background: #fbbc04 (orange)
  Text: Black
  Bold: Yes

MEDIUM:
  Background: #fff9c4 (light yellow)
  Text: Black

LOW:
  Background: #c8e6c9 (light green)
  Text: Black
```

---

### Sentiment Icons

**Apply to:** Raw Data (Tickets)!J column (Sentiment)

**Custom formula:**
```excel
=IF(J2="POSITIVE","😊 " & J2,IF(J2="NEGATIVE","😞 " & J2,"😐 " & J2))
```

---

## Setup Instructions

### Quick Start (15 minutes)

```bash
Step 1: Make a Copy
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Access template: [TEMPLATE_URL]
2. File → Make a copy
3. Name: "AI Support Dashboard - [Your Company]"
4. Save to your Google Drive

Step 2: Configure Data Import
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Option A: Using Add-on
  1. Extensions → Add-ons → Get add-ons
  2. Install "Airtable" add-on
  3. Configure connection (see Method 1)

Option B: Using Apps Script
  1. Extensions → Apps Script
  2. Paste provided code
  3. Update credentials
  4. Run setup (see Method 2)

Option C: Using Zapier
  1. Create Zapier account
  2. Setup Zap (see Method 3)

Step 3: Initial Data Load
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Airtable Sync → Refresh Now
2. Wait for data to populate
3. Verify all sheets have data
4. Check formulas calculate correctly

Step 4: Customize
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Update company name in header
2. Add logo (Insert → Image)
3. Adjust colors to brand
4. Modify KPI thresholds in Config
5. Add/remove charts as needed

Step 5: Share
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Click "Share" button
2. Add team members
3. Set permissions:
   - Managers: Can edit
   - Team: Can view
   - Executives: Can view
4. Optional: Publish to web (View only)
```

---

### Advanced Setup

**Data Validation:**

```yaml
Sheet: Raw Data (Tickets)
Column B (Status):
  Criteria: List from range
  Range: 'Config'!D2:D5
  Values: Resolved, Escalated, Pending, In Progress
  Show dropdown: Yes
  Reject input: Yes

Column H (Category):
  Criteria: List from range
  Range: 'Config'!F2:F5
  Values: Bug, Billing, Feature, General

Column I (Priority):
  Criteria: List from range
  Range: 'Config'!H2:H5
  Values: URGENT, HIGH, MEDIUM, LOW
```

---

**Protected Ranges:**

```yaml
Protect:
  - Dashboard sheet (entire)
    Exception: None
    Warning: "Dashboard is auto-generated. Edit Calculations sheet instead."
  
  - Calculations!A1:Z1 (header row)
    Exception: Editors
  
  - Config sheet formulas
    Exception: Owner only
```

---

**Named Ranges:**

```yaml
Create named ranges for easy formula reference:

Name: TodayTicketCount
Range: Calculations!B2

Name: ResolutionRateToday
Range: Calculations!B4

Name: CostSavingsDaily
Range: Calculations!B82

Usage in formulas:
=TodayTicketCount
Instead of:
=Calculations!B2
```

---

## Template Access

### Google Sheets Template

**Option 1: Direct Copy (Recommended)**
```
📊 Template URL:
https://docs.google.com/spreadsheets/d/TEMPLATE_ID/copy

Instructions:
1. Click link above
2. Click "Make a copy"
3. Follow setup instructions
```

**Option 2: Download XLSX**
```
Download pre-configured Excel file:
Compatible with Microsoft Excel 365
Includes all formulas and charts
Manual import required
```

**Option 3: Template Gallery**
```
Coming soon:
Google Workspace Marketplace
"AI Support Dashboard Template"
```

---

## Mobile Optimization

### Mobile Dashboard View

**Responsive Design:**
```yaml
Create new sheet: "Mobile Dashboard"

Layout (Portrait):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Row 1-2: Header
Row 3-6: Today KPI (large)
Row 7-10: This Week KPI
Row 11-14: Resolution Rate (progress bar)
Row 15-25: 7-Day Chart
Row 26-35: Category Chart
Row 36-45: Top 5 Agents (simplified)

Font sizes: 1.5x larger
Charts: Full width
Spacing: Increased padding
```

**Mobile-Friendly Features:**
- Large touch targets (48x48px minimum)
- Simplified charts (less detail)
- Key metrics only
- Swipe-friendly layout
- Offline caching

---

## Automation & Alerts

### Email Reports

**Apps Script for daily email:**

```javascript
function sendDailyReport() {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const dashboard = ss.getSheetByName('Dashboard');
  
  // Get KPIs
  const todayTickets = dashboard.getRange('C6').getValue();
  const resolutionRate = dashboard.getRange('E11').getValue();
  
  // Create email
  const recipient = 'team@company.com';
  const subject = 'Daily Support Dashboard - ' + new Date().toLocaleDateString();
  const body = `
    Daily Support Metrics:
    
    Today's Tickets: ${todayTickets}
    Resolution Rate: ${(resolutionRate * 100).toFixed(1)}%
    
    View full dashboard:
    ${ss.getUrl()}
  `;
  
  // Send email
  MailApp.sendEmail(recipient, subject, body);
}

// Setup trigger: Daily at 6 PM
function setupDailyEmail() {
  ScriptApp.newTrigger('sendDailyReport')
    .timeBased()
    .atHour(18)
    .everyDays(1)
    .create();
}
```

---

### Slack Notifications

**Webhook integration:**

```javascript
function sendSlackAlert() {
  const WEBHOOK_URL = 'https://hooks.slack.com/services/YOUR/WEBHOOK/URL';
  
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const calcs = ss.getSheetByName('Calculations');
  
  // Check thresholds
  const resolutionRate = calcs.getRange('B4').getValue();
  const escalationRate = calcs.getRange('B6').getValue();
  
  if (resolutionRate < 0.7 || escalationRate > 0.3) {
    const payload = {
      'text': '🚨 Alert: Dashboard metrics outside thresholds',
      'blocks': [
        {
          'type': 'section',
          'text': {
            'type': 'mrkdwn',
            'text': `*Resolution Rate:* ${(resolutionRate*100).toFixed(1)}% (Target: ≥70%)\n*Escalation Rate:* ${(escalationRate*100).toFixed(1)}% (Target: ≤30%)`
          }
        }
      ]
    };
    
    const options = {
      'method': 'post',
      'contentType': 'application/json',
      'payload': JSON.stringify(payload)
    };
    
    UrlFetchApp.fetch(WEBHOOK_URL, options);
  }
}
```

---

## Best Practices

### Performance Optimization

```yaml
DO:
✅ Use ARRAYFORMULA for repeated calculations
✅ Limit IMPORTRANGE usage
✅ Use named ranges
✅ Cache API responses
✅ Minimize volatile functions (NOW, TODAY)
✅ Use filters instead of multiple conditions
✅ Close unused sheets

DON'T:
❌ Use IMPORTDATA frequently
❌ Create circular references
❌ Use entire column references (A:A)
❌ Over-format cells
❌ Create too many charts
```

---

### Maintenance Checklist

```yaml
Daily:
□ Check data refresh status
□ Verify no #ERROR cells
□ Review KPIs

Weekly:
□ Archive old data (>90 days)
□ Check Apps Script quota
□ Update thresholds if needed

Monthly:
□ Review formula accuracy
□ Optimize slow calculations
□ Update documentation
□ Backup sheet
```

---

## Troubleshooting

### Common Issues

**❌ Data not refreshing**
```
Solutions:
1. Check Apps Script triggers
2. Verify API credentials
3. Check Airtable rate limits
4. Manually run refresh
5. Review error logs
```

**❌ Formulas showing #REF!**
```
Solutions:
1. Check sheet names match
2. Verify ranges exist
3. Fix broken references
4. Restore from backup
```

**❌ Charts not updating**
```
Solutions:
1. Refresh data ranges
2. Check chart data source
3. Recalculate sheet (Ctrl+F9)
4. Recreate chart
```

---

## Support

For template questions:
- 📧 Email: support@yourcompany.com
- 💬 Slack: #dashboard-help
- 📖 Video Tutorial: [Coming soon]

---

**Template Version:** 1.0.0
**Last Updated:** January 9, 2024
**Compatible with:** Google Sheets, Excel 365

---

**End of Template Documentation** 📊
