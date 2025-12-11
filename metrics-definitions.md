# Metrics Definitions

Comprehensive reference guide for all metrics, KPIs, and calculations used in the AI Customer Support System analytics and reporting.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Core Operational Metrics](#core-operational-metrics)
3. [Quality Metrics](#quality-metrics)
4. [Financial Metrics](#financial-metrics)
5. [Performance Metrics](#performance-metrics)
6. [Customer Experience Metrics](#customer-experience-metrics)
7. [AI Performance Metrics](#ai-performance-metrics)
8. [Team Efficiency Metrics](#team-efficiency-metrics)
9. [Category & Channel Metrics](#category--channel-metrics)
10. [Advanced Metrics](#advanced-metrics)
11. [Calculated Fields](#calculated-fields)
12. [Benchmarks & Targets](#benchmarks--targets)

---

## Overview

**Purpose:** Standardize metric definitions across all dashboards, reports, and analytics to ensure consistent interpretation and decision-making.

**Metric Types:**
```yaml
📊 Volume Metrics:        Count-based measurements
📈 Rate Metrics:          Percentage-based calculations  
⏱️ Time Metrics:         Duration and speed measurements
💰 Financial Metrics:     Cost and savings calculations
🎯 Quality Metrics:       Accuracy and effectiveness measures
😊 Experience Metrics:    Customer satisfaction indicators
```

**Calculation Frequency:**
```yaml
Real-time:     Updated on every ticket
Hourly:        Aggregated every hour
Daily:         Calculated at midnight
Weekly:        Monday 12:00 AM
Monthly:       1st of month 12:00 AM
```

---

## Core Operational Metrics

### M001: Total Tickets

**Definition:** Total count of support requests received across all channels.

**Formula:**
```sql
COUNT(ticket_id)
WHERE created >= [period_start] 
  AND created <= [period_end]
```

**Calculation:**
```javascript
totalTickets = supportTickets.length
```

**Filters:**
- Can be filtered by date range
- Can be filtered by channel (Slack, Email)
- Can be filtered by category
- Can be filtered by priority

**Example:**
```
Period: January 9, 2024
Total Tickets: 127
Breakdown:
  - Slack: 89 (70%)
  - Email: 38 (30%)
```

**Interpretation:**
- High volume: Indicates product issues or growth
- Low volume: Could indicate reduced engagement or improved product
- Trends matter more than absolute numbers

**Target:** No fixed target (baseline varies by product stage)

**Related Metrics:** M002, M003, M004, M005

---

### M002: Resolved Tickets

**Definition:** Count of tickets successfully handled by AI without human escalation.

**Formula:**
```sql
COUNT(ticket_id)
WHERE status = 'Resolved'
  AND created >= [period_start]
  AND created <= [period_end]
```

**Calculation:**
```javascript
resolvedTickets = supportTickets.filter(
  ticket => ticket.status === 'Resolved'
).length
```

**What Counts as "Resolved":**
```yaml
✅ AI provided helpful response
✅ Customer confirmed resolution (if applicable)
✅ No escalation triggered
✅ Status = "Resolved" in Airtable

❌ Does NOT count:
  - Escalated to human
  - Pending customer response
  - In progress
```

**Example:**
```
Total Tickets: 127
Resolved: 98
Resolution Rate: 77.2%
```

**Interpretation:**
- High resolution: AI is handling tickets well
- Improving trend: AI is learning/improving
- Declining trend: May need prompt tuning

**Target:** ≥75% of total tickets

**Related Metrics:** M001, M006 (Resolution Rate)

---

### M003: Escalated Tickets

**Definition:** Count of tickets that required human agent intervention.

**Formula:**
```sql
COUNT(ticket_id)
WHERE status = 'Escalated'
  AND created >= [period_start]
  AND created <= [period_end]
```

**Calculation:**
```javascript
escalatedTickets = supportTickets.filter(
  ticket => ticket.status === 'Escalated'
).length
```

**Escalation Triggers:**
```yaml
Automatic Escalation:
  - Priority = URGENT
  - Confidence < 0.70
  - Sentiment = NEGATIVE + Priority = HIGH
  - Billing dispute > $100
  - Legal keywords detected
  - VIP customer + Priority > MEDIUM

Manual Escalation:
  - Customer requests human
  - Complex multi-issue request
  - AI identifies need for human
```

**Example:**
```
Total Tickets: 127
Escalated: 29
Escalation Rate: 22.8%

Breakdown:
  - Auto-escalated: 24 (82.8%)
  - AI-identified: 5 (17.2%)
```

**Interpretation:**
- High escalation: Complex tickets or AI needs tuning
- By urgency: Expected for URGENT tickets
- By confidence: Low confidence → more escalations

**Target:** ≤15% of total tickets (excluding URGENT)

**Related Metrics:** M001, M007 (Escalation Rate)

---

### M004: Pending Tickets

**Definition:** Count of tickets awaiting response (customer reply or human assignment).

**Formula:**
```sql
COUNT(ticket_id)
WHERE status = 'Pending'
  AND created >= [period_start]
  AND created <= [period_end]
```

**Calculation:**
```javascript
pendingTickets = supportTickets.filter(
  ticket => ticket.status === 'Pending'
).length
```

**What "Pending" Means:**
```yaml
Scenarios:
  - AI waiting for customer to answer questions
  - Multi-turn conversation in progress
  - Escalated but not yet assigned to human
  - Human requested more info from customer
```

**Example:**
```
Current Pending: 12 tickets
Age Distribution:
  - < 1 hour: 8 tickets
  - 1-4 hours: 3 tickets
  - > 4 hours: 1 ticket (⚠️ SLA risk)
```

**Interpretation:**
- High pending: Bottleneck in process
- Old pending: SLA violations likely
- Trend matters: Growing vs. stable

**Target:** 
- <10% of active tickets pending
- <1 hour average age for URGENT
- <4 hours average age for HIGH

**Related Metrics:** M025 (SLA Compliance)

---

### M005: In Progress Tickets

**Definition:** Count of tickets actively being handled by human agents.

**Formula:**
```sql
COUNT(ticket_id)
WHERE status = 'In Progress'
  AND assigned_to IS NOT NULL
  AND created >= [period_start]
  AND created <= [period_end]
```

**Calculation:**
```javascript
inProgressTickets = supportTickets.filter(
  ticket => ticket.status === 'In Progress' && ticket.assignedTo
).length
```

**What "In Progress" Means:**
```yaml
Criteria:
  - Ticket assigned to human agent
  - Agent actively working on it
  - Not yet resolved
  - Customer may be engaged in conversation
```

**Example:**
```
In Progress: 8 tickets

By Agent:
  - Sarah: 3 tickets
  - Mike: 3 tickets  
  - Emma: 2 tickets

By Priority:
  - URGENT: 2
  - HIGH: 4
  - MEDIUM: 2
```

**Interpretation:**
- High count: Team may be overloaded
- Even distribution: Good load balancing
- Stuck tickets: May need escalation

**Target:** 
- <10 tickets per agent
- Average resolution time by priority
- No tickets stuck >24 hours

**Related Metrics:** M029 (Agent Utilization)

---

## Quality Metrics

### M006: Resolution Rate

**Definition:** Percentage of tickets successfully resolved by AI without human intervention.

**Formula:**
```sql
(COUNT(ticket_id WHERE status = 'Resolved') / 
 COUNT(ticket_id)) * 100
```

**Calculation:**
```javascript
resolutionRate = (resolvedTickets / totalTickets) * 100
```

**Example:**
```
Total Tickets: 127
Resolved: 98
Resolution Rate: 77.17%

By Category:
  - General: 85% (high - simple questions)
  - Bug: 65% (medium - needs investigation)
  - Billing: 45% (low - often needs human)
  - Feature: 90% (high - info requests)
```

**Interpretation:**
```yaml
90-100%: Excellent (but verify quality)
85-90%:  Very Good
75-85%:  Good (target range)
65-75%:  Fair (needs improvement)
<65%:    Poor (significant issues)

Context Matters:
  - Higher expected for simple categories
  - Lower acceptable for complex categories
  - Trend more important than single point
```

**Target:** ≥75% overall, ≥85% for General category

**Improvement Strategies:**
- Improve AI prompts for low-confidence categories
- Add more training examples
- Enhance knowledge base
- Better routing logic

**Related Metrics:** M002, M007

---

### M007: Escalation Rate

**Definition:** Percentage of tickets that required human intervention.

**Formula:**
```sql
(COUNT(ticket_id WHERE status = 'Escalated') / 
 COUNT(ticket_id)) * 100
```

**Calculation:**
```javascript
escalationRate = (escalatedTickets / totalTickets) * 100
```

**Example:**
```
Total Tickets: 127
Escalated: 29
Escalation Rate: 22.83%

By Reason:
  - Low confidence: 12 (41%)
  - URGENT priority: 8 (28%)
  - Billing dispute: 5 (17%)
  - Customer request: 4 (14%)
```

**Interpretation:**
```yaml
<10%:    Excellent (but verify not missing issues)
10-15%:  Very Good (target range)
15-25%:  Fair (acceptable for complex domains)
25-35%:  Poor (needs attention)
>35%:    Critical (AI not effective)

Acceptable Higher Rates:
  - Early product launch
  - Complex technical product
  - High-value customers (better to escalate)
  - Regulated industries (compliance needs)
```

**Target:** ≤15% overall (excluding mandatory URGENT escalations)

**What's Acceptable:**
```yaml
Always Escalate:
  - URGENT priority (100%)
  - VIP customers with issues
  - Legal/compliance matters
  - Billing disputes >$100

Should Escalate:
  - Confidence <0.70
  - Negative sentiment + HIGH priority
  - Complex multi-part questions

Avoid Escalating:
  - Simple questions
  - High confidence (>0.90)
  - Standard workflows
```

**Related Metrics:** M003, M006

---

### M008: AI Confidence Score

**Definition:** Average confidence level of AI categorization and routing decisions.

**Formula:**
```sql
AVG(confidence_score)
WHERE created >= [period_start]
  AND created <= [period_end]
```

**Calculation:**
```javascript
avgConfidence = supportTickets.reduce(
  (sum, ticket) => sum + ticket.confidenceScore, 0
) / supportTickets.length
```

**Score Range:** 0.00 to 1.00 (0% to 100%)

**Example:**
```
Average Confidence: 0.87 (87%)

Distribution:
  - High (0.90-1.00): 45% of tickets
  - Medium (0.75-0.89): 38% of tickets
  - Low (0.60-0.74): 12% of tickets
  - Very Low (<0.60): 5% of tickets

By Category:
  - Feature requests: 0.94 (clear language)
  - General questions: 0.89 (straightforward)
  - Bug reports: 0.85 (sometimes vague)
  - Billing: 0.82 (context needed)
```

**Interpretation:**
```yaml
0.95-1.00: Extremely confident (excellent)
0.85-0.95: High confidence (very good)
0.75-0.85: Medium confidence (good)
0.65-0.75: Low confidence (fair - consider escalation)
<0.65:     Very low (poor - auto-escalate)

Patterns to Watch:
  - Declining trend: Prompt drift or data issues
  - Category variance: Some agents need tuning
  - Correlation with resolution: High confidence → high resolution
```

**Target:** ≥0.85 average, <10% of tickets with confidence <0.70

**Improvement Strategies:**
- Add more training examples for low-confidence categories
- Improve prompt clarity and specificity
- Better keyword detection
- More context in system prompts

**Related Metrics:** M007 (Escalation Rate - low confidence triggers escalation)

---

### M009: First Contact Resolution (FCR)

**Definition:** Percentage of tickets resolved in the first interaction without follow-ups.

**Formula:**
```sql
(COUNT(ticket_id WHERE status = 'Resolved' AND turn_count = 1) /
 COUNT(ticket_id WHERE status = 'Resolved')) * 100
```

**Calculation:**
```javascript
fcrRate = (singleTurnResolved / totalResolved) * 100
```

**Example:**
```
Resolved Tickets: 98
First Contact Resolution: 82
FCR Rate: 83.67%

Multi-turn Resolutions:
  - 2 turns: 12 tickets (12.2%)
  - 3+ turns: 4 tickets (4.1%)
```

**Interpretation:**
```yaml
>90%:    Excellent (very efficient)
80-90%:  Very Good (target range)
70-80%:  Good (acceptable)
60-70%:  Fair (needs improvement)
<60%:    Poor (too many follow-ups)

Why Multi-turn Happens:
  - Customer needs clarification
  - Complex troubleshooting
  - Missing initial information
  - AI asks diagnostic questions
```

**Target:** ≥80% FCR rate

**Improvement Strategies:**
- Anticipate common follow-up questions
- Ask all necessary questions upfront
- Provide comprehensive initial responses
- Better initial triage

**Related Metrics:** M010 (Average Turns per Ticket)

---

### M010: Average Turns per Ticket

**Definition:** Average number of back-and-forth exchanges per resolved ticket.

**Formula:**
```sql
AVG(turn_count)
WHERE status = 'Resolved'
  AND created >= [period_start]
  AND created <= [period_end]
```

**Calculation:**
```javascript
avgTurns = resolvedTickets.reduce(
  (sum, ticket) => sum + ticket.turnCount, 0
) / resolvedTickets.length
```

**Turn Definition:** Each customer message + AI response = 1 turn

**Example:**
```
Average Turns: 1.3

Distribution:
  - 1 turn: 82 tickets (83.7%)
  - 2 turns: 12 tickets (12.2%)
  - 3 turns: 3 tickets (3.1%)
  - 4+ turns: 1 ticket (1.0%)

By Category:
  - Feature: 1.1 (simple info)
  - General: 1.2 (straightforward)
  - Bug: 1.8 (troubleshooting)
  - Billing: 2.1 (context gathering)
```

**Interpretation:**
```yaml
1.0-1.5:  Excellent (efficient)
1.5-2.0:  Very Good (acceptable)
2.0-2.5:  Good (some complexity)
2.5-3.0:  Fair (lots of back-and-forth)
>3.0:     Poor (inefficient, consider escalation)

Context:
  - Bug reports: Higher turns expected (troubleshooting)
  - Simple questions: Should be 1-2 turns
  - Billing: May need multiple exchanges for context
```

**Target:** ≤2.0 average turns

**Related Metrics:** M009 (FCR)

---

## Financial Metrics

### M011: Cost per Ticket (AI)

**Definition:** Average cost to process one ticket using AI automation.

**Formula:**
```
Cost per AI Ticket = OpenAI API Cost / Total Tickets
```

**Detailed Calculation:**
```javascript
// OpenAI API pricing (as of 2024)
const INPUT_COST_PER_1K = 0.003  // $0.003 per 1K input tokens
const OUTPUT_COST_PER_1K = 0.015 // $0.015 per 1K output tokens

// Average tokens per ticket
const avgInputTokens = 1200   // Customer message + system prompt
const avgOutputTokens = 800   // AI response

// Calculate per-ticket cost
const inputCost = (avgInputTokens / 1000) * INPUT_COST_PER_1K
const outputCost = (avgOutputTokens / 1000) * OUTPUT_COST_PER_1K

costPerTicket = inputCost + outputCost
// = (1200/1000 * 0.003) + (800/1000 * 0.015)
// = 0.0036 + 0.012
// = 0.0156 ≈ $0.016

// Rounded benchmark: $0.03 (includes infrastructure overhead)
```

**Components:**
```yaml
Direct Costs:
  - OpenAI API calls: ~$0.016/ticket
  - Airtable API: ~$0.001/ticket
  - n8n infrastructure: ~$0.003/ticket
  - Slack/Gmail API: ~$0.001/ticket
  
Overhead:
  - Infrastructure: ~$0.005/ticket
  - Monitoring: ~$0.003/ticket
  - Storage: ~$0.001/ticket

Total: ~$0.03/ticket
```

**Example:**
```
Daily Volume: 127 tickets
AI Processing Cost: 127 × $0.03 = $3.81/day

Monthly Projection:
  - 3,810 tickets/month
  - Cost: $114.30/month
  - Annual: $1,371.60/year
```

**Target:** ≤$0.05 per ticket

**Related Metrics:** M012, M013, M014

---

### M012: Cost per Ticket (Human)

**Definition:** Average cost to process one ticket using traditional human support.

**Formula:**
```
Cost per Human Ticket = (Agent Salary + Overhead) / (Tickets per Hour × Hours per Month)
```

**Detailed Calculation:**
```javascript
// Assumptions for US-based support agent
const annualSalary = 50000        // $50K/year
const benefitsMultiplier = 1.3    // 30% benefits/overhead
const totalCompensation = annualSalary * benefitsMultiplier // $65K

const workingMonthsPerYear = 12
const monthlyCompensation = totalCompensation / workingMonthsPerYear // $5,416.67

const hoursPerMonth = 160         // 40 hours/week × 4 weeks
const hourlyRate = monthlyCompensation / hoursPerMonth // $33.85/hour

const ticketsPerHour = 4          // Industry average
const costPerTicket = hourlyRate / ticketsPerHour // $8.46

// Rounded benchmark: $8.33/ticket
```

**Cost Breakdown:**
```yaml
Support Agent (T1):
  - Salary: $50K/year
  - Benefits: 30%
  - Overhead: Workspace, tools, management
  - Tickets/hour: 4
  - Cost/ticket: $8.33

Senior Agent (T2):
  - Salary: $70K/year
  - Benefits: 30%
  - Cost/ticket: $11.67

Specialist (T3):
  - Salary: $90K/year
  - Benefits: 30%
  - Cost/ticket: $15.00
```

**Example:**
```
Traditional Support Costs:
  - Daily: 127 tickets × $8.33 = $1,057.91
  - Monthly: 3,810 tickets × $8.33 = $31,738.30
  - Annual: 45,720 tickets × $8.33 = $380,647.60
```

**Industry Benchmarks:**
```yaml
B2B SaaS:       $10-15/ticket
B2C Tech:       $6-10/ticket
Enterprise:     $15-25/ticket
```

**Related Metrics:** M011, M013

---

### M013: Cost Savings per Ticket

**Definition:** Difference between traditional human support cost and AI automation cost.

**Formula:**
```
Savings per Ticket = Cost per Human Ticket - Cost per AI Ticket
```

**Calculation:**
```javascript
savingsPerTicket = 8.33 - 0.03 // $8.30 per ticket
```

**Example:**
```
Per Ticket Savings: $8.30

Daily Savings (98 resolved tickets):
  - Traditional cost: 98 × $8.33 = $816.34
  - AI cost: 98 × $0.03 = $2.94
  - Savings: $813.40

Monthly Savings (2,940 resolved):
  - Traditional: $24,490.20
  - AI: $88.20
  - Savings: $24,402.00

Annual Savings (35,280 resolved):
  - Traditional: $293,882.40
  - AI: $1,058.40
  - Savings: $292,824.00
```

**Interpretation:**
```yaml
Per Ticket: $8.30 savings (99.6% reduction)
ROI: Massive (typical system costs ~$30K setup + $3K/year)
Payback Period: <2 months for typical implementation
```

**Target:** ≥$8.00 per ticket (96% cost reduction)

**Important Notes:**
```yaml
Conservative Calculation:
  - Only counts resolved tickets (not escalated)
  - Doesn't include value of faster response times
  - Doesn't include customer retention value
  - Doesn't include 24/7 availability benefit

Full Value Includes:
  - Direct cost savings: $8.30/ticket
  - Faster response: +$2.00/ticket value
  - 24/7 availability: +$1.00/ticket value
  - Consistency: +$0.50/ticket value
  Total: ~$11.80/ticket value
```

**Related Metrics:** M011, M012, M014

---

### M014: Total Cost Savings

**Definition:** Total cumulative savings from AI automation over a time period.

**Formula:**
```
Total Savings = (Resolved Tickets × Human Cost) - (Total Tickets × AI Cost)
```

**Calculation:**
```javascript
totalSavings = (resolvedTickets × 8.33) - (totalTickets × 0.03)
```

**Example:**
```
Daily Calculation:
  - Total Tickets: 127
  - Resolved: 98
  - Traditional Cost: 98 × $8.33 = $816.34
  - AI Cost: 127 × $0.03 = $3.81
  - Net Savings: $816.34 - $3.81 = $812.53

Monthly Projection (30 days):
  - Savings: $812.53 × 30 = $24,375.90

Annual Projection:
  - Savings: $812.53 × 365 = $296,573.45
```

**ROI Calculation:**
```yaml
Implementation Costs:
  - Initial setup: $30,000 (one-time)
  - Monthly operations: $250 (OpenAI + infrastructure)
  - Annual operations: $3,000

First Year:
  - Revenue: $296,573 (savings)
  - Costs: $33,000 (setup + operations)
  - Net ROI: $263,573
  - ROI %: 798%
  - Payback period: 37 days

Ongoing Years:
  - Revenue: $296,573 (savings)
  - Costs: $3,000 (operations only)
  - Net ROI: $293,573
  - ROI %: 9,786%
```

**Interpretation:**
```yaml
Massive ROI:
  - 99.6% cost reduction per ticket
  - Payback in ~1 month
  - ~800% first-year ROI
  - ~9,800% ongoing ROI

Additional Value (Not Counted):
  - Faster response times
  - 24/7 availability
  - Consistent quality
  - Scalability (handle 10x volume without 10x cost)
  - Customer satisfaction improvements
  - Team focus on complex work
```

**Target:** 
- First year: >$250,000 savings
- Break-even: <2 months
- ROI: >500% first year

**Related Metrics:** M011, M012, M013

---

## Performance Metrics

### M015: Response Time (First Response)

**Definition:** Time from ticket creation to first AI response.

**Formula:**
```sql
AVG(TIMESTAMPDIFF(SECOND, created_at, first_response_at))
```

**Calculation:**
```javascript
responseTime = firstResponseTimestamp - createdTimestamp
avgResponseTime = tickets.reduce(
  (sum, ticket) => sum + ticket.responseTime, 0
) / tickets.length
```

**Example:**
```
Average Response Time: 2.8 seconds

Distribution:
  - p50 (median): 2.3 seconds
  - p95: 4.8 seconds
  - p99: 7.2 seconds
  - Max: 12.1 seconds

By Channel:
  - Slack: 2.1s (webhook instant)
  - Email: 3.5s (polling every 60s)
```

**Interpretation:**
```yaml
<3s:      Excellent (near-instant)
3-5s:     Very Good (fast)
5-10s:    Good (acceptable)
10-30s:   Fair (noticeable delay)
>30s:     Poor (too slow)

Industry Comparison:
  - AI system: 2-5 seconds
  - Human chat: 2-5 minutes
  - Human email: 2-24 hours
```

**Target:** 
- p95 < 5 seconds
- p99 < 10 seconds
- Average < 3 seconds

**What Affects Response Time:**
```yaml
Factors:
  - OpenAI API latency: 1-3s
  - n8n processing: 0.5-1s
  - Airtable writes: 0.3-0.5s
  - Network latency: 0.2-1s
  - Message complexity: +0-2s

Optimization Tips:
  - Use GPT-3.5-turbo for faster responses
  - Optimize prompts (shorter = faster)
  - Cache common responses
  - Parallel API calls where possible
```

**Related Metrics:** M016, M025

---

### M016: Resolution Time (Total)

**Definition:** Total time from ticket creation to final resolution.

**Formula:**
```sql
AVG(TIMESTAMPDIFF(SECOND, created_at, resolved_at))
WHERE status = 'Resolved'
```

**Calculation:**
```javascript
resolutionTime = resolvedTimestamp - createdTimestamp
avgResolutionTime = resolvedTickets.reduce(
  (sum, ticket) => sum + ticket.resolutionTime, 0
) / resolvedTickets.length
```

**Example:**
```
Average Resolution Time: 8.5 minutes

Distribution:
  - Same turn (<1 min): 82 tickets (83.7%)
  - Multi-turn (1-15 min): 14 tickets (14.3%)
  - Extended (15+ min): 2 tickets (2.0%)

By Category:
  - Feature: 2.1 min (quick info)
  - General: 3.5 min (straightforward)
  - Bug: 18.7 min (troubleshooting)
  - Billing: 12.4 min (context gathering)
```

**Interpretation:**
```yaml
<5 min:     Excellent (very efficient)
5-15 min:   Very Good (acceptable)
15-30 min:  Good (complex issues)
30-60 min:  Fair (needs attention)
>60 min:    Poor (consider escalation)

For Human-Escalated Tickets:
<1 hour:    Excellent (URGENT SLA)
1-4 hours:  Very Good (HIGH SLA)
4-24 hours: Good (MEDIUM SLA)
24-48 hours: Fair (LOW SLA)
>48 hours:  Poor (SLA violation)
```

**Target:**
- AI-resolved: <10 minutes average
- URGENT escalations: <1 hour
- HIGH escalations: <4 hours
- MEDIUM: <24 hours

**Related Metrics:** M015, M009

---

### M017: Agent Response Time (Escalated)

**Definition:** Time from ticket escalation to human agent's first response.

**Formula:**
```sql
AVG(TIMESTAMPDIFF(SECOND, escalated_at, agent_first_response_at))
WHERE status = 'Escalated'
```

**Calculation:**
```javascript
agentResponseTime = agentFirstResponseTimestamp - escalatedTimestamp
```

**Example:**
```
Average Agent Response Time: 18.5 minutes

By Priority:
  - URGENT: 8.2 min (target: 15 min) ✅
  - HIGH: 32.1 min (target: 60 min) ✅
  - MEDIUM: 2.8 hours (target: 4 hours) ✅
  - LOW: 6.2 hours (target: 24 hours) ✅

By Agent:
  - Sarah: 12.3 min (fast)
  - Mike: 15.7 min (good)
  - Emma: 28.4 min (acceptable)
```

**Target:** Meet SLA by priority (see M025)

**Related Metrics:** M025 (SLA Compliance)

---

### M018: Queue Depth

**Definition:** Current number of tickets waiting for assignment or response.

**Formula:**
```sql
COUNT(ticket_id)
WHERE status IN ('Pending', 'Escalated')
  AND assigned_to IS NULL
```

**Calculation:**
```javascript
queueDepth = tickets.filter(
  t => (t.status === 'Pending' || t.status === 'Escalated') 
       && !t.assignedTo
).length
```

**Example:**
```
Current Queue Depth: 12 tickets

Age Distribution:
  - <15 min: 7 tickets (fresh)
  - 15-60 min: 3 tickets (aging)
  - 1-4 hours: 2 tickets (⚠️ attention needed)
  - >4 hours: 0 tickets

By Priority:
  - URGENT: 0 (good - should be 0)
  - HIGH: 2 (acceptable)
  - MEDIUM: 8 (normal)
  - LOW: 2 (low priority)
```

**Interpretation:**
```yaml
0-5:      Excellent (very manageable)
6-15:     Good (normal workload)
16-30:    Fair (busy period)
31-50:    Poor (understaffed)
>50:      Critical (emergency - need more agents)

Red Flags:
  - Any URGENT in queue >15 min
  - Any ticket >4 hours
  - Rapidly growing queue
  - Consistently high queue
```

**Target:**
- <10 tickets in queue
- 0 URGENT tickets unassigned
- No tickets >1 hour for HIGH priority

**Related Metrics:** M025 (SLA Compliance)

---

## Customer Experience Metrics

### M019: Customer Satisfaction Score (CSAT)

**Definition:** Average customer rating of support interaction quality.

**Formula:**
```sql
AVG(satisfaction_rating)
WHERE satisfaction_rating IS NOT NULL
  AND created >= [period_start]
  AND created <= [period_end]
```

**Scale:** 1-5 stars
- 5 stars: Excellent
- 4 stars: Good
- 3 stars: Acceptable
- 2 stars: Poor
- 1 star: Very Poor

**Calculation:**
```javascript
csat = tickets.filter(t => t.satisfactionRating)
  .reduce((sum, t) => sum + t.satisfactionRating, 0) 
  / ticketsWithRatings.length
```

**Example:**
```
Average CSAT: 4.3 / 5.0

Distribution:
  - 5 stars: 48 tickets (52%)
  - 4 stars: 32 tickets (35%)
  - 3 stars: 9 tickets (10%)
  - 2 stars: 2 tickets (2%)
  - 1 star: 1 ticket (1%)

Response Rate: 72% (92 of 127 rated)

By Category:
  - Feature: 4.7 (highest - good news delivery)
  - General: 4.4 (high - helpful answers)
  - Bug: 4.0 (lower - frustration with issues)
  - Billing: 3.8 (lowest - money concerns)
```

**Interpretation:**
```yaml
4.5-5.0:  Excellent (world-class)
4.0-4.5:  Very Good (above average)
3.5-4.0:  Good (meets expectations)
3.0-3.5:  Fair (needs improvement)
<3.0:     Poor (serious issues)

Industry Benchmarks:
  - Best-in-class: 4.5+
  - Above average: 4.0-4.5
  - Average: 3.5-4.0
  - Below average: <3.5
```

**Target:** ≥4.0 average, <5% ratings below 3 stars

**How to Collect:**
```yaml
Methods:
  - Post-resolution survey (Slack/Email)
  - Thumbs up/down in interface
  - Follow-up email after 24 hours
  - In-app rating widget

Question:
"How would you rate your support experience?"
⭐⭐⭐⭐⭐ (1-5 stars)
```

**Related Metrics:** M020 (NPS), M021 (Sentiment)

---

### M020: Net Promoter Score (NPS)

**Definition:** Likelihood of customers recommending the product/support to others.

**Formula:**
```
NPS = % Promoters - % Detractors
```

**Scale:** 0-10
- 9-10: Promoters (loyal enthusiasts)
- 7-8: Passives (satisfied but unenthusiastic)
- 0-6: Detractors (unhappy customers)

**Calculation:**
```javascript
const promoters = responses.filter(r => r >= 9).length
const passives = responses.filter(r => r >= 7 && r <= 8).length
const detractors = responses.filter(r => r <= 6).length
const total = responses.length

const promoterPercent = (promoters / total) * 100
const detractorPercent = (detractors / total) * 100

nps = promoterPercent - detractorPercent
```

**Example:**
```
NPS Survey Results (100 responses):
  - 10: 35 responses
  - 9: 28 responses → Promoters: 63 (63%)
  - 8: 18 responses
  - 7: 12 responses → Passives: 30 (30%)
  - 6: 4 responses
  - 5-0: 3 responses → Detractors: 7 (7%)

NPS = 63% - 7% = +56

Industry Comparison:
  - SaaS average: +30 to +40
  - Best-in-class: +50 to +70
  - World-class: +70+
```

**Interpretation:**
```yaml
+70 to +100:  Excellent (world-class)
+50 to +70:   Very Good (best-in-class)
+30 to +50:   Good (above average)
+10 to +30:   Fair (average)
0 to +10:     Poor (needs improvement)
Negative:     Critical (serious problems)

Context:
  - Support NPS often lower than product NPS
  - Faster response → Higher NPS
  - First contact resolution → Higher NPS
  - Empathy → Higher NPS
```

**Target:** ≥+50 (best-in-class support)

**Survey Question:**
```
"On a scale of 0-10, how likely are you to recommend 
our product/support to a friend or colleague?"

[0] [1] [2] [3] [4] [5] [6] [7] [8] [9] [10]
Not likely                          Very likely

Follow-up: "What's the primary reason for your score?"
```

**Related Metrics:** M019 (CSAT), M021 (Sentiment)

---

### M021: Sentiment Score

**Definition:** AI-detected emotional tone of customer messages.

**Categories:**
- POSITIVE: Happy, satisfied, grateful
- NEUTRAL: Factual, informational
- NEGATIVE: Frustrated, angry, disappointed

**Formula:**
```sql
(COUNT(POSITIVE) / COUNT(*)) * 100 -- Positive %
(COUNT(NEGATIVE) / COUNT(*)) * 100 -- Negative %
```

**Calculation:**
```javascript
const sentiments = tickets.map(t => t.sentiment)
const positive = sentiments.filter(s => s === 'POSITIVE').length
const neutral = sentiments.filter(s => s === 'NEUTRAL').length
const negative = sentiments.filter(s => s === 'NEGATIVE').length

positivePercent = (positive / sentiments.length) * 100
negativePercent = (negative / sentiments.length) * 100
```

**Example:**
```
Sentiment Distribution:
  - 😊 Positive: 45% (57 tickets)
  - 😐 Neutral: 42% (53 tickets)
  - 😞 Negative: 13% (17 tickets)

Sentiment Score: +32 (45% - 13%)

By Category:
  - Feature: 78% positive (good news)
  - General: 52% positive (helpful)
  - Bug: 18% positive (frustration)
  - Billing: 25% positive (money concerns)
```

**Interpretation:**
```yaml
Sentiment Score (Positive % - Negative %):
+40 to +100:  Excellent
+20 to +40:   Very Good
0 to +20:     Good
-10 to 0:     Fair
<-10:         Poor

Red Flags:
  - Increasing negative trend
  - >20% negative overall
  - Negative sentiment in resolved tickets
  - Negative for simple categories
```

**Target:** 
- <15% negative sentiment
- >40% positive sentiment
- Sentiment Score >+25

**How It's Detected:**
```yaml
AI analyzes message for:
  - Emotional keywords
  - Punctuation (!!!, ???)
  - Capitalization (URGENT!!!)
  - Tone (polite vs. demanding)
  - Context (complaint vs. question)

Examples:
  Positive: "Thanks so much! This helped a lot 😊"
  Neutral: "How do I export my data to CSV?"
  Negative: "This is the third time! Very frustrated!!!"
```

**Related Metrics:** M019 (CSAT), M020 (NPS)

---

## AI Performance Metrics

### M022: Categorization Accuracy

**Definition:** Percentage of tickets correctly categorized by AI (validated against human review).

**Formula:**
```
Accuracy = Correct Categorizations / Total Reviewed * 100
```

**Categories:**
- 🐛 BUG
- 💰 BILLING
- ✨ FEATURE
- 💬 GENERAL

**Calculation:**
```javascript
// Requires human validation sample
const reviewed = 100 // Sample size
const correct = 92   // Human agrees with AI

accuracy = (correct / reviewed) * 100 // 92%
```

**Example:**
```
Sample Size: 100 tickets (random sample)
Correct: 92
Incorrect: 8
Accuracy: 92%

By Category:
  - Feature: 98% (clear language)
  - General: 95% (straightforward)
  - Bug: 88% (sometimes ambiguous)
  - Billing: 87% (needs context)

Common Errors:
  - Bug vs General: 4 tickets
  - Billing vs Bug: 2 tickets
  - Feature vs General: 2 tickets
```

**Interpretation:**
```yaml
>95%:     Excellent (production-ready)
90-95%:   Very Good (acceptable)
85-90%:   Good (minor tuning needed)
80-85%:   Fair (needs improvement)
<80%:     Poor (significant issues)

Acceptable Error Rates:
  - Ambiguous categories: 5-10%
  - Edge cases: 2-5%
  - Multi-category: 3-7%
```

**Target:** ≥90% accuracy overall, ≥85% per category

**How to Measure:**
```yaml
Method 1: Weekly Sample
  - Random sample of 100 tickets
  - Human expert reviews
  - Marks correct/incorrect
  - Calculates accuracy

Method 2: Escalation Review
  - Review escalated tickets
  - Check if categorization correct
  - Aggregate results

Method 3: Customer Feedback
  - "Was this helpful?" button
  - Track mismatch patterns
```

**Improvement Strategies:**
- Add training examples for confused categories
- Improve keyword detection
- Better handling of ambiguous messages
- Multi-label classification for edge cases

**Related Metrics:** M008 (Confidence), M023 (Routing Accuracy)

---

### M023: Routing Accuracy

**Definition:** Percentage of tickets routed to the correct specialist agent.

**Formula:**
```
Routing Accuracy = Correct Routes / Total Routes * 100
```

**Agents:**
- 🐛 Bug Handler
- 💰 Billing Agent
- ✨ Feature Agent
- 💬 General Agent

**Calculation:**
```javascript
// Validated by human review or customer feedback
const correctRoutes = 95
const totalRoutes = 100
routingAccuracy = (correctRoutes / totalRoutes) * 100 // 95%
```

**Example:**
```
Routing Accuracy: 95%

By Agent:
  - Feature Agent: 98% (clearest)
  - General Agent: 96% (catch-all)
  - Bug Handler: 93% (technical)
  - Billing Agent: 92% (nuanced)

Errors:
  - Bug → General: 3%
  - Billing → Bug: 2%
```

**Target:** ≥90% routing accuracy

**Related Metrics:** M022 (Categorization)

---

### M024: Response Relevance Score

**Definition:** How relevant and helpful the AI response is to the customer query.

**Measurement:** Human evaluation on 1-5 scale
- 5: Perfect response, fully addresses query
- 4: Good response, mostly relevant
- 3: Acceptable, somewhat helpful
- 2: Poor, partially relevant
- 1: Irrelevant, unhelpful

**Example:**
```
Sample: 100 responses reviewed
Average Score: 4.2 / 5.0

Distribution:
  - 5: 55 (55%)
  - 4: 30 (30%)
  - 3: 10 (10%)
  - 2: 4 (4%)
  - 1: 1 (1%)

By Category:
  - General: 4.5 (straightforward answers)
  - Feature: 4.3 (roadmap info)
  - Bug: 4.0 (troubleshooting)
  - Billing: 3.9 (context needed)
```

**Target:** ≥4.0 average

**Related Metrics:** M019 (CSAT)

---

## Team Efficiency Metrics

### M025: SLA Compliance

**Definition:** Percentage of tickets resolved within Service Level Agreement timeframes.

**SLA Targets:**
```yaml
URGENT:   First response: 15 min,  Resolution: 1 hour
HIGH:     First response: 1 hour,  Resolution: 4 hours
MEDIUM:   First response: 4 hours, Resolution: 24 hours
LOW:      First response: 24 hours, Resolution: 48 hours
```

**Formula:**
```sql
(COUNT(tickets WHERE resolved_within_sla = true) / 
 COUNT(tickets)) * 100
```

**Calculation:**
```javascript
const withinSLA = tickets.filter(t => {
  const timeDiff = t.resolvedAt - t.createdAt
  const slaTarget = SLA_TARGETS[t.priority]
  return timeDiff <= slaTarget
}).length

slaCompliance = (withinSLA / tickets.length) * 100
```

**Example:**
```
Overall SLA Compliance: 94%

By Priority:
  - URGENT: 100% (8/8) ✅
  - HIGH: 95% (19/20) ✅
  - MEDIUM: 92% (55/60) ✅
  - LOW: 90% (35/39) ✅

SLA Violations: 8 tickets (6%)
  - MEDIUM: 5 tickets (avg overage: 4.2 hours)
  - LOW: 3 tickets (avg overage: 6.5 hours)

Root Causes:
  - Understaffed: 5 tickets
  - Complex issue: 2 tickets
  - Waiting on customer: 1 ticket
```

**Interpretation:**
```yaml
>95%:     Excellent (world-class)
90-95%:   Very Good (strong performance)
85-90%:   Good (acceptable)
80-85%:   Fair (needs attention)
<80%:     Poor (serious issues)

By Priority:
  - URGENT: Must be 100% (critical)
  - HIGH: Target 95%+ (important)
  - MEDIUM: Target 90%+ (standard)
  - LOW: Target 85%+ (acceptable)
```

**Target:** 
- ≥95% overall
- 100% for URGENT
- ≥95% for HIGH

**Related Metrics:** M015, M016, M017

---

### M026: Agent Utilization

**Definition:** Percentage of agent's working time spent actively handling tickets.

**Formula:**
```
Utilization = (Active Time / Available Time) * 100
```

**Calculation:**
```javascript
const activeTime = agent.tickets.reduce(
  (sum, t) => sum + t.resolutionTime, 0
)
const availableTime = 8 * 60 * 60 // 8-hour workday in seconds

utilization = (activeTime / availableTime) * 100
```

**Example:**
```
Agent: Sarah Chen
Shift: 8 hours (28,800 seconds)
Active Time: 6.4 hours (23,040 seconds)
Utilization: 80%

Breakdown:
  - Ticket handling: 5.2 hours (65%)
  - Research/investigation: 0.8 hours (10%)
  - Documentation: 0.4 hours (5%)
  - Idle/breaks: 1.6 hours (20%)

Team Average: 75%
```

**Interpretation:**
```yaml
80-90%:   Optimal (productive, not burned out)
70-80%:   Good (healthy balance)
60-70%:   Fair (underutilized or lots of breaks)
90-100%:  ⚠️ Risk of burnout
<60%:     Underutilized (training opportunity?)
```

**Target:** 75-85% utilization

**Note:** 100% is not the goal - agents need time for breaks, learning, documentation

**Related Metrics:** M027, M029

---

### M027: Tickets per Agent per Day

**Definition:** Average number of tickets handled by each agent daily.

**Formula:**
```sql
COUNT(ticket_id) / COUNT(DISTINCT assigned_to)
WHERE status IN ('In Progress', 'Resolved')
  AND assigned_to IS NOT NULL
  AND created = [date]
```

**Calculation:**
```javascript
ticketsPerAgent = totalAssignedTickets / activeAgents
```

**Example:**
```
Date: January 9, 2024
Total Assigned Tickets: 29 (escalations)
Active Agents: 3
Tickets per Agent: 9.7

Individual:
  - Sarah: 12 tickets (high)
  - Mike: 10 tickets (average)
  - Emma: 7 tickets (lower - training new agent)

AI-Handled (for comparison):
  - AI: 98 tickets/day (no capacity limit)
```

**Interpretation:**
```yaml
Industry Benchmarks:
  - Chat support: 20-30 tickets/day
  - Email support: 15-25 tickets/day
  - Complex technical: 8-15 tickets/day
  - Mixed: 12-20 tickets/day

With AI:
  - Human agents: 8-15 tickets/day (complex only)
  - AI: Unlimited (handles simple)
```

**Target:** 
- 10-15 tickets/agent/day (escalations only)
- Balanced distribution (±20%)

**Related Metrics:** M026, M029

---

### M028: Average Handle Time (AHT)

**Definition:** Average time an agent spends handling one ticket from assignment to resolution.

**Formula:**
```sql
AVG(TIMESTAMPDIFF(SECOND, assigned_at, resolved_at))
WHERE status = 'Resolved'
  AND assigned_to IS NOT NULL
```

**Calculation:**
```javascript
aht = resolvedTickets
  .filter(t => t.assignedTo)
  .reduce((sum, t) => sum + (t.resolvedAt - t.assignedAt), 0)
  / resolvedTickets.length
```

**Example:**
```
Average Handle Time: 28.5 minutes

By Priority:
  - URGENT: 18.2 min (fast resolution)
  - HIGH: 32.4 min (moderate)
  - MEDIUM: 45.8 min (detailed work)
  - LOW: 38.6 min (varies)

By Agent:
  - Sarah: 24.3 min (efficient)
  - Mike: 29.7 min (thorough)
  - Emma: 35.2 min (new agent - learning)
```

**Interpretation:**
```yaml
15-30 min:  Excellent (efficient)
30-45 min:  Very Good (thorough)
45-60 min:  Good (complex issues)
60-90 min:  Fair (very complex or inefficient)
>90 min:    Poor (needs escalation or training)

Context Matters:
  - Simple tickets: 10-20 min
  - Moderate: 20-40 min
  - Complex: 40-90 min
  - Escalated tickets typically longer
```

**Target:** 
- <30 minutes average
- <20 minutes for URGENT

**Related Metrics:** M016, M026

---

### M029: Workload Distribution

**Definition:** How evenly tickets are distributed across agents.

**Measurement:** Standard deviation of tickets per agent

**Formula:**
```javascript
const mean = ticketsPerAgent.reduce((a,b) => a+b) / agents.length
const variance = ticketsPerAgent.reduce(
  (sum, count) => sum + Math.pow(count - mean, 2), 0
) / agents.length
const stdDev = Math.sqrt(variance)

// Coefficient of variation (normalized)
const cv = (stdDev / mean) * 100
```

**Example:**
```
Tickets per Agent:
  - Sarah: 12
  - Mike: 10
  - Emma: 7
  
Mean: 9.67
Standard Deviation: 2.08
Coefficient of Variation: 21.5%

Interpretation: Moderately balanced
(Emma is new, so lower load is expected)
```

**Interpretation:**
```yaml
CV <15%:    Excellent (very balanced)
CV 15-25%:  Good (acceptable variance)
CV 25-40%:  Fair (some imbalance)
CV >40%:    Poor (significant imbalance)

Acceptable Reasons for Imbalance:
  - New agent (training period)
  - Different specializations
  - Part-time schedules
  - Handling complex long-running tickets
```

**Target:** CV <25% (excluding new agents)

**Related Metrics:** M027, M028

---

## Category & Channel Metrics

### M030: Category Distribution

**Definition:** Percentage breakdown of tickets by category.

**Categories:**
- 🐛 BUG
- 💰 BILLING  
- ✨ FEATURE
- 💬 GENERAL

**Calculation:**
```javascript
const total = tickets.length
categoryDistribution = {
  BUG: tickets.filter(t => t.category === 'BUG').length / total * 100,
  BILLING: tickets.filter(t => t.category === 'BILLING').length / total * 100,
  FEATURE: tickets.filter(t => t.category === 'FEATURE').length / total * 100,
  GENERAL: tickets.filter(t => t.category === 'GENERAL').length / total * 100
}
```

**Example:**
```
Total Tickets: 127

Category Distribution:
  - 🐛 Bug: 45 tickets (35%)
  - 💰 Billing: 32 tickets (25%)
  - ✨ Feature: 28 tickets (22%)
  - 💬 General: 22 tickets (18%)

Trend (vs last week):
  - Bug: +5% (product update caused issues)
  - Billing: -2% (improved documentation)
  - Feature: +3% (roadmap questions increasing)
  - General: -6% (better onboarding)
```

**Interpretation:**
```yaml
Normal Distribution:
  - General: 30-50% (most common)
  - Bug: 20-35% (depends on product maturity)
  - Feature: 15-25% (customer engagement)
  - Billing: 10-20% (business queries)

Red Flags:
  - Bugs >50%: Product quality issues
  - Billing >30%: Pricing confusion
  - Feature >35%: Communication gap
  - General >60%: Poor documentation
```

**Use Cases:**
- Product prioritization
- Documentation gaps
- Feature planning
- Training focus

**Related Metrics:** M031

---

### M031: Channel Distribution

**Definition:** Percentage breakdown of tickets by communication channel.

**Channels:**
- 💬 Slack
- 📧 Email

**Calculation:**
```javascript
channelDistribution = {
  Slack: tickets.filter(t => t.channel === 'Slack').length / total * 100,
  Email: tickets.filter(t => t.channel === 'Email').length / total * 100
}
```

**Example:**
```
Total Tickets: 127

Channel Distribution:
  - 💬 Slack: 89 tickets (70%)
  - 📧 Email: 38 tickets (30%)

Performance by Channel:
  Slack:
    - Avg Response: 2.1s
    - Resolution Rate: 79%
    - CSAT: 4.4/5
  
  Email:
    - Avg Response: 3.5s (polling delay)
    - Resolution Rate: 74%
    - CSAT: 4.2/5
```

**Interpretation:**
```yaml
Typical Distribution:
  - Internal tools (Slack): 60-80%
  - External (Email): 20-40%

Channel Preferences:
  - Slack: Faster, real-time, casual
  - Email: Detailed, async, formal
  - Impacts response expectations
```

**Use Cases:**
- Resource allocation
- Response time optimization
- Channel preference analysis

**Related Metrics:** M030

---

## Advanced Metrics

### M032: Repeat Contact Rate

**Definition:** Percentage of customers who contact support multiple times about the same issue.

**Formula:**
```sql
(COUNT(DISTINCT customers with >1 ticket on same issue) /
 COUNT(DISTINCT customers)) * 100
```

**Calculation:**
```javascript
const repeatCustomers = customers.filter(c => 
  c.tickets.filter(t => t.similarIssue).length > 1
).length

repeatRate = (repeatCustomers / totalCustomers) * 100
```

**Example:**
```
Total Customers: 98
Repeat Contacts: 8
Repeat Rate: 8.2%

Common Reasons:
  - Issue not fully resolved: 4 (50%)
  - Follow-up question: 2 (25%)
  - Related issue: 2 (25%)
```

**Interpretation:**
```yaml
<5%:      Excellent (high first contact resolution)
5-10%:    Very Good (acceptable)
10-15%:   Good (some repeat issues)
15-25%:   Fair (needs improvement)
>25%:     Poor (serious resolution issues)
```

**Target:** <10%

**Related Metrics:** M009 (FCR)

---

### M033: Time to Competency (New Agent)

**Definition:** Time for new human agent to reach target performance levels.

**Metrics Tracked:**
- Tickets per day → Target: 80% of team average
- Average Handle Time → Target: 120% of team average
- CSAT → Target: 3.8+

**Example:**
```
New Agent: Emma Wilson
Start Date: Dec 1, 2024

Week 1: Tickets: 3/day, AHT: 65 min, CSAT: 3.5
Week 2: Tickets: 5/day, AHT: 52 min, CSAT: 3.8
Week 3: Tickets: 7/day, AHT: 42 min, CSAT: 4.1
Week 4: Tickets: 9/day, AHT: 35 min, CSAT: 4.3 ✅

Time to Competency: 4 weeks

With AI Support:
  - Handles simple tickets automatically
  - Emma focuses on complex issues
  - Learns from AI responses
  - Faster ramp-up
```

**Target:** <6 weeks to competency

---

### M034: Knowledge Base Hit Rate

**Definition:** Percentage of AI responses that successfully reference knowledge base articles.

**Formula:**
```
(Responses with KB references / Total Responses) * 100
```

**Example:**
```
Total Responses: 127
KB References: 89
Hit Rate: 70.1%

Most Referenced:
  1. "How to export data" - 23 times
  2. "Password reset guide" - 18 times
  3. "Billing cycles explained" - 15 times

Gap Analysis:
  - Missing: "API rate limits" (requested 8 times)
  - Outdated: "Old pricing model" (needs update)
```

**Target:** >60% hit rate

**Use Cases:**
- Identify documentation gaps
- Prioritize KB article creation
- Improve AI knowledge

---

## Calculated Fields

### Formula Reference

**Age (Hours):**
```excel
=ROUND(DATETIME_DIFF(NOW(), {Created}, 'hours'), 1)
```

**SLA Status:**
```excel
=IF(
  {Status} = "Resolved", 
  "✅ Met",
  IF(
    AND({Priority} = "URGENT", {Age (Hours)} > 1),
    "🔴 Overdue - CRITICAL",
    IF(
      AND({Priority} = "HIGH", {Age (Hours)} > 4),
      "🔴 Overdue",
      IF(
        AND({Priority} = "MEDIUM", {Age (Hours)} > 24),
        "⚠️ At Risk",
        "🟢 On Track"
      )
    )
  )
)
```

**Resolution Time:**
```excel
=IF(
  {Status} = "Resolved",
  ROUND(DATETIME_DIFF({Last Modified}, {Created}, 'hours'), 1),
  BLANK()
)
```

**Performance Grade:**
```excel
=IF(
  {Resolution Rate} >= 0.85,
  "🌟 A",
  IF(
    {Resolution Rate} >= 0.75,
    "✅ B",
    IF(
      {Resolution Rate} >= 0.65,
      "⚠️ C",
      "❌ D"
    )
  )
)
```

**Cost Savings:**
```excel
=({Resolved Tickets} * 8.33) - ({Total Tickets} * 0.03)
```

---

## Benchmarks & Targets

### Performance Standards

```yaml
Core Metrics:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Resolution Rate:          Target ≥75%,  Excellent ≥85%
Escalation Rate:          Target ≤15%,  Excellent ≤10%
AI Confidence:            Target ≥0.85, Excellent ≥0.90
Response Time:            Target <5s,   Excellent <3s
Customer Satisfaction:    Target ≥4.0,  Excellent ≥4.5
NPS:                      Target ≥+50,  Excellent ≥+70

Financial:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cost per AI Ticket:       Target ≤$0.05
Cost Savings per Ticket:  Target ≥$8.00
ROI (First Year):         Target ≥500%
Payback Period:           Target <2 months

Quality:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Categorization Accuracy:  Target ≥90%,  Excellent ≥95%
First Contact Resolution: Target ≥80%,  Excellent ≥90%
SLA Compliance:           Target ≥95%,  Excellent ≥98%
Repeat Contact Rate:      Target <10%,  Excellent <5%

Team Efficiency:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Agent Utilization:        Target 75-85%
Average Handle Time:      Target <30 min
Tickets per Agent/Day:    Target 10-15 (escalations only)
Workload Distribution:    Target CV <25%
```

### Industry Comparisons

```yaml
Traditional Support (Human Only):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Resolution Rate:          60-70%
Response Time:            2-5 minutes (chat), 2-24 hours (email)
Cost per Ticket:          $8-15
CSAT:                     3.5-4.0
Agent Capacity:           20-30 tickets/day

AI-Assisted Support:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Resolution Rate:          75-85%
Response Time:            <5 seconds
Cost per Ticket:          $0.03-0.10
CSAT:                     4.0-4.5
AI Capacity:              Unlimited
Human Focus:              Complex tickets only
```

### Goal Setting Framework

```yaml
Short-term Goals (30 days):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
- Achieve 75% resolution rate
- Response time <5s (p95)
- CSAT >4.0
- SLA compliance >90%

Medium-term Goals (90 days):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
- Achieve 80% resolution rate
- Response time <3s (p95)
- CSAT >4.2
- SLA compliance >95%
- ROI >500%

Long-term Goals (12 months):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
- Achieve 85% resolution rate
- Categorization accuracy >95%
- CSAT >4.5
- NPS >+60
- Cost savings >$250K/year
- Team efficiency +300%
```

---

## Version

**Version:** 1.0.0  
**Last Updated:** 2024-01-09  
**Total Metrics Defined:** 34  
**Categories:** 9

---

**End of Metrics Definitions** 📊

Use this reference guide to ensure consistent metric interpretation across all dashboards, reports, and analytics. Update as new metrics are added or definitions are refined.
