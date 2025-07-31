# N8N Training Automation Setup Guide

This guide provides step-by-step instructions for setting up the automated welcome and resource pack system using N8N 1.100.1.

## Prerequisites

Before starting, ensure you have:

- ✅ **N8N Cloud account** (or self-hosted N8N 1.100.1+)
- ✅ **Google Workspace account** with admin access
- ✅ **Gmail account** for sending emails
- ✅ **Google Calendar** for scheduling training sessions
- ✅ **Google Sheets** access for trainee tracking

## Phase 1: Google Sheets Setup

### 1.1 Create Master Tracking Sheet

1. **Create a new Google Sheet** named `"Master New Hire List - 2025"`
2. **Set up columns** exactly as follows:

| Column A | Column B | Column C | Column D | Column E | Column F | Column G | Column H |
|----------|----------|----------|----------|----------|----------|----------|----------|
| Timestamp | Full Name | Email Address | Department | Training Start Date | Manager Name | Status | Email Sent Date |

### 1.2 Configure Date Format

**Important**: Training Start Date must be in `YYYY-MM-DD` format (e.g., `2025-08-28`)

1. **Select column E** (Training Start Date)
2. **Format → Number → Date**
3. **Choose custom format**: `yyyy-mm-dd`

### 1.3 Set Up Status Values

**Status column** should contain one of:
- `New` - Ready for welcome email
- `Email Sent` - Welcome email delivered
- `Training Scheduled` - Calendar event created
- `Complete` - Training finished

## Phase 2: N8N Workflow Import

### 2.1 Import Workflow

1. **Download** `workflows/new-hire-welcome-automation.json`
2. **Log into N8N Cloud**
3. **Click "Add workflow"**
4. **Click "Import"** and select the JSON file
5. **Save** the imported workflow

### 2.2 Configure Google Credentials

**You'll need to set up 3 Google credentials:**

#### Google Sheets Access
1. **Click on Google Sheets Trigger node**
2. **Credential to connect with** → **"+ Create New Credential"**
3. **Choose "Google OAuth2 API"**
4. **Follow OAuth flow** to authorize
5. **Test connection** by selecting your sheet

#### Gmail Access  
1. **Click on Gmail node**
2. **Create new Gmail credential**
3. **Authorize Gmail API access**
4. **Grant send email permissions**

#### Google Calendar Access
1. **Click on Calendar node**
2. **Create new Google Calendar credential** 
3. **Authorize calendar management**
4. **Select your training calendar**

### 2.3 Update Sheet References

1. **Google Sheets Trigger node:**
   - **Document**: Select your "Master New Hire List - 2025"
   - **Sheet**: Select "Sheet1" (or your sheet name)
   - **Trigger On**: "Row Added"

2. **Update Status node:**
   - **Document**: Same sheet as trigger
   - **Column to Match On**: "Email Address"

## Phase 3: Configure Email Content

### 3.1 Customize Welcome Email

1. **Click on Gmail node**
2. **Update email template** with your:
   - Company name and branding
   - Training wiki URLs
   - Trainer contact information
   - Specific training schedule

### 3.2 Email Template Variables

The workflow uses these dynamic variables:
- `{{$json["Full Name"]}}` - Trainee name
- `{{$json["Training Start Date"]}}` - Start date
- `{{$json["Manager Name"]}}` - Manager name
- `{{$json["Email Address"]}}` - Trainee email

## Phase 4: Calendar Integration

### 4.1 Configure Calendar Events

1. **Click on Google Calendar node**
2. **Set calendar** (usually "primary" or your training calendar)
3. **Configure event details:**
   - **Title**: `Day 1 Product Training - {{$json["Full Name"]}}`
   - **Start**: `{{$json["Training Start Date"]}}T09:00:00`
   - **End**: `{{$json["Training Start Date"]}}T17:00:00`

### 4.2 Event Description Template

```
Product Training Session with [Your Name]

Trainee: {{$json["Full Name"]}}
Department: {{$json["Department"]}}
Manager: {{$json["Manager Name"]}}

Pre-training checklist:
✓ MacBook ready
✓ iPad ready
✓ Wiki access confirmed
```

## Phase 5: Testing

### 5.1 Test Workflow Components

**Test each node individually:**

1. **Google Sheets Trigger**
   - Add test row to your sheet
   - Check if trigger detects new entry

2. **Filter Node**
   - Ensure Status = "New" passes through
   - Verify other statuses are filtered out

3. **Gmail Node**
   - Send test email to yourself
   - Check email formatting and links

4. **Status Update**
   - Verify status changes to "Email Sent"
   - Check timestamp is recorded

5. **Calendar Event**
   - Confirm event is created
   - Verify attendees and description

### 5.2 End-to-End Testing

1. **Add test trainee** to Google Sheet:
   ```
   Timestamp: [Leave blank]
   Full Name: Test User
   Email: your.email@company.com
   Department: Test Department
   Training Start Date: 2025-08-28
   Manager Name: Test Manager
   Status: New
   Email Sent Date: [Leave blank]
   ```

2. **Activate workflow**
3. **Wait 5 minutes** (polling interval)
4. **Check results:**
   - Email received?
   - Status updated to "Email Sent"?
   - Calendar event created?

## Phase 6: Deployment

### 6.1 Activate Workflow

1. **Click "Active" toggle** in top-right
2. **Workflow should show "Active" status**
3. **Monitor executions** in the executions tab

### 6.2 Set Up Monitoring

**Create monitoring dashboard:**
- Track email delivery success rate
- Monitor failed executions
- Set up error notifications

### 6.3 User Training

**Train HR/Admin users on:**
- Correct Google Sheet format
- Required date format (YYYY-MM-DD)
- Status field values
- How to check automation results

## Advanced Configuration

### Optional Enhancements

1. **Slack Notifications**
   - Add Slack node after calendar
   - Notify training team of new trainees

2. **Reminder Emails**
   - Create separate workflow for 2-day reminders
   - Use Schedule Trigger + Google Sheets lookup

3. **Error Handling**
   - Add Try-Catch nodes around email sending
   - Create error notification system

### Performance Optimization

1. **Polling Frequency**
   - Default: 5 minutes
   - Reduce for faster response
   - Increase to conserve API calls

2. **Rate Limiting**
   - Gmail: 250 quota units per user per second
   - Google Sheets: 500 requests per 100 seconds
   - Calendar: 12,000 requests per minute

## Security Considerations

### Data Protection
- **No sensitive data** stored in N8N
- **OAuth2 authentication** for all Google services
- **Audit trail** maintained in Google Sheets

### Access Control
- **Limit Google Sheet** edit access
- **Secure N8N workspace** access
- **Regular credential** rotation

## Maintenance

### Regular Tasks
- **Monitor execution** success rates
- **Update email templates** as needed
- **Review and clean** old training records
- **Update N8N** to latest version

### Quarterly Review
- **Analyze automation** effectiveness
- **Gather user feedback**
- **Plan enhancements**
- **Update documentation**

---

**Need help?** Contact the training automation team or refer to our [troubleshooting guide](./troubleshooting.md). 