# N8N Training Automation Troubleshooting Guide

This guide covers common issues and solutions based on real implementation experience with N8N 1.100.1.

## Common Issues & Solutions

### 1. Filter Node Going to False Branch

**Problem**: IF/Filter node always executes the False branch, even with Status = "New"

**Symptoms**:
- Workflow runs but no emails are sent
- Filter node shows data going to False output
- Status field appears correct in Google Sheets

**Solution**:
```javascript
// In IF node condition:
Value 1: {{$json["Status"]}}
Condition: Equal  
Value 2: New  // WITHOUT quotes
```

**Key Points**:
- ❌ Don't use quotes around "New" in Value 2
- ✅ Use {{$json["Status"]}} exactly (case sensitive)
- ✅ Ensure Status field contains exactly "New" (no extra spaces)

### 2. Email Sent Date Shows Formula Instead of Timestamp

**Problem**: Google Sheets shows `{{$now}}` literally instead of actual timestamp

**Symptoms**:
- Status updates correctly to "Email Sent"
- Email Sent Date column shows the formula text
- Other fields update properly

**Solution**:
```javascript
// In Google Sheets update node:
Email Sent Date: ={{$now}}  // Note the = at the beginning
```

**Alternative Solution**:
```javascript
Email Sent Date: {{$now.format('yyyy-MM-dd HH:mm:ss')}}
```

### 3. Calendar Event Creation Fails (400 Error)

**Problem**: "Bad request - please check your parameters" error when creating calendar events

**Symptoms**:
- Email sends successfully
- Status updates correctly  
- Calendar node shows red error
- Error message: "Bad Request"

**Root Cause**: Date format mismatch

**Solution**:
1. **Fix Google Sheets date format**:
   - Select Training Start Date column
   - Format → Number → Date
   - Choose format: `YYYY-MM-DD` (e.g., 2025-08-28)

2. **Verify N8N date format**:
   ```javascript
   Start: {{$json["Training Start Date"]}}T09:00:00
   End: {{$json["Training Start Date"]}}T17:00:00
   ```

### 4. Google Sheets Trigger Not Detecting New Rows

**Problem**: Adding new rows to Google Sheet doesn't trigger the workflow

**Symptoms**:
- Workflow is active
- New rows added but no execution
- Manual execution works fine

**Solutions**:

1. **Check Polling Interval**:
   - Default: 5 minutes
   - Wait at least one polling cycle
   - Check executions tab for attempts

2. **Verify Sheet Configuration**:
   - Document name must match exactly
   - Sheet name must be correct (usually "Sheet1")
   - Trigger set to "Row Added"

3. **Test Authentication**:
   - Re-authenticate Google Sheets credential
   - Ensure proper permissions granted
   - Test connection in credential settings

### 5. Gmail Authentication Issues

**Problem**: Gmail node fails with authentication errors

**Symptoms**:
- "Invalid credentials" error
- "Insufficient permissions" error
- OAuth flow fails

**Solutions**:

1. **Re-create Gmail Credential**:
   - Delete existing credential
   - Create new Gmail OAuth2 credential
   - Grant all requested permissions

2. **Check Gmail API Quotas**:
   - Gmail has daily sending limits
   - Monitor API usage in Google Cloud Console
   - Consider using SMTP as alternative

### 6. Duplicate Emails Being Sent

**Problem**: Same trainee receives multiple welcome emails

**Symptoms**:
- Multiple executions for same person
- Status shows "Email Sent" but emails keep coming
- Execution log shows repeated triggers

**Solutions**:

1. **Verify Filter Logic**:
   - Ensure IF node filters Status = "New" only
   - Check that Status updates to "Email Sent"
   - Confirm filter is properly connected

2. **Check Google Sheets Updates**:
   - Verify Status column updates correctly
   - Ensure no manual overwrites to "New"
   - Check for duplicate rows

### 7. Missing Calendar Title and Description

**Problem**: Calendar events create but missing title and description

**Symptoms**:
- Calendar event appears in calendar
- No title or generic title
- No description content

**Solution**:
1. **Look for "Additional Fields"** section in Calendar node
2. **Add these fields**:
   ```javascript
   Summary: Day 1 Product Training - {{$json["Full Name"]}}
   Description: [Your description template]
   ```

3. **If no Additional Fields section**:
   - Check node version (should be v2+)
   - Update N8N if using older version

## Debugging Tips

### 1. Use Execution Log
- Click on "Executions" tab
- Check each node's input/output data
- Look for error messages and stack traces

### 2. Test Nodes Individually
- Use "Execute Node" button on each node
- Verify data flow between nodes
- Check data transformations

### 3. Check Data Types
- Ensure dates are in correct format
- Verify email addresses are valid
- Check for empty/null values

### 4. Monitor API Quotas
- Google Sheets: 500 requests per 100 seconds
- Gmail: 250 quota units per user per second  
- Calendar: 12,000 requests per minute

## Performance Issues

### 1. Slow Execution
**Causes**:
- High polling frequency
- Large Google Sheets
- Network latency

**Solutions**:
- Increase polling interval (reduce frequency)
- Filter data at source
- Use pagination for large datasets

### 2. Rate Limiting
**Symptoms**:
- 429 "Too Many Requests" errors
- Intermittent failures
- Slow API responses

**Solutions**:
- Add delay between operations
- Reduce polling frequency  
- Implement retry logic

## Getting Help

### 1. N8N Community Resources
- [N8N Community Forum](https://community.n8n.io/)
- [N8N Documentation](https://docs.n8n.io/)
- [N8N GitHub Issues](https://github.com/n8n-io/n8n/issues)

### 2. Google API Documentation
- [Google Sheets API](https://developers.google.com/sheets/api)
- [Gmail API](https://developers.google.com/gmail/api)
- [Google Calendar API](https://developers.google.com/calendar)

### 3. Internal Support
- Training Team: andrea.kaur@storehub.com
- IT Support: [Your IT contact]
- N8N Admin: [Your N8N administrator]

## Logs and Monitoring

### 1. Enable Detailed Logging
```javascript
// Add debug nodes to track data flow
console.log('Current data:', JSON.stringify($input.all(), null, 2));
```

### 2. Set Up Monitoring Alerts
- Email delivery failure alerts
- Workflow execution failure notifications
- API quota warning alerts

### 3. Regular Health Checks
- Weekly execution success rate review
- Monthly API usage analysis
- Quarterly workflow performance assessment

### 8. Aggregate Node Calendar Integration Issues

**Problem**: "attendee.split is not a function" or "Bad request" errors after adding Aggregate node

**Symptoms**:
- Calendar node fails after Aggregate node is added
- Arrays being passed where strings are expected
- Date fields not accessible in calendar node
- "Bad request - please check your parameters" error

**Solution**:
```javascript
// Update Calendar node fields for aggregated data:

// Start Time:
{{$json["Training Start Date"][0]}}T09:00:00

// End Time: 
{{$json["Training Start Date"][0]}}T17:00:00

// Attendees:
{{$json["Email Address"].join(",")}}

// Event Title:
Day 1 Product Training - {{$json["Training Start Date"][0]}} ({{$json["Full Name"].length}} trainees)

// Event Description:
Product Training Session for:
• {{$json["Full Name"][0]}} - {{$json["Department"][0]}}
• {{$json["Full Name"][1]}} - {{$json["Department"][1]}}
```

**Key Points**:
- ✅ Use `[0]` to access first item in aggregated arrays for single values
- ✅ Use `.join(",")` to convert email arrays to comma-separated strings  
- ✅ Use `.length` to count number of trainees
- ✅ Calendar expects strings, not arrays for most fields

**Common Aggregate Node Configuration**:
- **Aggregate**: "Individual Fields"
- **Group By**: Training Start Date (operation: "first")
- **Collect Fields**: Full Name, Email Address, Department (operation: "collect")

---

**Still having issues?** Create a support ticket with:
1. Exact error message
2. Screenshot of node configuration
3. Sample data that's failing
4. Execution ID from N8N 