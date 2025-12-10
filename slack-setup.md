# Slack Setup Guide

Complete guide to setting up Slack integration for the AI Customer Support System.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Step 1: Create Slack App](#step-1-create-slack-app)
4. [Step 2: Configure Bot Permissions](#step-2-configure-bot-permissions)
5. [Step 3: Install App to Workspace](#step-3-install-app-to-workspace)
6. [Step 4: Get Credentials](#step-4-get-credentials)
7. [Step 5: Create Channels](#step-5-create-channels)
8. [Step 6: Configure n8n](#step-6-configure-n8n)
9. [Step 7: Test Integration](#step-7-test-integration)
10. [Troubleshooting](#troubleshooting)
11. [Advanced Configuration](#advanced-configuration)

---

## Overview

**What you're building:** A Slack bot that monitors support channels, automatically responds to customer inquiries using AI, and escalates complex issues to human agents.

**Time required:** 15-20 minutes

**Slack workspace required:** Yes (you must be a workspace admin)

---

## Prerequisites

### Required Access
- [ ] Slack workspace admin access
- [ ] Ability to create apps in workspace
- [ ] Permission to create channels
- [ ] n8n instance running

### Before You Start
- [ ] Read through entire guide
- [ ] Have .env file ready to update
- [ ] Backup existing Slack configuration (if any)

---

## Step 1: Create Slack App

### 1.1 Navigate to Slack API Dashboard

```
1. Open browser
2. Go to: https://api.slack.com/apps
3. Click "Create New App"
```

### 1.2 Choose Creation Method

```
Select: "From scratch"
(Not "From an app manifest" - we'll configure manually)
```

### 1.3 Configure Basic Information

```
App Name: AI Support Bot
(or "AI Customer Support" or your preferred name)

Pick a workspace: [Select your workspace]

Click: "Create App"
```

**✅ Success:** You'll be redirected to your new app's dashboard

---

## Step 2: Configure Bot Permissions

### 2.1 Navigate to OAuth & Permissions

```
Left sidebar → Features → OAuth & Permissions
```

### 2.2 Add Bot Token Scopes

Scroll to **"Scopes"** section → **"Bot Token Scopes"** → Click **"Add an OAuth Scope"**

Add these scopes one by one:

#### Required Scopes (Must Have)

```
✅ channels:history     - Read messages in public channels
✅ channels:read        - View basic channel info
✅ chat:write          - Send messages as @ai-support-bot
✅ chat:write.public   - Send messages to channels without joining
✅ files:write         - Upload files (for attachments)
✅ users:read          - View people in workspace
✅ users:read.email    - View email addresses
```

#### Optional Scopes (Recommended)

```
○ channels:manage      - Create and manage channels
○ groups:history       - Read private channel messages (if needed)
○ groups:read          - View private channels
○ im:history          - Read direct messages (if offering DM support)
○ im:write            - Send direct messages
○ reactions:write     - Add emoji reactions to messages
○ pins:write          - Pin important messages
```

**⚠️ Important:** Only add scopes you actually need. More scopes = more security review required in larger organizations.

### 2.3 Configure Redirect URLs (Optional)

If using OAuth flow for user authentication:

```
Redirect URLs:
https://your-n8n-domain.com/oauth/callback
http://localhost:5678/oauth/callback  (for development)
```

---

## Step 3: Install App to Workspace

### 3.1 Install the App

```
Still in "OAuth & Permissions" section:
Scroll to top → Click "Install to Workspace"

Review permissions → Click "Allow"
```

**✅ Success:** You'll see "OAuth Tokens for Your Workspace" section appear

### 3.2 Save Bot Token

```
Copy the "Bot User OAuth Token" 
Format: xoxb-xxxx-xxxx-xxxxxxxxxxxx

Save this to your .env file:
SLACK_BOT_TOKEN=xoxb-[your-token-here]
```

**🔐 Security:** This token is sensitive! Never commit to git or share publicly.

---

## Step 4: Get Credentials

### 4.1 Bot User OAuth Token

Already saved in Step 3.2 ✅

### 4.2 Signing Secret

```
Left sidebar → Settings → Basic Information
Scroll to "App Credentials" section

Copy "Signing Secret"
Save to .env:
SLACK_SIGNING_SECRET=[your-secret]
```

### 4.3 Client ID & Secret (Optional - for OAuth)

```
Same location as Signing Secret:

Copy "Client ID"
SLACK_CLIENT_ID=[your-client-id]

Copy "Client Secret"
SLACK_CLIENT_SECRET=[your-client-secret]
```

### 4.4 App-Level Token (Optional - for Socket Mode)

```
Left sidebar → Settings → Basic Information
Scroll to "App-Level Tokens"

Click "Generate Token and Scopes"
Token Name: n8n-socket
Add scope: connections:write

Copy token (starts with xapp-)
SLACK_APP_TOKEN=xapp-[your-token]
```

---

## Step 5: Create Channels

### 5.1 Create Support Channel

```
In Slack:
1. Click "+" next to "Channels"
2. Create new channel
   Name: support (or customer-support)
   Description: "Customer support inquiries - monitored by AI bot"
   Make public ✓
3. Click "Create"
```

### 5.2 Add Bot to Channel

```
In #support channel:
1. Type: /invite @AI Support Bot
2. Press Enter

Bot should appear in channel member list
```

### 5.3 Get Channel ID

**Method 1: From URL**
```
1. Open #support channel
2. Look at URL in browser:
   slack.com/app/.../messages/[CHANNEL_ID]/...
3. CHANNEL_ID format: C01ABC2D3EF
4. Save to .env:
   SLACK_SUPPORT_CHANNEL_ID=C01ABC2D3EF
```

**Method 2: From Channel Details**
```
1. Open #support channel
2. Click channel name at top
3. Scroll to bottom
4. Copy "Channel ID"
```

### 5.4 Create Additional Channels

Create these channels following the same process:

```
Channel: #escalations
Purpose: Human agent escalations
Privacy: Private (recommended)
Members: Support team + bot
SLACK_ESCALATIONS_CHANNEL_ID=C01XYZ789

Channel: #system-logs
Purpose: System notifications and logs
Privacy: Private
Members: Admin team + bot
SLACK_SYSTEM_LOGS_CHANNEL_ID=C02ABC456

Channel: #analytics
Purpose: Daily reports and metrics
Privacy: Public or Private
Members: Stakeholders + bot
SLACK_ANALYTICS_CHANNEL_ID=C03DEF789
```

### 5.5 Complete .env Configuration

Your .env should now have:
```bash
SLACK_BOT_TOKEN=xoxb-****-****-************************
SLACK_SIGNING_SECRET=********************************
SLACK_CLIENT_ID=********************.apps.svc.cluster.local
SLACK_CLIENT_SECRET=****************************************
SLACK_APP_TOKEN=xapp-****-****-************************

SLACK_SUPPORT_CHANNEL_ID=C01ABC2D3EF
SLACK_ESCALATIONS_CHANNEL_ID=C01XYZ789
SLACK_SYSTEM_LOGS_CHANNEL_ID=C02ABC456
SLACK_ANALYTICS_CHANNEL_ID=C03DEF789
```

---

## Step 6: Configure n8n

### 6.1 Add Slack Credentials in n8n

```
1. Open n8n: http://localhost:5678
2. Click Settings (gear icon) → Credentials
3. Click "Add Credential" → Search "Slack"
4. Select "Slack OAuth2 API"
```

### 6.2 Fill in Slack OAuth2 Credentials

```
Credential Name: Slack Support Bot
Client ID: [From .env]
Client Secret: [From .env]
Access Token: [Your Bot User OAuth Token from .env]

Click "Create"
```

### 6.3 Test Credentials

```
In credential screen:
Click "Test Credential"

✅ Should see: "Credential test successful"
❌ If fails: Check token and permissions
```

### 6.4 Import Workflows

```
1. In n8n → Workflows
2. Click "Import from File"
3. Select: 01-slack-support.json
4. Click "Import"
5. Open imported workflow
```

### 6.5 Configure Workflow Nodes

#### Update Slack Trigger Node
```
1. Click "Slack Trigger" node
2. Credential: Select "Slack Support Bot"
3. Channel: Select #support from dropdown
   (or enter manually: C01ABC2D3EF)
4. Trigger On: "Message"
```

#### Update Slack Send Message Nodes
```
For each "Slack" node in workflow:
1. Click node
2. Credential: "Slack Support Bot"
3. Channel: Verify correct channel ID
4. Save
```

#### Update Airtable Nodes
```
1. Click Airtable nodes
2. Select/create Airtable credential
3. Base: Select your base
4. Table: Select "Support Tickets"
5. Save
```

### 6.6 Activate Workflow

```
1. All nodes should be green (no errors)
2. Toggle "Active" switch at top
3. Workflow is now listening!
```

---

## Step 7: Test Integration

### 7.1 Basic Message Test

```
1. Go to #support channel in Slack
2. Type: "Test message - is the bot working?"
3. Press Enter

Expected:
✅ Bot should respond within 3-5 seconds
✅ Message should be relevant
✅ Check n8n execution history for logs
```

### 7.2 Test Bug Report

```
In #support:
"Help! The app crashes when I click the export button. Getting error code 500."

Expected:
✅ Bot categorizes as BUG
✅ Provides troubleshooting steps
✅ Offers workaround
✅ Airtable record created
```

### 7.3 Test Escalation

```
In #support:
"I was charged $500 instead of $50! I need an immediate refund!"

Expected:
✅ Bot recognizes billing + urgent + negative
✅ Escalates to human
✅ Posts in #escalations channel
✅ Customer receives acknowledgment
```

### 7.4 Verify n8n Execution

```
1. In n8n → Executions
2. Should see successful executions
3. Click execution to view:
   - Trigger data
   - AI responses
   - Airtable records
   - Slack messages sent
```

### 7.5 Verify Airtable

```
1. Open Airtable base
2. Check "Support Tickets" table
3. Should see new records with:
   - Ticket ID
   - Customer info
   - Message content
   - Category, Priority, Sentiment
   - AI response
   - Status
```

---

## Troubleshooting

### Issue: Bot Not Responding

**Symptoms:** Messages sent to #support but no bot response

**Solutions:**

```
1. Check Bot is in Channel
   - Type: /invite @AI Support Bot
   - Verify bot appears in member list

2. Check n8n Workflow Active
   - Workflow should show "Active" toggle ON
   - Check execution history for errors

3. Verify Channel ID
   - Compare SLACK_SUPPORT_CHANNEL_ID in .env
   - With actual channel ID in Slack

4. Test Bot Token
   curl -H "Authorization: Bearer xoxb-YOUR-TOKEN" \
        https://slack.com/api/auth.test
   
   Should return: "ok": true

5. Check Bot Permissions
   - Ensure all required scopes added
   - Reinstall app if scopes changed
```

---

### Issue: Bot Can't Send Messages

**Symptoms:** Bot reads messages but can't reply

**Solutions:**

```
1. Check chat:write Scope
   - OAuth & Permissions → Bot Token Scopes
   - Ensure chat:write is listed
   - Reinstall if missing

2. Verify Bot Token in n8n
   - Credentials should use Bot User OAuth Token
   - NOT User OAuth Token

3. Check Channel Permissions
   - Bot must be member of channel
   - For private channels, explicitly invite bot

4. Test Manually in n8n
   - Create new Slack node
   - Select channel
   - Enter test message
   - Execute node
```

---

### Issue: Credentials Invalid

**Symptoms:** "Invalid credentials" error in n8n

**Solutions:**

```
1. Regenerate Token
   - Slack App → OAuth & Permissions
   - "Reinstall App"
   - Copy new token
   - Update n8n credential

2. Check Token Format
   - Bot Token: xoxb-***
   - User Token: xoxp-*** (wrong - don't use)
   - App Token: xapp-*** (different use case)

3. Verify Workspace
   - Token is workspace-specific
   - Using token from correct workspace?

4. Check Expiration
   - Some tokens expire
   - Regenerate if needed
```

---

### Issue: Missing Permissions

**Symptoms:** "Missing scope: channels:history" errors

**Solutions:**

```
1. Add Missing Scopes
   - OAuth & Permissions → Bot Token Scopes
   - Add required scope
   - Click "Reinstall App"
   - Update token in n8n

2. Required Scopes Checklist:
   ☑ channels:history
   ☑ channels:read
   ☑ chat:write
   ☑ users:read

3. After Adding Scopes:
   - Always reinstall app
   - Get new token
   - Update everywhere token is used
```

---

### Issue: Channel ID Not Found

**Symptoms:** "Channel not found" error

**Solutions:**

```
1. Verify Channel ID Format
   - Should be: C01ABC2D3EF (11 characters)
   - NOT: #support (channel name)
   - NOT: support (without #)

2. Get Channel ID Correctly
   - Method 1: From URL when viewing channel
   - Method 2: Right-click channel → View details
   - Method 3: Use Slack API:
     curl -H "Authorization: Bearer xoxb-TOKEN" \
          https://slack.com/api/conversations.list

3. Update in Both Places
   - .env file
   - n8n workflow nodes

4. Private Channels
   - Bot must be invited first
   - Then can get channel ID
```

---

### Issue: Messages Timing Out

**Symptoms:** "Request timeout" errors

**Solutions:**

```
1. Increase Timeout in n8n
   - Workflow settings
   - Execution timeout: 60 seconds

2. Check OpenAI API
   - Verify API key valid
   - Check rate limits
   - Test OpenAI separately

3. Optimize Workflow
   - Reduce unnecessary nodes
   - Cache frequently used data
   - Use parallel processing where possible
```

---

## Advanced Configuration

### Event Subscriptions (Real-time)

For instant message detection (instead of polling):

```
1. Slack App → Event Subscriptions
2. Enable Events: ON
3. Request URL: https://your-n8n-domain.com/webhook/slack-events
4. Subscribe to bot events:
   - message.channels
   - message.groups (if supporting private channels)
   - message.im (if supporting DMs)
5. Save Changes
6. Update n8n webhook node with verification token
```

---

### Slash Commands

Create custom commands like `/support-help`:

```
1. Slack App → Slash Commands
2. Create New Command
   Command: /support-help
   Request URL: https://your-n8n-domain.com/webhook/slack-command
   Short Description: "Get AI support help"
   Usage Hint: "[your question]"
3. Save
4. Create matching n8n webhook workflow
```

---

### Interactive Messages

Enable buttons and dropdowns:

```
1. Slack App → Interactivity & Shortcuts
2. Enable Interactivity: ON
3. Request URL: https://your-n8n-domain.com/webhook/slack-interactive
4. Save

Example button in n8n:
{
  "blocks": [
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "Was this helpful?"
      }
    },
    {
      "type": "actions",
      "elements": [
        {
          "type": "button",
          "text": {"type": "plain_text", "text": "Yes"},
          "value": "helpful_yes",
          "action_id": "helpful_yes"
        },
        {
          "type": "button",
          "text": {"type": "plain_text", "text": "No"},
          "value": "helpful_no",
          "action_id": "helpful_no"
        }
      ]
    }
  ]
}
```

---

### Bot Customization

#### Update Bot Display Name
```
Slack App → App Home → Display Information
Bot Display Name: AI Support Assistant
Default Name: @ai-support
```

#### Upload Bot Icon
```
Same location:
App Icon: Upload 512x512 PNG
Choose an icon that represents your brand
```

#### Add Bot Description
```
App Home → App Display Information
Description: "AI-powered customer support bot that provides instant help and escalates complex issues to human agents."
```

---

### Message Formatting

#### Rich Text Messages
```javascript
// In n8n Slack node, use blocks:
{
  "text": "Fallback text",
  "blocks": [
    {
      "type": "header",
      "text": {
        "type": "plain_text",
        "text": "🐛 Bug Report Received"
      }
    },
    {
      "type": "section",
      "fields": [
        {
          "type": "mrkdwn",
          "text": "*Ticket:*\nTKT-20240109-001"
        },
        {
          "type": "mrkdwn",
          "text": "*Priority:*\nHIGH"
        }
      ]
    },
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "*Issue:* App crashes on export"
      }
    },
    {
      "type": "actions",
      "elements": [
        {
          "type": "button",
          "text": {"type": "plain_text", "text": "View in Airtable"},
          "url": "https://airtable.com/..."
        }
      ]
    }
  ]
}
```

#### Emoji Support
```
Use emoji in messages:
:white_check_mark: Task completed
:warning: Warning message  
:x: Error occurred
:robot_face: AI response
```

---

### Rate Limiting

Slack API limits:
```
Tier 1: 1+ requests per minute
Tier 2: 20+ requests per minute  
Tier 3: 50+ requests per minute
Tier 4: 100+ requests per minute

Best Practices:
- Batch messages when possible
- Use retry logic with exponential backoff
- Monitor rate limit headers
- Cache channel/user lookups
```

---

### Monitoring & Analytics

#### Enable Audit Logs
```
Enterprise Grid only:
Admin console → Settings → Organization settings → Audit logs
Export logs for analysis
```

#### Track Bot Metrics
```
Slack App → Analytics
View:
- Message volume
- Active users
- Response times
- Error rates
```

#### Custom Logging in n8n
```javascript
// Add to workflow:
const logData = {
  timestamp: new Date(),
  channel: $json.channel,
  user: $json.user,
  message: $json.text,
  ai_response: $json.ai_response,
  confidence: $json.confidence,
  execution_time: $execution.executionTime
};

// Send to logging service or Airtable
```

---

## Security Best Practices

### Token Security
```
✅ Store tokens in .env (never in code)
✅ Use environment-specific tokens (dev/prod)
✅ Rotate tokens quarterly
✅ Revoke tokens when team members leave
✅ Monitor token usage for anomalies
✅ Use least-privilege principle (minimal scopes)
```

### Bot Permissions
```
✅ Only grant necessary scopes
✅ Regularly audit permissions
✅ Restrict bot to specific channels
✅ Use private channels for sensitive topics
✅ Implement message filtering for PII
✅ Log all bot activities
```

### Compliance
```
✅ Review data retention policies
✅ Implement GDPR compliance (if applicable)
✅ Document data flows
✅ Provide privacy policy
✅ Allow users to opt-out
✅ Secure data transmission (HTTPS only)
```

---

## Slack Workspace Settings

### Configure Workspace for Bot

```
Admin Settings to Review:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. App Management
   - Allow custom integrations: Yes
   - App approval: Configure as needed

2. Permissions
   - Members can install apps: Configure
   - App restrictions: Set if needed

3. Retention
   - Message retention policy
   - File retention policy

4. Notifications
   - Configure notification preferences
   - Avoid notification spam from bot
```

---

## Migration from Existing Bot

If replacing an existing bot:

```
1. Export existing bot data
2. Map user IDs (old bot → new bot)
3. Migrate channel memberships
4. Update integrations to point to new bot
5. Run in parallel for testing period
6. Switch traffic gradually
7. Decommission old bot
8. Archive old bot data
```

---

## Support & Resources

### Official Documentation
- Slack API: https://api.slack.com/
- Bot Users: https://api.slack.com/bot-users
- OAuth: https://api.slack.com/authentication/oauth-v2
- Scopes: https://api.slack.com/scopes

### Slack Community
- Community Forum: https://community.slack.com/
- Stack Overflow: Tag [slack-api]
- GitHub Issues: Track n8n Slack node issues

### Testing Tools
- Postman Collection: Available for Slack API
- Slack API Tester: Test API calls directly
- Block Kit Builder: Design message layouts
  https://app.slack.com/block-kit-builder/

---

## Checklist

### Pre-Launch Checklist
- [ ] Slack app created
- [ ] All required scopes added
- [ ] App installed to workspace
- [ ] Bot tokens saved to .env
- [ ] All channels created
- [ ] Bot invited to channels
- [ ] Channel IDs saved to .env
- [ ] n8n credentials configured
- [ ] Workflows imported
- [ ] Workflows activated
- [ ] Test messages successful
- [ ] Escalations working
- [ ] Airtable integration verified
- [ ] Error handling tested
- [ ] Rate limiting understood
- [ ] Security review completed
- [ ] Documentation updated
- [ ] Team trained on bot usage

---

## Next Steps

After successful Slack setup:

1. **Configure Email Support** (if needed)
   - See: `gmail-setup.md`

2. **Set Up Analytics**
   - See: `google-sheets-template.md`

3. **Configure Escalation Management**
   - Review: `AUTOMATIONS.md`

4. **Train Your Team**
   - Share bot capabilities
   - Define escalation procedures
   - Set SLA expectations

5. **Monitor Performance**
   - Daily: Check n8n executions
   - Weekly: Review metrics
   - Monthly: Optimize workflows

---

## Version

**Version**: 1.0.0  
**Last Updated**: 2024-01-09  
**Compatible with**: Slack API 2.0, n8n 0.200+

---

**Setup Complete!** 🎉

Your Slack bot is now ready to provide AI-powered customer support. For questions or issues, refer to the [Troubleshooting](#troubleshooting) section or contact your system administrator.
