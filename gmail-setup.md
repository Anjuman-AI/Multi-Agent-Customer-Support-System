# Gmail Setup Guide

Complete guide to setting up Gmail integration for email-based customer support in the AI Customer Support System.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Step 1: Enable Gmail API](#step-1-enable-gmail-api)
4. [Step 2: Create OAuth Credentials](#step-2-create-oauth-credentials)
5. [Step 3: Configure OAuth Consent Screen](#step-3-configure-oauth-consent-screen)
6. [Step 4: Generate Refresh Token](#step-4-generate-refresh-token)
7. [Step 5: Configure Email Filters](#step-5-configure-email-filters)
8. [Step 6: Configure n8n](#step-6-configure-n8n)
9. [Step 7: Test Integration](#step-7-test-integration)
10. [Email Templates](#email-templates)
11. [Troubleshooting](#troubleshooting)
12. [Advanced Configuration](#advanced-configuration)

---

## Overview

**What you're building:** An automated email support system that monitors a Gmail inbox, processes customer inquiries with AI, sends intelligent responses, and escalates complex issues to human agents.

**Time required:** 20-30 minutes

**Gmail account required:** Yes (Google Workspace or personal Gmail)

**Key capabilities:**
- Monitor support@yourdomain.com inbox
- Auto-reply to customer emails
- Track conversations with threading
- Create tickets in Airtable
- Escalate to human agents
- Send formatted HTML responses

---

## Prerequisites

### Required Access
- [ ] Google account (Workspace or personal Gmail)
- [ ] Admin access to Google Cloud Console (or ability to create projects)
- [ ] Email account for support (e.g., support@yourdomain.com)
- [ ] n8n instance running
- [ ] Airtable base configured

### Before You Start
- [ ] Read through entire guide
- [ ] Have .env file ready to update
- [ ] Backup Gmail settings (if modifying existing account)
- [ ] Understand Gmail API quotas and limits

### Gmail API Quotas
```
Free Tier (per day):
- Read quota: 1,000,000,000 quota units
- Send quota: Depends on account type
  - Personal Gmail: 500 emails/day
  - Google Workspace: 2,000 emails/day
- Rate limit: 250 quota units/user/second

Each operation costs:
- Read email: 5 units
- Send email: 100 units
- List messages: 5 units
```

---

## Step 1: Enable Gmail API

### 1.1 Access Google Cloud Console

```
1. Open browser
2. Go to: https://console.cloud.google.com/
3. Sign in with your Google account
```

### 1.2 Create New Project (or Select Existing)

**Create New Project:**
```
1. Click project dropdown (top left, next to "Google Cloud")
2. Click "New Project"
3. Project name: AI Support System
4. Organization: [Select if available]
5. Location: [Select if applicable]
6. Click "Create"
7. Wait for project creation (10-30 seconds)
8. Select your new project from dropdown
```

**Or Select Existing:**
```
1. Click project dropdown
2. Select existing project
3. Proceed to next step
```

### 1.3 Enable Gmail API

```
1. In Google Cloud Console
2. Click "☰" menu → "APIs & Services" → "Library"
3. Search: "Gmail API"
4. Click "Gmail API" in results
5. Click "Enable"
6. Wait for API to enable (5-10 seconds)

✅ Success: You'll see "API enabled" message
```

---

## Step 2: Create OAuth Credentials

### 2.1 Navigate to Credentials

```
1. Click "☰" menu → "APIs & Services" → "Credentials"
2. Click "+ CREATE CREDENTIALS" (top of page)
3. Select "OAuth client ID"
```

### 2.2 Configure OAuth Consent Screen (First Time Only)

If prompted "To create an OAuth client ID, you must first configure your consent screen":

```
1. Click "Configure Consent Screen"
2. Choose user type:
   - Internal: For Google Workspace organizations only
   - External: For personal Gmail or public access
   
   Recommendation: External (more flexible)
   
3. Click "Create"
```

**See [Step 3: Configure OAuth Consent Screen](#step-3-configure-oauth-consent-screen) for detailed consent screen setup**

### 2.3 Create OAuth Client ID

After consent screen is configured:

```
1. Back to "Credentials" page
2. Click "+ CREATE CREDENTIALS" → "OAuth client ID"
3. Application type: "Web application"
4. Name: "AI Support Email Integration"
```

### 2.4 Add Authorized Redirect URIs

```
Add these redirect URIs:

For n8n:
https://your-n8n-domain.com/rest/oauth2-credential/callback
http://localhost:5678/rest/oauth2-credential/callback (development)

For Google OAuth Playground (for getting refresh token):
https://developers.google.com/oauthplayground

Click "Add URI" for each one
```

**Example:**
```
Authorized redirect URIs:
1. https://n8n.yourdomain.com/rest/oauth2-credential/callback
2. http://localhost:5678/rest/oauth2-credential/callback
3. https://developers.google.com/oauthplayground
```

### 2.5 Save and Get Credentials

```
1. Click "Create"
2. Modal appears with credentials

Copy and save:
- Client ID: 
  123456789012-abcdefghijklmnop.apps.googleusercontent.com
  
- Client secret: 
  GOCSPX-AbCdEfGhIjKlMnOpQrStUvWxYz

Save to .env:
GMAIL_CLIENT_ID=[your-client-id]
GMAIL_CLIENT_SECRET=[your-client-secret]

3. Click "OK"
```

**🔐 Security:** Keep these credentials secure. Never commit to version control.

---

## Step 3: Configure OAuth Consent Screen

### 3.1 App Information

```
Fill in required fields:

App name: AI Support System
User support email: [Your email]
App logo: [Optional - 120x120px PNG]

Application home page: https://yourdomain.com
Application privacy policy: https://yourdomain.com/privacy
Application terms of service: https://yourdomain.com/terms

Developer contact information:
Email: [Your email or support email]
```

### 3.2 Scopes

```
1. Click "Add or Remove Scopes"
2. Add these Gmail scopes:

Required Scopes:
✅ .../auth/gmail.readonly
   - Read all resources and metadata (no email body content)
   
✅ .../auth/gmail.modify  
   - Read, compose, send, and modify emails
   
✅ .../auth/gmail.send
   - Send emails on your behalf

Optional (Recommended):
○ .../auth/gmail.labels
   - Manage mailbox labels
   
○ .../auth/gmail.metadata
   - View email metadata

3. Click "Update"
4. Click "Save and Continue"
```

### 3.3 Test Users (For External Apps in Testing)

If app is "External" and not published:

```
1. Click "Add Users"
2. Add test user emails:
   - Your email
   - support@yourdomain.com
   - Team member emails
   
3. Click "Save and Continue"

⚠️ Note: Only test users can authenticate while app is in testing mode
```

### 3.4 Review and Submit

```
1. Review all settings
2. Click "Back to Dashboard"

App Status:
- Testing: Up to 100 test users
- In Production: Unlimited users (requires verification)

For internal use: Testing mode is sufficient
For public use: Submit for verification
```

---

## Step 4: Generate Refresh Token

You need a refresh token to access Gmail programmatically. Two methods:

### Method 1: Using Google OAuth Playground (Recommended)

#### 4.1 Open OAuth Playground

```
Go to: https://developers.google.com/oauthplayground
```

#### 4.2 Configure Playground

```
1. Click gear icon (⚙️) in top right
2. Check "Use your own OAuth credentials"
3. Enter:
   OAuth Client ID: [Your Client ID from Step 2]
   OAuth Client secret: [Your Client Secret from Step 2]
4. Close settings
```

#### 4.3 Select Gmail Scopes

```
1. Left panel: Select & authorize APIs
2. Expand "Gmail API v1"
3. Select these scopes:
   ☑ https://www.googleapis.com/auth/gmail.readonly
   ☑ https://www.googleapis.com/auth/gmail.modify
   ☑ https://www.googleapis.com/auth/gmail.send
   
4. Click "Authorize APIs"
```

#### 4.4 Authorize Access

```
1. Google sign-in page appears
2. Sign in with the Gmail account you want to use for support
3. Review permissions
4. Click "Allow"
5. You'll be redirected back to OAuth Playground
```

#### 4.5 Get Refresh Token

```
1. Left panel: "Step 2 Exchange authorization code for tokens"
2. Click "Exchange authorization code for tokens"
3. Right panel displays:
   - Access token
   - Refresh token ← This is what you need!
   
4. Copy the refresh token
5. Save to .env:
   GMAIL_REFRESH_TOKEN=[your-refresh-token]
```

**Example refresh token format:**
```
1//0gXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

---

### Method 2: Using n8n OAuth2 Flow

#### 4.1 Create Gmail OAuth2 Credential in n8n

```
1. Open n8n: http://localhost:5678
2. Settings → Credentials
3. Click "Add Credential"
4. Search "Gmail OAuth2 API"
5. Select "Gmail OAuth2 API"
```

#### 4.2 Fill in Credentials

```
Name: Gmail Support Account
Client ID: [From Step 2]
Client Secret: [From Step 2]

Scopes (comma-separated):
https://www.googleapis.com/auth/gmail.readonly,https://www.googleapis.com/auth/gmail.modify,https://www.googleapis.com/auth/gmail.send
```

#### 4.3 Authenticate

```
1. Click "Connect my account"
2. Google OAuth window opens
3. Sign in with support Gmail account
4. Review and allow permissions
5. You'll be redirected back to n8n
6. Credential is now connected!
```

#### 4.4 Get Refresh Token (Optional)

n8n stores the refresh token internally. To extract it:

```
1. In n8n, open workflow
2. Add Gmail node
3. Select credential
4. Test connection
5. Check n8n logs for token (for backup purposes)
```

---

## Step 5: Configure Email Filters

### 5.1 Create Support Email Alias

**For Google Workspace:**
```
1. Admin Console → Apps → Google Workspace → Gmail
2. Groups → Create Group
   Name: Support
   Email: support@yourdomain.com
   Members: [Your Gmail account]
3. Save
```

**For Personal Gmail:**
```
Use email forwarding or filters:
1. Gmail → Settings → Forwarding and POP/IMAP
2. Add forwarding address
3. Confirm forwarding
```

### 5.2 Create Gmail Labels

```
1. Open Gmail
2. Left sidebar → More → Create new label
3. Create these labels:
   - AI-Support-Inbox (for incoming tickets)
   - AI-Support-Processed (for handled emails)
   - AI-Support-Escalated (for human review)
   - AI-Support-Archived (for resolved tickets)
```

### 5.3 Create Filter for Incoming Support Emails

```
1. Gmail → Settings → Filters and Blocked Addresses
2. Create new filter:
   To: support@yourdomain.com
   Has the words: -label:AI-Support-Processed
   
3. Choose action:
   ☑ Apply label: AI-Support-Inbox
   ☑ Never send to Spam
   
4. Click "Create filter"
```

### 5.4 Auto-Archive Processed Emails

```
Create filter:
   Has the words: label:AI-Support-Processed
   
Action:
   ☑ Skip Inbox (Archive)
   ☑ Apply label: AI-Support-Archived
```

---

## Step 6: Configure n8n

### 6.1 Update Environment Variables

Complete .env with all Gmail settings:

```bash
# Gmail Configuration
GMAIL_CLIENT_ID=123456789012-abcd.apps.googleusercontent.com
GMAIL_CLIENT_SECRET=GOCSPX-AbCdEfGhIjKlMnOp
GMAIL_REFRESH_TOKEN=1//0gXXXXXXXXXXXXXXXXXXX

# Email addresses
GMAIL_SUPPORT_EMAIL=support@yourdomain.com
GMAIL_ANALYTICS_EMAIL=analytics@yourdomain.com
GMAIL_ESCALATIONS_EMAIL=escalations@yourdomain.com
```

### 6.2 Import Email Support Workflow

```
1. In n8n → Workflows
2. Click "Import from File"
3. Select: 02-email-support.json
4. Click "Import"
5. Workflow appears in list
```

### 6.3 Configure Gmail Trigger Node

```
1. Open imported workflow
2. Click "Gmail Trigger" node
3. Credential: Select/Create Gmail OAuth2 credential
4. Event: "Message Received"
5. Simple: Toggle OFF (use advanced)
6. Filters:
   Label IDs: [Select "AI-Support-Inbox"]
   Include Spam and Trash: No
7. Polling Interval: 60 seconds (1 minute)
8. Save
```

### 6.4 Configure Gmail Send Nodes

For each "Gmail" send node in workflow:

```
1. Click node
2. Credential: Select Gmail OAuth2
3. Resource: "Message"
4. Operation: "Send"
5. Options:
   To: {{$json.from}}  (reply to sender)
   Subject: Re: {{$json.subject}}
   Message Type: HTML
   Body: {{$json.aiResponse}}
6. Save
```

### 6.5 Configure Gmail Label Nodes

```
1. Click "Add Label" node
2. Credential: Gmail OAuth2
3. Resource: "Message"
4. Operation: "Add Label"
5. Message ID: {{$json.id}}
6. Label IDs: [Select "AI-Support-Processed"]
7. Save
```

### 6.6 Link to Airtable

```
1. Click "Airtable" node
2. Credential: Select Airtable credential
3. Base: [Your base]
4. Table: Support Tickets
5. Fields to Send:
   - Ticket ID: Generate unique ID
   - Customer Email: {{$json.from}}
   - Subject: {{$json.subject}}
   - Message: {{$json.textPlain}}
   - Channel: Email
   - Created: {{$json.internalDate}}
   - Thread ID: {{$json.threadId}}
   - Message ID: {{$json.id}}
6. Save
```

### 6.7 Activate Workflow

```
1. Verify all nodes are configured (no red errors)
2. Toggle "Active" switch at top
3. Workflow starts polling Gmail every 60 seconds
```

---

## Step 7: Test Integration

### 7.1 Send Test Email

```
From different email account:
To: support@yourdomain.com
Subject: Test - AI Support System
Body: "Hi, I'm testing the AI support system. Can you help me export my data?"

Click Send
```

### 7.2 Monitor n8n Execution

```
1. In n8n → Executions
2. Wait 60 seconds (polling interval)
3. Should see new execution appear
4. Click execution to view:
   - Email received
   - AI processing
   - Response generated
   - Label applied
   - Airtable record created
```

### 7.3 Check Gmail

```
1. Open Gmail support inbox
2. Verify:
   ✅ Original email has "AI-Support-Processed" label
   ✅ Email moved to archive
   
3. Check Sent folder:
   ✅ Reply sent to customer
   ✅ Threaded with original email
   ✅ Proper formatting
```

### 7.4 Check Airtable

```
1. Open Airtable base
2. Support Tickets table
3. Verify new record:
   ✅ Ticket ID generated
   ✅ Customer email captured
   ✅ Subject line saved
   ✅ Message content stored
   ✅ Channel = Email
   ✅ Thread ID for tracking
```

### 7.5 Test Follow-up Email

```
Reply to original test email:
"Thanks! How long does export take?"

Expected:
✅ n8n detects reply in same thread
✅ AI understands context
✅ Response references previous conversation
✅ Airtable record updated or new record created
```

### 7.6 Test Escalation

```
Send email:
To: support@yourdomain.com
Subject: URGENT - Charged $500 incorrectly
Body: "I was charged $500 instead of $50 on my credit card. I need an immediate refund or I'm canceling my account!"

Expected:
✅ AI detects billing + negative sentiment
✅ Escalates to human
✅ Email sent to escalations@yourdomain.com
✅ Customer receives acknowledgment
✅ Ticket marked as "Escalated" in Airtable
```

---

## Email Templates

### Auto-Reply Template

```html
<!DOCTYPE html>
<html>
<head>
    <style>
        body { font-family: Arial, sans-serif; line-height: 1.6; color: #333; }
        .container { max-width: 600px; margin: 0 auto; padding: 20px; }
        .header { background: #1a73e8; color: white; padding: 20px; text-align: center; }
        .content { padding: 20px; background: #f9f9f9; }
        .footer { padding: 20px; text-align: center; font-size: 12px; color: #666; }
        .button { background: #1a73e8; color: white; padding: 12px 24px; text-decoration: none; border-radius: 4px; display: inline-block; margin: 10px 0; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h2>AI Support Response</h2>
        </div>
        <div class="content">
            <p>Hi {{customerName}},</p>
            
            <p>{{aiResponse}}</p>
            
            <p>If this resolves your issue, no further action is needed. If you need additional help, just reply to this email.</p>
            
            <a href="{{ticketUrl}}" class="button">View Ticket</a>
        </div>
        <div class="footer">
            <p>Ticket ID: {{ticketId}}<br>
            Response Time: {{responseTime}}<br>
            Confidence: {{confidenceScore}}</p>
            
            <p>This is an automated response powered by AI. A human agent will follow up if needed.</p>
        </div>
    </div>
</body>
</html>
```

### Escalation Notification Template

```html
<!DOCTYPE html>
<html>
<head>
    <style>
        body { font-family: Arial, sans-serif; color: #333; }
        .container { max-width: 600px; margin: 0 auto; }
        .alert { background: #ea4335; color: white; padding: 15px; border-radius: 4px; margin: 20px 0; }
        .details { background: #f9f9f9; padding: 15px; margin: 10px 0; border-left: 4px solid #ea4335; }
        .button { background: #ea4335; color: white; padding: 12px 24px; text-decoration: none; border-radius: 4px; display: inline-block; }
    </style>
</head>
<body>
    <div class="container">
        <div class="alert">
            <h3>🚨 Escalated Ticket - Immediate Attention Required</h3>
        </div>
        
        <div class="details">
            <p><strong>Ticket ID:</strong> {{ticketId}}</p>
            <p><strong>Customer:</strong> {{customerName}} ({{customerEmail}})</p>
            <p><strong>Priority:</strong> {{priority}}</p>
            <p><strong>Category:</strong> {{category}}</p>
            <p><strong>Sentiment:</strong> {{sentiment}}</p>
            <p><strong>Reason for Escalation:</strong> {{escalationReason}}</p>
        </div>
        
        <div class="details">
            <h4>Customer Message:</h4>
            <p>{{customerMessage}}</p>
        </div>
        
        <div class="details">
            <h4>AI Assessment:</h4>
            <p>{{aiAssessment}}</p>
            <p><strong>Confidence:</strong> {{confidence}}</p>
        </div>
        
        <p style="margin: 20px 0;">
            <a href="{{ticketUrl}}" class="button">Handle This Ticket</a>
        </p>
        
        <p style="color: #666; font-size: 12px;">
            Expected response time: {{slaTarget}}<br>
            Time since received: {{ticketAge}}
        </p>
    </div>
</body>
</html>
```

### Customer Acknowledgment (Escalation)

```html
<!DOCTYPE html>
<html>
<body style="font-family: Arial, sans-serif; line-height: 1.6; color: #333;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <h3>Thank You for Contacting Support</h3>
        
        <p>Hi {{customerName}},</p>
        
        <p>We've received your message and understand this requires immediate attention from our specialist team.</p>
        
        <div style="background: #fff3cd; padding: 15px; border-radius: 4px; margin: 20px 0;">
            <p><strong>Your ticket has been escalated to a human agent.</strong></p>
            <p>Ticket ID: <strong>{{ticketId}}</strong></p>
            <p>Expected response: <strong>{{expectedResponse}}</strong></p>
        </div>
        
        <p>One of our specialists will personally review your case and respond within {{responseTime}}.</p>
        
        <p>In the meantime, if you have any additional information to add, simply reply to this email.</p>
        
        <p>Best regards,<br>
        {{companyName}} Support Team</p>
        
        <hr style="margin: 30px 0; border: none; border-top: 1px solid #ddd;">
        
        <p style="font-size: 12px; color: #666;">
            Ticket ID: {{ticketId}}<br>
            Priority: {{priority}}<br>
            Created: {{timestamp}}
        </p>
    </div>
</body>
</html>
```

---

## Troubleshooting

### Issue: Gmail API Not Enabled

**Symptoms:** 
- "Gmail API has not been used in project..." error
- 403 errors in n8n

**Solutions:**
```
1. Google Cloud Console
2. APIs & Services → Library
3. Search "Gmail API"
4. Click "Enable"
5. Wait 5 minutes for propagation
6. Retry in n8n
```

---

### Issue: Invalid Grant / Refresh Token Expired

**Symptoms:**
- "invalid_grant" error
- "Token has been expired or revoked"

**Solutions:**
```
1. Regenerate refresh token:
   - OAuth Playground method (Step 4)
   - Get new refresh token
   - Update .env
   - Restart n8n

2. Check OAuth consent screen:
   - Ensure app not in suspended state
   - Verify test users added (if external + testing)
   - Re-authenticate if needed

3. Revoke and re-authorize:
   - Google Account → Security → Third-party apps
   - Remove "AI Support System"
   - Re-authenticate through OAuth flow
```

---

### Issue: Insufficient Permissions

**Symptoms:**
- 403 "Insufficient Permission" errors
- Can read but not send emails

**Solutions:**
```
1. Verify Scopes:
   Required scopes in OAuth consent screen:
   ✓ gmail.readonly
   ✓ gmail.modify
   ✓ gmail.send

2. Re-authenticate:
   - After adding scopes, must re-authenticate
   - Get new tokens with updated scopes

3. Check n8n credential:
   - Scopes field should include all three
   - Comma-separated, no spaces
```

---

### Issue: Emails Not Being Detected

**Symptoms:**
- n8n workflow not triggering
- Manual test works but polling doesn't

**Solutions:**
```
1. Check Polling Interval:
   - 60 seconds minimum recommended
   - Shorter intervals may hit rate limits

2. Verify Label Filter:
   - Gmail Trigger → Label IDs
   - Must match exact label ID (not name)
   - Get label ID from Gmail settings

3. Check Email Label:
   - Incoming emails must have correct label
   - Verify filter rules in Gmail
   - Manually test by applying label

4. Test Filter:
   gmail.users.messages.list({
     userId: 'me',
     labelIds: ['Label_XX'],
     maxResults: 1
   })
```

---

### Issue: Duplicate Responses

**Symptoms:**
- Customer receives multiple AI responses
- Same email processed twice

**Solutions:**
```
1. Add Message ID Tracking:
   - Store processed message IDs in Airtable
   - Check before processing
   - Skip if already processed

2. Improve Label Logic:
   - Apply "Processed" label immediately
   - Filter excludes processed emails
   - Use atomic operations

3. Add Rate Limiting:
   - Limit workflow executions
   - 1 execution per message ID
   - Debounce duplicate triggers

4. Check Workflow Logic:
   - Ensure "Add Label" node runs
   - Verify label applied successfully
   - Check for error handling gaps
```

---

### Issue: HTML Emails Not Rendering

**Symptoms:**
- Emails sent as plain text
- HTML tags visible in email

**Solutions:**
```
1. Gmail Node Configuration:
   - Message Type: HTML (not Text)
   - Body field: HTML content
   - Content-Type header: text/html

2. Test HTML:
   - Send to yourself first
   - Verify rendering in multiple clients
   - Check for broken tags

3. Alternative Approach:
   - Use rich text editor
   - Copy HTML source
   - Paste into workflow
```

---

### Issue: Thread Handling Broken

**Symptoms:**
- Replies create new threads
- Can't find original conversation

**Solutions:**
```
1. Use Thread ID:
   - Store threadId from original email
   - Include in reply:
     {
       threadId: {{$json.threadId}},
       subject: "Re: {{$json.subject}}"
     }

2. Reference Headers:
   - In-Reply-To: {{$json.messageId}}
   - References: {{$json.references}}

3. Subject Line:
   - Must start with "Re: "
   - Keep original subject intact
```

---

### Issue: Rate Limit Exceeded

**Symptoms:**
- 429 "Rate limit exceeded" errors
- Quota exceeded messages

**Solutions:**
```
1. Check Current Usage:
   - Cloud Console → APIs & Services → Dashboard
   - Gmail API → Quotas
   - View current usage

2. Optimize Polling:
   - Increase interval (60s → 120s)
   - Reduce maxResults per poll
   - Implement exponential backoff

3. Request Quota Increase:
   - Cloud Console → Quotas
   - Request increase
   - Justify business need

4. Use Batch Operations:
   - Batch multiple operations
   - Reduce API calls
   - Cache frequent lookups
```

---

## Advanced Configuration

### Gmail Push Notifications

For instant email detection (instead of polling):

#### Enable Cloud Pub/Sub

```
1. Cloud Console → Pub/Sub
2. Create topic: "gmail-notifications"
3. Create subscription: "gmail-n8n"
4. Note subscription name

5. Grant Gmail access:
   - Topic permissions
   - Add member: gmail-api-push@system.gserviceaccount.com
   - Role: Pub/Sub Publisher
```

#### Configure Gmail Watch

```javascript
// In n8n, create HTTP endpoint
// Then call Gmail watch API:

{
  "method": "POST",
  "url": "https://gmail.googleapis.com/gmail/v1/users/me/watch",
  "headers": {
    "Authorization": "Bearer {{$auth.accessToken}}"
  },
  "body": {
    "topicName": "projects/YOUR_PROJECT/topics/gmail-notifications",
    "labelIds": ["Label_XX"]  // Your support label
  }
}

// Gmail will push notifications to Pub/Sub
// Process in near real-time
```

---

### Advanced Filtering

#### Multi-Language Detection

```javascript
// In n8n Function node:
const languageDetect = require('langdetect');

const emailBody = $json.textPlain;
const language = languageDetect.detect(emailBody)[0].lang;

// Route to language-specific AI agent
return {
  ...json,
  language: language,
  route: language === 'en' ? 'english-agent' : 'translation-needed'
};
```

#### Spam Detection

```javascript
// Check for spam indicators
const spamKeywords = ['viagra', 'lottery', 'inheritance', 'click here now'];
const emailLower = $json.textPlain.toLowerCase();

const isSpam = spamKeywords.some(keyword => 
  emailLower.includes(keyword)
);

if (isSpam) {
  // Apply spam label, skip AI processing
  return { spam: true };
}
```

#### Priority Detection

```javascript
// Detect urgent emails
const urgentPatterns = [
  /urgent/i,
  /asap/i,
  /emergency/i,
  /immediately/i,
  /critical/i
];

const isUrgent = urgentPatterns.some(pattern => 
  pattern.test($json.subject) || pattern.test($json.textPlain)
);

return {
  ...json,
  priority: isUrgent ? 'URGENT' : 'NORMAL'
};
```

---

### Auto-Responder Schedule

Only respond during business hours:

```javascript
// In n8n Function node
const now = new Date();
const hour = now.getHours();
const day = now.getDay();

// Business hours: Mon-Fri, 9 AM - 5 PM
const isBusinessHours = (
  day >= 1 && day <= 5 &&  // Monday to Friday
  hour >= 9 && hour < 17    // 9 AM to 5 PM
);

if (!isBusinessHours) {
  // Send out-of-office response
  return {
    ...json,
    response: "Thank you for contacting us. Our office hours are Monday-Friday, 9 AM - 5 PM. We'll respond when we reopen.",
    outOfOffice: true
  };
}

// Normal AI processing
return { ...json, outOfOffice: false };
```

---

### Email Signature

Professional signature for all responses:

```html
<div style="margin-top: 30px; padding-top: 20px; border-top: 2px solid #1a73e8;">
    <p style="margin: 5px 0;"><strong>{{agentName}}</strong><br>
    AI Support Assistant<br>
    {{companyName}}</p>
    
    <p style="margin: 5px 0; font-size: 12px; color: #666;">
    📧 support@yourdomain.com<br>
    🌐 www.yourdomain.com<br>
    📞 1-800-SUPPORT</p>
    
    <p style="margin: 15px 0 5px 0; font-size: 11px; color: #999;">
    This email was generated by an AI assistant. If you need to speak with a human agent, please reply to this email with "human" and we'll escalate your request immediately.
    </p>
</div>
```

---

### Attachment Handling

Process and store email attachments:

```javascript
// In n8n workflow:

1. Gmail Trigger returns attachments as:
   $json.payload.parts[].body.attachmentId

2. Download attachment:
   Gmail Node → Get Attachment
   Message ID: {{$json.id}}
   Attachment ID: {{$json.attachmentId}}

3. Upload to Google Drive:
   Google Drive Node → Upload
   File: {{$binary.data}}
   Folder: [Support Attachments]

4. Store URL in Airtable:
   Attachment URL: {{$json.webViewLink}}
```

---

### Auto-Categorization Training

Improve AI categorization over time:

```javascript
// Track categorization accuracy
const feedback = {
  ticketId: $json.ticketId,
  aiCategory: $json.category,
  aiPriority: $json.priority,
  humanCategory: null,  // Updated by human later
  humanPriority: null,
  timestamp: new Date(),
  correct: null  // Calculated after human review
};

// Store in "Training Data" table
// Periodically review and adjust prompts
// Feed back into AI training
```

---

### Integration with CRM

Sync email support with CRM:

```javascript
// After creating Airtable ticket:

1. Check if customer exists in CRM (Salesforce/HubSpot)
   - Query by email
   
2. If exists:
   - Link ticket to customer record
   - Update customer last_contacted
   - Add to support history
   
3. If new:
   - Create customer record
   - Import from email data
   - Tag as "New from Email Support"

4. Update CRM with ticket URL
   - Add note with ticket details
   - Set follow-up reminder
```

---

## Email Metrics & Analytics

### Track Key Metrics

```javascript
// Calculate in n8n:

const metrics = {
  // Response time
  responseTime: Date.now() - new Date($json.receivedAt).getTime(),
  
  // Email length
  emailLength: $json.textPlain.length,
  
  // Attachments
  hasAttachments: $json.payload.parts?.some(p => p.filename),
  attachmentCount: $json.payload.parts?.filter(p => p.filename).length || 0,
  
  // Thread depth
  threadDepth: $json.references?.split(',').length || 1,
  
  // Business hours
  receivedDuringBusinessHours: isBusinessHours(),
  
  // First response
  isFirstResponse: !$json.threadId || $json.threadId === $json.id
};

// Store in Daily Metrics table
```

---

## Security Best Practices

### Email Security

```yaml
✅ Use OAuth2 (not less secure IMAP)
✅ Enable 2FA on Gmail account
✅ Regularly rotate refresh tokens
✅ Monitor API usage for anomalies
✅ Use dedicated support account (not personal)
✅ Restrict OAuth scopes to minimum needed
✅ Log all email access attempts
✅ Implement rate limiting
✅ Validate email headers for spoofing
✅ Scan attachments for malware
```

### Data Privacy

```yaml
✅ Encrypt sensitive data in transit (HTTPS)
✅ Redact PII in logs
✅ Implement data retention policies
✅ Allow customers to request data deletion
✅ Comply with GDPR/CCPA requirements
✅ Document data flows
✅ Secure backup of emails
✅ Audit access logs regularly
```

### Access Control

```yaml
✅ Use service account with least privilege
✅ Separate dev/staging/prod accounts
✅ Revoke access for departed team members
✅ Monitor OAuth token usage
✅ Set token expiration policies
✅ Implement IP whitelisting if possible
✅ Use audit logs
```

---

## Performance Optimization

### Reduce API Calls

```javascript
// Batch operations:
const labelOperations = messages.map(msg => ({
  messageId: msg.id,
  addLabelIds: ['Label_PROCESSED'],
  removeLabelIds: ['Label_INBOX']
}));

// Single batch API call
gmail.users.messages.batchModify({
  userId: 'me',
  requestBody: {
    ids: messages.map(m => m.id),
    addLabelIds: ['Label_PROCESSED']
  }
});
```

### Cache Frequently Used Data

```javascript
// Cache label IDs
const labelCache = {
  'AI-Support-Inbox': 'Label_XX',
  'AI-Support-Processed': 'Label_YY',
  'AI-Support-Escalated': 'Label_ZZ'
};

// Use cached values instead of repeated API calls
```

### Optimize Polling

```javascript
// Smart polling based on activity
const lastEmailTime = getLastEmailTime();
const timeSinceLastEmail = Date.now() - lastEmailTime;

// If no emails in 5 minutes, reduce polling frequency
const pollInterval = timeSinceLastEmail > 300000 ? 300 : 60;  // seconds

// Adaptive polling saves API quota
```

---

## Checklist

### Pre-Launch Checklist

- [ ] Gmail API enabled in Cloud Console
- [ ] OAuth2 credentials created
- [ ] Consent screen configured
- [ ] Refresh token generated and saved
- [ ] Test user(s) added (if external app)
- [ ] Support email account configured
- [ ] Gmail labels created
- [ ] Email filters set up
- [ ] n8n workflow imported
- [ ] n8n credentials configured
- [ ] Workflow nodes updated with IDs
- [ ] Test emails successful
- [ ] Airtable integration verified
- [ ] Auto-reply templates tested
- [ ] Escalation flow working
- [ ] HTML formatting correct
- [ ] Thread handling verified
- [ ] Rate limits understood
- [ ] Backup email access configured
- [ ] Team trained on system
- [ ] Monitoring set up

---

## Next Steps

After successful Gmail setup:

1. **Configure Slack** (if not done)
   - See: `slack-setup.md`

2. **Set Up Analytics**
   - See: `google-sheets-template.md`

3. **Configure Airtable**
   - See: `SCHEMA.md`, `VIEWS.md`, `AUTOMATIONS.md`

4. **Train Your Team**
   - Share system capabilities
   - Define escalation procedures
   - Set response time expectations

5. **Monitor Performance**
   - Daily: Check n8n executions
   - Weekly: Review response times
   - Monthly: Analyze customer satisfaction

---

## Support Resources

### Official Documentation
- Gmail API: https://developers.google.com/gmail/api
- OAuth 2.0: https://developers.google.com/identity/protocols/oauth2
- Scopes: https://developers.google.com/gmail/api/auth/scopes

### Testing Tools
- OAuth Playground: https://developers.google.com/oauthplayground
- Gmail API Explorer: https://developers.google.com/gmail/api/reference/rest
- Message Format Tester: Test email formats before sending

### Community
- Stack Overflow: Tag [gmail-api]
- n8n Community: Email integration questions
- Google Cloud Community: API issues

---

## Version

**Version**: 1.0.0  
**Last Updated**: 2024-01-09  
**Compatible with**: Gmail API v1, n8n 0.200+

---

**Setup Complete!** 📧

Your Gmail integration is now ready to automatically process customer support emails with AI. For questions or issues, refer to the [Troubleshooting](#troubleshooting) section or contact your system administrator.
