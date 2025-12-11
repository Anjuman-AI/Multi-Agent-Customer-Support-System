# Dashboard Setup Guide

Complete guide to setting up and configuring analytics dashboards for the AI Customer Support System, including real-time monitoring, performance metrics, and business intelligence.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Dashboard Types](#dashboard-types)
3. [Metrics & KPIs](#metrics--kpis)
4. [Real-Time Dashboard Setup](#real-time-dashboard-setup)
5. [Google Sheets Dashboard](#google-sheets-dashboard)
6. [HTML Dashboard](#html-dashboard)
7. [Airtable Dashboard](#airtable-dashboard)
8. [Slack Reports](#slack-reports)
9. [Advanced Analytics](#advanced-analytics)
10. [Monitoring & Alerts](#monitoring--alerts)

---

## Overview

**Purpose:** Provide comprehensive visibility into AI support system performance, customer satisfaction, and business impact.

**Dashboard Architecture:**
```yaml
Layer 1: Real-Time (Immediate feedback)
├── Airtable Interfaces (live data)
├── n8n Execution Monitor (workflow health)
└── Slack Bot Status (system health)

Layer 2: Daily Reports (Operational insights)
├── Slack Daily Summary (automated)
├── HTML Dashboard (web-based)
└── Google Sheets (data analysis)

Layer 3: Strategic Analytics (Business intelligence)
├── Weekly Trends (patterns)
├── Monthly Reports (performance)
└── Executive Dashboard (ROI)
```

**Stakeholder Views:**
```yaml
Support Team:        Real-time ticket queue, escalations
Support Managers:    Daily metrics, team performance
Product Team:        Feature requests, bug patterns
Finance/Leadership:  Cost savings, ROI, customer satisfaction
```

---

## Dashboard Types

### 1. Real-Time Dashboard (Airtable Interface)

**Purpose:** Live view of current ticket queue and system status

**Views:**
- Active tickets by status
- Unassigned escalations
- Recent conversations
- Agent workload
- System health indicators

**Update Frequency:** Real-time (live data)

**Primary Users:** Support agents, managers

---

### 2. Daily Analytics Dashboard (Slack + HTML)

**Purpose:** Daily performance summary and trends

**Metrics:**
- Tickets processed (today)
- Resolution rate
- Escalation rate
- Average response time
- Cost savings
- Customer satisfaction

**Update Frequency:** Daily at 9:00 AM

**Primary Users:** Support team, managers

---

### 3. Google Sheets Dashboard

**Purpose:** Deep dive analysis and custom reporting

**Features:**
- Historical data analysis
- Custom date ranges
- Pivot tables
- Charts and graphs
- Export capabilities

**Update Frequency:** Daily sync + on-demand

**Primary Users:** Analysts, managers, executives

---

### 4. Executive Dashboard

**Purpose:** High-level business metrics and ROI

**Metrics:**
- Total tickets handled
- Auto-resolution rate
- Cost per ticket
- Total cost savings
- Customer NPS
- Team efficiency

**Update Frequency:** Weekly + Monthly

**Primary Users:** Leadership, executives

---

## Metrics & KPIs

### Core Performance Metrics

```yaml
Operational Metrics:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 Total Tickets:           Count of all support requests
✅ Resolved Tickets:        Auto-resolved by AI
🚨 Escalated Tickets:       Sent to human agents
⏳ Pending Tickets:         Awaiting response
🔄 In Progress:             Being handled by humans

Quality Metrics:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 Resolution Rate:         Resolved / Total * 100%
📉 Escalation Rate:         Escalated / Total * 100%
🎯 AI Confidence:           Average confidence score
⚡ Response Time:           Seconds to first response
✨ Customer Satisfaction:   CSAT score (1-5)

Financial Metrics:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💰 Cost per Ticket:         AI: $0.03, Human: $8.33
💵 Total Savings:           (Resolved * 8.33) - (Total * 0.03)
📊 ROI:                     Savings / Investment * 100%

Category Breakdown:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🐛 Bug Reports:             Count + resolution rate
💰 Billing Inquiries:       Count + escalation rate
✨ Feature Requests:        Count + roadmap additions
💬 General Questions:       Count + auto-resolve rate

Sentiment Analysis:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
😊 Positive:                Percentage of positive interactions
😐 Neutral:                 Percentage of neutral interactions
😞 Negative:                Percentage of negative interactions

Channel Distribution:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💬 Slack Tickets:           Count + resolution rate
📧 Email Tickets:           Count + response time
```

### Performance Targets

```yaml
Target KPIs:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Auto-Resolution Rate:    ≥75%
📈 Resolution Rate:         ≥85%
📉 Escalation Rate:         ≤15%
⚡ Response Time:           <5 seconds (p95)
🎯 AI Confidence:           ≥0.85 average
😊 Customer Satisfaction:   ≥4.0/5.0
💰 Cost per Ticket:         ≤$1.00

SLA Targets by Priority:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔴 URGENT:                  First response: 15 minutes
                            Resolution: 1 hour

🟠 HIGH:                    First response: 1 hour
                            Resolution: 4 hours

🟡 MEDIUM:                  First response: 4 hours
                            Resolution: 24 hours

🟢 LOW:                     First response: 24 hours
                            Resolution: 48 hours
```

---

## Real-Time Dashboard Setup

### Airtable Interface Configuration

#### Step 1: Create Dashboard Interface

**Location:** Airtable → Support Tickets Base → Interfaces

1. Click **"Create interface"**
2. Choose **"Dashboard"** template
3. Name: "Support Team Dashboard"

#### Step 2: Add Core Widgets

**Widget 1: Ticket Queue (List)**
```yaml
Widget Type: List
Data Source: Support Tickets table
View: All Tickets (sorted by Priority, Created)
Fields to Display:
  - Ticket ID
  - Status (with color coding)
  - Customer Name
  - Category (with emoji)
  - Priority (with color)
  - Age (Hours)
  - SLA Status
Filters: Status ≠ Resolved
Sort: Priority DESC, Created ASC
Refresh: Real-time
```

**Widget 2: Status Overview (Chart)**
```yaml
Widget Type: Pie Chart
Data Source: Support Tickets table
Grouping: Status
Values: Count of records
Colors:
  - Resolved: Green
  - Escalated: Red
  - Pending: Yellow
  - In Progress: Blue
Filters: Created = Today
```

**Widget 3: Metrics Summary (Number Cards)**
```yaml
Widget 1:
  Title: "Total Today"
  Calculation: Count
  Filter: Created = Today
  Color: Blue

Widget 2:
  Title: "Resolved"
  Calculation: Count
  Filter: Created = Today AND Status = Resolved
  Color: Green

Widget 3:
  Title: "Escalated"
  Calculation: Count
  Filter: Created = Today AND Status = Escalated
  Color: Red

Widget 4:
  Title: "Avg Response Time"
  Calculation: Average of Response Time
  Filter: Created = Today
  Color: Purple
  Format: X.X seconds
```

**Widget 4: Unassigned Escalations (List)**
```yaml
Widget Type: List
Data Source: Support Tickets table
View: Unassigned Escalations
Fields:
  - Ticket ID
  - Customer Name
  - Priority
  - Escalation Reason
  - Age (Hours)
Highlight: Age > 1 hour (red)
Sort: Priority DESC, Created ASC
Alert: Show badge if count > 0
```

**Widget 5: Category Breakdown (Bar Chart)**
```yaml
Widget Type: Horizontal Bar Chart
Data Source: Support Tickets table
Grouping: Category
Values: Count
Filter: Created = Today
Colors: Category-specific (Bug=Red, Billing=Green, etc.)
```

**Widget 6: Response Time Trend (Line Chart)**
```yaml
Widget Type: Line Chart
Data Source: Support Tickets table
X-axis: Created (by hour)
Y-axis: Average Response Time
Filter: Created = Today
Goal Line: 5 seconds (target)
```

#### Step 3: Configure Permissions

```yaml
Access Control:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Support Agents:       View + Edit (assigned tickets)
Support Managers:     View + Edit (all tickets)
Executives:           View only
External Stakeholders: View only (filtered data)

Sharing:
- Internal Link: Share with team
- Public Link: Disabled (sensitive data)
- Password Protection: Enabled for executives
```

#### Step 4: Mobile Optimization

```yaml
Mobile Settings:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Layout: Responsive (auto-adjusts)
Priority Order:
  1. Unassigned Escalations (critical)
  2. Today's Metrics (overview)
  3. Ticket Queue (operational)
  4. Charts (analysis)

Mobile Actions:
- Quick assign: Tap to assign ticket
- Quick status: Swipe to update status
- Quick view: Tap for full details
```

---

## Google Sheets Dashboard

### Setup Overview

**File:** `AI Support Analytics Dashboard.gsheet`

**Sheet Structure:**
```yaml
Sheet 1: Dashboard            (Main view with charts)
Sheet 2: Raw Data            (Imported from Airtable)
Sheet 3: Daily Metrics       (Aggregated data)
Sheet 4: Trends              (Historical analysis)
Sheet 5: Cost Analysis       (Financial metrics)
Sheet 6: Agent Performance   (Team metrics)
```

### Step 1: Set Up Data Import

**Install Airtable API App:**
1. Extensions → Add-ons → Get add-ons
2. Search "Airtable"
3. Install "Airtable Sync for Google Sheets"

**Configure Connection:**
```javascript
// Apps Script: Code.gs
function importAirtableData() {
  const AIRTABLE_API_KEY = PropertiesService.getScriptProperties()
    .getProperty('AIRTABLE_API_KEY');
  const BASE_ID = PropertiesService.getScriptProperties()
    .getProperty('AIRTABLE_BASE_ID');
  const TABLE_NAME = 'Support Tickets';
  
  const url = `https://api.airtable.com/v0/${BASE_ID}/${TABLE_NAME}`;
  const options = {
    'method': 'get',
    'headers': {
      'Authorization': `Bearer ${AIRTABLE_API_KEY}`
    }
  };
  
  const response = UrlFetchApp.fetch(url, options);
  const data = JSON.parse(response.getContentText());
  
  // Clear existing data
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Raw Data');
  sheet.clear();
  
  // Write headers
  const headers = ['Ticket ID', 'Status', 'Category', 'Priority', 'Sentiment', 
                   'Created', 'Response Time', 'AI Confidence', 'Channel'];
  sheet.getRange(1, 1, 1, headers.length).setValues([headers]);
  
  // Write data
  const rows = data.records.map(record => [
    record.fields['Ticket ID'],
    record.fields['Status'],
    record.fields['Category'],
    record.fields['Priority'],
    record.fields['Sentiment'],
    record.fields['Created'],
    record.fields['Response Time'],
    record.fields['Confidence Score'],
    record.fields['Channel']
  ]);
  
  if (rows.length > 0) {
    sheet.getRange(2, 1, rows.length, headers.length).setValues(rows);
  }
  
  Logger.log(`Imported ${rows.length} tickets`);
}

// Schedule: Run daily at 6:00 AM
function setupDailyTrigger() {
  ScriptApp.newTrigger('importAirtableData')
    .timeBased()
    .atHour(6)
    .everyDays(1)
    .create();
}
```

### Step 2: Create Dashboard Sheet

**Layout:**
```
A1:J30 = Dashboard area with charts and metrics
```

**Metric Cards (A1:D5):**
```yaml
Card 1: Total Tickets Today
  Formula: =COUNTIF('Raw Data'!F:F, TODAY())
  Format: Large number, blue background

Card 2: Resolution Rate
  Formula: =COUNTIFS('Raw Data'!F:F, TODAY(), 'Raw Data'!B:B, "Resolved") / 
           COUNTIF('Raw Data'!F:F, TODAY())
  Format: Percentage, green if >85%, red if <75%

Card 3: Average Response Time
  Formula: =AVERAGE(FILTER('Raw Data'!G:G, 'Raw Data'!F:F = TODAY()))
  Format: X.X seconds, green if <5, red if >10

Card 4: Cost Savings Today
  Formula: =(COUNTIFS('Raw Data'!F:F, TODAY(), 'Raw Data'!B:B, "Resolved") * 8.33) - 
           (COUNTIF('Raw Data'!F:F, TODAY()) * 0.03)
  Format: Currency, large green number
```

**Charts:**

**Chart 1: Daily Ticket Volume (A7:F20)**
```yaml
Chart Type: Column Chart
Data Range: 'Daily Metrics'!A:B (Date, Total Tickets)
X-axis: Date (last 30 days)
Y-axis: Count
Title: "Daily Ticket Volume"
Trend Line: Yes (linear)
```

**Chart 2: Resolution Rate Trend (G7:L20)**
```yaml
Chart Type: Line Chart
Data Range: 'Daily Metrics'!A:C (Date, Resolution Rate)
X-axis: Date (last 30 days)
Y-axis: Percentage
Title: "Resolution Rate Trend"
Goal Line: 85% (red dotted line)
Color: Green if above goal, red if below
```

**Chart 3: Category Distribution (A22:F35)**
```yaml
Chart Type: Pie Chart
Data Range: 'Raw Data'!C:C (Category)
Filter: Created = Today
Title: "Today's Tickets by Category"
Colors: Bug=Red, Billing=Green, Feature=Purple, General=Blue
```

**Chart 4: Sentiment Analysis (G22:L35)**
```yaml
Chart Type: Donut Chart
Data Range: 'Raw Data'!E:E (Sentiment)
Filter: Created = Today
Title: "Customer Sentiment Today"
Colors: Positive=Green, Neutral=Yellow, Negative=Red
```

### Step 3: Create Daily Metrics Sheet

**Formula-Based Aggregation:**
```yaml
Column A: Date
  Formula: =UNIQUE('Raw Data'!F:F)
  
Column B: Total Tickets
  Formula: =COUNTIF('Raw Data'!F:F, A2)
  
Column C: Resolved
  Formula: =COUNTIFS('Raw Data'!F:F, A2, 'Raw Data'!B:B, "Resolved")
  
Column D: Escalated
  Formula: =COUNTIFS('Raw Data'!F:F, A2, 'Raw Data'!B:B, "Escalated")
  
Column E: Resolution Rate
  Formula: =C2/B2
  Format: Percentage
  
Column F: Escalation Rate
  Formula: =D2/B2
  Format: Percentage
  
Column G: Avg Response Time
  Formula: =AVERAGEIF('Raw Data'!F:F, A2, 'Raw Data'!G:G)
  Format: Number (seconds)
  
Column H: Avg Confidence
  Formula: =AVERAGEIF('Raw Data'!F:F, A2, 'Raw Data'!H:H)
  Format: Number (0.XX)
  
Column I: Cost Savings
  Formula: =(C2 * 8.33) - (B2 * 0.03)
  Format: Currency
  
Column J: Performance Grade
  Formula: =IF(E2>=0.85, "A", IF(E2>=0.75, "B", IF(E2>=0.65, "C", "D")))
  Format: Conditional (A=Green, B=Blue, C=Orange, D=Red)
```

### Step 4: Advanced Analytics

**Trends Sheet Formulas:**

**Week-over-Week Growth:**
```excel
=('Daily Metrics'!B8 - 'Daily Metrics'!B1) / 'Daily Metrics'!B1
```

**Moving Average (7-day):**
```excel
=AVERAGE('Daily Metrics'!B2:B8)
```

**Forecast (Next 30 days):**
```excel
=FORECAST(TODAY()+30, 'Daily Metrics'!B:B, 'Daily Metrics'!A:A)
```

**Correlation Analysis:**
```excel
=CORREL('Daily Metrics'!B:B, 'Daily Metrics'!E:E)
// Correlation between volume and resolution rate
```

### Step 5: Conditional Formatting

**Alert Rules:**
```yaml
Resolution Rate < 75%:
  Background: Red
  Text: White
  Bold: Yes

Escalation Rate > 20%:
  Background: Orange
  Text: White

Response Time > 10 seconds:
  Background: Yellow
  Text: Black

Cost Savings < $0:
  Background: Red (negative ROI alert)
```

---

## HTML Dashboard

### Setup Overview

**File:** `dashboard.html` (web-based, self-contained)

**Features:**
- Real-time data from Airtable API
- Interactive charts (Chart.js)
- Responsive design
- Auto-refresh every 60 seconds
- Export to PDF
- Mobile-friendly

### Complete HTML Dashboard Code

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Support System Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            padding: 20px;
            color: #333;
        }
        
        .dashboard {
            max-width: 1400px;
            margin: 0 auto;
        }
        
        .header {
            background: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            margin-bottom: 30px;
        }
        
        .header h1 {
            font-size: 32px;
            color: #667eea;
            margin-bottom: 10px;
        }
        
        .header .last-updated {
            color: #666;
            font-size: 14px;
        }
        
        .metrics-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        
        .metric-card {
            background: white;
            padding: 25px;
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            transition: transform 0.3s;
        }
        
        .metric-card:hover {
            transform: translateY(-5px);
        }
        
        .metric-card .label {
            font-size: 14px;
            color: #666;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 10px;
        }
        
        .metric-card .value {
            font-size: 36px;
            font-weight: bold;
            margin-bottom: 5px;
        }
        
        .metric-card .change {
            font-size: 14px;
        }
        
        .metric-card.blue .value { color: #667eea; }
        .metric-card.green .value { color: #10b981; }
        .metric-card.red .value { color: #ef4444; }
        .metric-card.purple .value { color: #764ba2; }
        
        .charts-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        
        .chart-card {
            background: white;
            padding: 25px;
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        
        .chart-card h3 {
            margin-bottom: 20px;
            color: #333;
        }
        
        .alert-banner {
            background: #fef3c7;
            border-left: 4px solid #f59e0b;
            padding: 15px 20px;
            border-radius: 8px;
            margin-bottom: 20px;
            display: none;
        }
        
        .alert-banner.show {
            display: block;
        }
        
        .loading {
            text-align: center;
            padding: 40px;
            color: white;
            font-size: 18px;
        }
        
        @media (max-width: 768px) {
            .metrics-grid {
                grid-template-columns: 1fr;
            }
            
            .charts-grid {
                grid-template-columns: 1fr;
            }
            
            .header h1 {
                font-size: 24px;
            }
        }
    </style>
</head>
<body>
    <div class="dashboard">
        <div class="header">
            <h1>🤖 AI Support System Dashboard</h1>
            <div class="last-updated">Last updated: <span id="lastUpdated">Loading...</span></div>
        </div>
        
        <div class="alert-banner" id="alertBanner">
            <strong>⚠️ Alert:</strong> <span id="alertMessage"></span>
        </div>
        
        <div class="loading" id="loading">Loading dashboard data...</div>
        
        <div id="dashboardContent" style="display:none;">
            <div class="metrics-grid">
                <div class="metric-card blue">
                    <div class="label">Total Tickets Today</div>
                    <div class="value" id="totalTickets">0</div>
                    <div class="change" id="totalChange">+0% vs yesterday</div>
                </div>
                
                <div class="metric-card green">
                    <div class="label">Resolution Rate</div>
                    <div class="value" id="resolutionRate">0%</div>
                    <div class="change" id="resolutionChange">Target: 85%</div>
                </div>
                
                <div class="metric-card red">
                    <div class="label">Escalation Rate</div>
                    <div class="value" id="escalationRate">0%</div>
                    <div class="change" id="escalationChange">Target: <15%</div>
                </div>
                
                <div class="metric-card purple">
                    <div class="label">Cost Savings Today</div>
                    <div class="value" id="costSavings">$0</div>
                    <div class="change">vs traditional support</div>
                </div>
            </div>
            
            <div class="charts-grid">
                <div class="chart-card">
                    <h3>📊 Category Distribution</h3>
                    <canvas id="categoryChart"></canvas>
                </div>
                
                <div class="chart-card">
                    <h3>😊 Sentiment Analysis</h3>
                    <canvas id="sentimentChart"></canvas>
                </div>
                
                <div class="chart-card">
                    <h3>📈 7-Day Ticket Volume</h3>
                    <canvas id="volumeChart"></canvas>
                </div>
                
                <div class="chart-card">
                    <h3>⚡ Response Time Trend</h3>
                    <canvas id="responseTimeChart"></canvas>
                </div>
            </div>
        </div>
    </div>
    
    <script>
        // Configuration
        const AIRTABLE_API_KEY = 'YOUR_AIRTABLE_API_KEY';
        const BASE_ID = 'YOUR_BASE_ID';
        const TABLE_NAME = 'Support Tickets';
        const REFRESH_INTERVAL = 60000; // 60 seconds
        
        // Fetch data from Airtable
        async function fetchTickets() {
            try {
                const response = await fetch(
                    `https://api.airtable.com/v0/${BASE_ID}/${TABLE_NAME}`,
                    {
                        headers: {
                            'Authorization': `Bearer ${AIRTABLE_API_KEY}`
                        }
                    }
                );
                
                if (!response.ok) throw new Error('Failed to fetch data');
                
                const data = await response.json();
                return data.records;
            } catch (error) {
                console.error('Error fetching tickets:', error);
                return [];
            }
        }
        
        // Process and display data
        async function updateDashboard() {
            const tickets = await fetchTickets();
            
            if (tickets.length === 0) {
                document.getElementById('loading').textContent = 'No data available';
                return;
            }
            
            // Filter today's tickets
            const today = new Date().toISOString().split('T')[0];
            const todayTickets = tickets.filter(t => 
                t.fields.Created && t.fields.Created.startsWith(today)
            );
            
            // Calculate metrics
            const total = todayTickets.length;
            const resolved = todayTickets.filter(t => t.fields.Status === 'Resolved').length;
            const escalated = todayTickets.filter(t => t.fields.Status === 'Escalated').length;
            const resolutionRate = total > 0 ? (resolved / total * 100).toFixed(1) : 0;
            const escalationRate = total > 0 ? (escalated / total * 100).toFixed(1) : 0;
            const costSavings = (resolved * 8.33) - (total * 0.03);
            
            // Update metric cards
            document.getElementById('totalTickets').textContent = total;
            document.getElementById('resolutionRate').textContent = resolutionRate + '%';
            document.getElementById('escalationRate').textContent = escalationRate + '%';
            document.getElementById('costSavings').textContent = '$' + costSavings.toFixed(2);
            
            // Update last updated time
            document.getElementById('lastUpdated').textContent = new Date().toLocaleTimeString();
            
            // Show alert if escalation rate is high
            if (escalationRate > 20) {
                const alertBanner = document.getElementById('alertBanner');
                const alertMessage = document.getElementById('alertMessage');
                alertMessage.textContent = `High escalation rate (${escalationRate}%) - Review ticket quality`;
                alertBanner.classList.add('show');
            }
            
            // Update charts
            updateCharts(todayTickets, tickets);
            
            // Show dashboard
            document.getElementById('loading').style.display = 'none';
            document.getElementById('dashboardContent').style.display = 'block';
        }
        
        // Initialize and update charts
        function updateCharts(todayTickets, allTickets) {
            // Category distribution
            const categories = todayTickets.reduce((acc, t) => {
                const cat = t.fields.Category || 'Unknown';
                acc[cat] = (acc[cat] || 0) + 1;
                return acc;
            }, {});
            
            new Chart(document.getElementById('categoryChart'), {
                type: 'doughnut',
                data: {
                    labels: Object.keys(categories),
                    datasets: [{
                        data: Object.values(categories),
                        backgroundColor: ['#ef4444', '#10b981', '#764ba2', '#667eea']
                    }]
                }
            });
            
            // Sentiment analysis
            const sentiments = todayTickets.reduce((acc, t) => {
                const sent = t.fields.Sentiment || 'Neutral';
                acc[sent] = (acc[sent] || 0) + 1;
                return acc;
            }, {});
            
            new Chart(document.getElementById('sentimentChart'), {
                type: 'pie',
                data: {
                    labels: Object.keys(sentiments),
                    datasets: [{
                        data: Object.values(sentiments),
                        backgroundColor: ['#10b981', '#fbbf24', '#ef4444']
                    }]
                }
            });
            
            // 7-day volume (simplified)
            new Chart(document.getElementById('volumeChart'), {
                type: 'line',
                data: {
                    labels: ['Day 1', 'Day 2', 'Day 3', 'Day 4', 'Day 5', 'Day 6', 'Today'],
                    datasets: [{
                        label: 'Tickets',
                        data: [45, 52, 48, 61, 55, 58, todayTickets.length],
                        borderColor: '#667eea',
                        backgroundColor: 'rgba(102, 126, 234, 0.1)',
                        tension: 0.4
                    }]
                }
            });
            
            // Response time trend
            const avgResponseTime = todayTickets
                .filter(t => t.fields['Response Time'])
                .map(t => t.fields['Response Time']);
            
            new Chart(document.getElementById('responseTimeChart'), {
                type: 'bar',
                data: {
                    labels: ['Avg', 'Min', 'Max', 'Target'],
                    datasets: [{
                        label: 'Seconds',
                        data: [
                            avgResponseTime.reduce((a,b) => a+b, 0) / avgResponseTime.length || 0,
                            Math.min(...avgResponseTime) || 0,
                            Math.max(...avgResponseTime) || 0,
                            5
                        ],
                        backgroundColor: ['#667eea', '#10b981', '#ef4444', '#fbbf24']
                    }]
                }
            });
        }
        
        // Initial load
        updateDashboard();
        
        // Auto-refresh every 60 seconds
        setInterval(updateDashboard, REFRESH_INTERVAL);
    </script>
</body>
</html>
```

### Deployment Options

**Option 1: Static File**
```bash
# Simply open dashboard.html in browser
# Update AIRTABLE_API_KEY and BASE_ID in the code
```

**Option 2: Web Server**
```bash
# Deploy to Netlify, Vercel, or GitHub Pages
# Set environment variables for API credentials
```

**Option 3: Internal Server**
```bash
# Host on company intranet
# Add authentication layer
# Enable HTTPS
```

---

## Airtable Dashboard

### Interface Configuration

**(Already covered in Real-Time Dashboard Setup above)**

Additional Airtable-specific features:

### Custom Views for Different Roles

**Support Agent View:**
```yaml
Filters: 
  - Assigned To = Current User OR
  - Status = Unassigned
Sort: Priority DESC, Age ASC
Visible Fields:
  - Ticket ID
  - Customer Name
  - Category
  - Message (truncated)
  - SLA Status
Hide: Cost fields, AI internals
```

**Manager View:**
```yaml
Filters: All tickets
Sort: Created DESC
Visible Fields: All
Grouping: Status, then Priority
Summary: Count, Avg Response Time, Avg Confidence
```

**Executive View:**
```yaml
Filters: Last 30 days
Grouping: Week
Visible Fields:
  - Date ranges
  - Total tickets
  - Resolution rate
  - Cost savings
  - Customer satisfaction
Charts: Weekly trends, Category breakdown
```

---

## Slack Reports

### Daily Summary Configuration

**Report Frequency:** Daily at 9:00 AM (business hours start)

**Report Format:**
```markdown
📊 *Daily Support Summary* - {Date}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Yesterday's Performance:*
• 📨 Total Tickets: 127
• ✅ Resolved: 98 (77%)
• 🚨 Escalated: 29 (23%)
• ⏱️ Avg Response Time: 3.2s

*By Category:*
• 🐛 Bugs: 45 (35%)
• 💰 Billing: 32 (25%)
• ✨ Features: 28 (22%)
• 💬 General: 22 (17%)

*Sentiment:*
• 😊 Positive: 45%
• 😐 Neutral: 42%
• 😞 Negative: 13%

*Cost Savings:*
💵 $814.20 vs traditional support

*Performance Grade:* 🌟 *B* (77% resolution)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Action Items:*
⚠️ 3 urgent tickets need attention
📋 12 unassigned escalations

<View Dashboard|https://airtable.com/...> | <Full Report|https://dashboard.com/...>
```

**n8n Workflow Configuration:**

*See workflow file: `03-analytics.json`*

Key nodes:
1. Schedule Trigger (daily 9 AM)
2. Airtable: Query yesterday's tickets
3. Function: Calculate metrics
4. Function: Format Slack message
5. Slack: Post to #analytics

---

## Advanced Analytics

### Cohort Analysis

**Track customer behavior over time:**

```sql
-- Pseudocode for analysis
SELECT 
  customer_email,
  FIRST_VALUE(created) AS first_contact,
  COUNT(*) AS total_tickets,
  AVG(satisfaction) AS avg_satisfaction,
  SUM(CASE WHEN status = 'Escalated' THEN 1 ELSE 0 END) AS escalations
FROM support_tickets
GROUP BY customer_email
HAVING total_tickets > 3
ORDER BY escalations DESC
```

### Predictive Analytics

**Predict escalation probability:**

```yaml
Features:
  - Customer history (past escalations)
  - Sentiment score
  - Message length
  - Time of day
  - Category
  - Priority keywords

Model: Logistic Regression
Training Data: Last 90 days
Accuracy Target: >85%

Output: Probability score (0-1)
Action: If >0.7, pre-escalate to human
```

### A/B Testing Framework

**Test different AI responses:**

```yaml
Experiment: Response Style A vs B
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Group A: Formal, technical language
Group B: Casual, friendly language

Metrics:
  - Customer satisfaction
  - Resolution rate
  - Follow-up questions
  - Escalation rate

Sample Size: 500 tickets each
Duration: 2 weeks
Success Criteria: CSAT improvement >0.3 points
```

---

## Monitoring & Alerts

### Alert Rules

**Critical Alerts (Immediate):**
```yaml
1. System Down
   Trigger: No tickets processed in 15 minutes
   Action: Page on-call engineer
   Channel: #alerts + SMS

2. API Failure
   Trigger: OpenAI/Airtable/Slack API error rate >10%
   Action: Alert ops team
   Channel: #alerts

3. Escalation Backlog
   Trigger: >10 unassigned escalations
   Action: Alert support manager
   Channel: #escalations + Email

4. VIP Customer Issue
   Trigger: VIP customer escalation
   Action: Page account manager + support lead
   Channel: #vip-alerts + SMS
```

**Warning Alerts (15-minute delay):**
```yaml
1. High Escalation Rate
   Trigger: Escalation rate >25% (hourly)
   Action: Alert support manager
   Channel: #analytics

2. Low Confidence Trend
   Trigger: Avg confidence <0.75 for 1 hour
   Action: Alert AI team
   Channel: #ai-quality

3. Slow Response Time
   Trigger: p95 response time >10s for 30 min
   Action: Alert ops team
   Channel: #performance

4. Customer Satisfaction Drop
   Trigger: CSAT <3.5 for 4 hours
   Action: Alert support manager
   Channel: #analytics
```

**Info Alerts (Daily summary):**
```yaml
1. Daily Metrics
   Trigger: Daily at 9 AM
   Action: Post summary
   Channel: #analytics

2. Weekly Trends
   Trigger: Monday 9 AM
   Action: Post week-over-week analysis
   Channel: #analytics

3. Monthly Report
   Trigger: 1st of month
   Action: Generate executive report
   Channel: Email to leadership
```

### Health Check Dashboard

**System Health Indicators:**

```yaml
🟢 Healthy:     All systems operational
🟡 Degraded:    Minor issues, system functioning
🔴 Critical:    Major issues, immediate attention needed

Components to Monitor:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. n8n Workflows
   - Execution success rate: >98%
   - Avg execution time: <10s
   - Active workflows: All 6

2. API Integrations
   - OpenAI: Response time <3s
   - Airtable: Response time <1s
   - Slack: Response time <1s
   - Gmail: Response time <2s

3. Data Quality
   - Records created: >0 per hour
   - Missing fields: <2%
   - Duplicate tickets: 0

4. Performance
   - End-to-end latency: <5s (p95)
   - Queue depth: <10 pending
   - Error rate: <1%
```

---

## Best Practices

### Dashboard Design

```yaml
✅ Do:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• Start with key metrics (top)
• Use consistent colors
• Show trends over time
• Include context (targets, comparisons)
• Make it actionable
• Update frequently (real-time or daily)
• Mobile-responsive design

❌ Don't:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• Overwhelm with too many metrics
• Use 3D charts (hard to read)
• Show data without context
• Ignore loading states
• Forget about accessibility
• Mix time periods inconsistently
```

### Data Refresh Strategy

```yaml
Real-time:
  - Unassigned escalations
  - System health
  - Active ticket queue

Hourly:
  - Response time trends
  - Current day metrics
  - Agent workload

Daily:
  - Daily summaries
  - Performance reports
  - Cost analysis

Weekly:
  - Trend analysis
  - Week-over-week comparisons
  - Team performance

Monthly:
  - Executive reports
  - Strategic metrics
  - ROI analysis
```

---

## Troubleshooting

### Common Issues

**Issue 1: Data Not Updating**
```yaml
Symptoms: Dashboard shows stale data
Causes:
  - API rate limit exceeded
  - Authentication token expired
  - Network connectivity issues

Solutions:
  1. Check API credentials
  2. Verify rate limits (Airtable: 5 req/s)
  3. Check n8n workflow execution logs
  4. Test API connection manually
```

**Issue 2: Charts Not Rendering**
```yaml
Symptoms: Blank charts, console errors
Causes:
  - Chart.js not loaded
  - Invalid data format
  - Missing data

Solutions:
  1. Check browser console for errors
  2. Verify Chart.js CDN link
  3. Validate data structure
  4. Check for null/undefined values
```

**Issue 3: Slow Dashboard Performance**
```yaml
Symptoms: Long load times, lag
Causes:
  - Too much data loaded
  - Inefficient queries
  - Too many chart animations

Solutions:
  1. Implement pagination
  2. Add date range filters
  3. Cache frequently accessed data
  4. Reduce animation complexity
```

---

## Version

**Version:** 1.0.0  
**Last Updated:** 2024-01-09  
**Dashboard Types:** 4 (Airtable, Google Sheets, HTML, Slack)  
**Metrics Tracked:** 20+ KPIs

---

**End of Dashboard Setup Guide** 📊

Use this guide to set up comprehensive analytics and monitoring for your AI Customer Support System, providing visibility at all organizational levels from real-time operations to strategic business intelligence.
