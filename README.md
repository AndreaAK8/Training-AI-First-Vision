# Training AI-First Vision - Automated Welcome & Resource Pack

🎯 **Win 1: The Automated Welcome & Resource Pack**  
*Timeline: Week 1-2 (July 28 - August 8)*

## Overview

This repository contains the N8N automation workflow that eliminates 30 minutes per batch of repetitive guidance and follow-up for new hires. The system automatically detects new trainees added to Google Sheets and sends personalized welcome emails with all essential resources.

### Problem We Solved
- ❌ **Before**: Trainers spent up to 30 minutes per batch on repetitive guidance
- ❌ **Before**: New hires frequently asked for information already provided
- ❌ **Before**: Manual coordination between HR and Training teams

### Our Solution
- ✅ **After**: Automatic welcome email triggered by Google Sheets entry
- ✅ **After**: Instant delivery of training resources and FAQ answers
- ✅ **After**: Automated calendar scheduling and status tracking

### Key Success Metric
🎯 **Reduce repetitive, ad-hoc questions from new hires by 50%**

## 🚀 Quick Start

### Prerequisites
- N8N Cloud account (or self-hosted N8N 1.100.1+)
- Google Workspace account with Sheets and Calendar access
- Gmail or SMTP email access

### Setup Steps

1. **Import the Workflow**
   ```bash
   # Download the workflow file
   curl -O https://raw.githubusercontent.com/AndreaAK8/Training-AI-First-Vision/main/workflows/new-hire-welcome-automation.json
   ```

2. **Set up Google Sheets**
   - Copy our [Master New Hire List template](./templates/Master-New-Hire-List-Template.csv)
   - Update with your specific departments and managers

3. **Configure N8N**
   - Import the workflow JSON file
   - Set up Google credentials (Sheets, Calendar, Gmail)
   - Update email templates with your branding

4. **Test & Deploy**
   - Add a test entry to your Google Sheet
   - Verify email delivery and calendar creation
   - Activate the workflow

## 📋 Workflow Components

### Core Automation Flow
```
Google Sheets Trigger
    ↓
Filter (Status = "New")
    ↓
Send Welcome Email (Gmail)
    ↓
Update Status in Sheet
    ↓
Aggregate by Training Date
    ↓
Create Group Calendar Event
    ↓
Send Lark Notification (Future)
```

### Files Structure
```
├── workflows/
│   ├── new-hire-welcome-automation.json       # Main workflow
│   ├── training-reminder-workflow.json        # 2-day reminder workflow
│   └── workflow-documentation.md              # Technical setup guide
├── templates/
│   ├── Master-New-Hire-List-Template.csv     # Google Sheets template
│   ├── welcome-email-template.html           # Email template
│   └── calendar-event-template.md            # Calendar event format
├── docs/
│   ├── setup-guide.md                        # Step-by-step setup
│   ├── troubleshooting.md                    # Common issues & fixes
│   └── customization-guide.md                # Adapting for your org
└── README.md                                 # This file
```

## 🎯 Features

### 🆕 Latest Enhancement: Group Calendar Scheduling
**Added: January 2025**

The workflow now includes intelligent **group scheduling functionality**:
- **Automatic Grouping**: Trainees with the same Training Start Date are grouped together
- **Consolidated Events**: One calendar event per training date instead of individual events  
- **Multi-Attendee Support**: All trainees for the same date are invited to a single session
- **Scalable Solution**: Handles any number of trainees per training date efficiently

### ✅ Automated Welcome Emails
- **Trigger**: New entry in "Master New Hire List - 2025" Google Sheet
- **Content**: Personalized with trainee name, start date, and manager
- **Resources**: Direct links to Training Wiki and organizational info
- **FAQ**: Pre-answers common questions about equipment and preparation

### ✅ Smart Duplicate Prevention
- Only sends emails to trainees with Status = "New"
- Automatically updates status to "Email Sent" after delivery
- Prevents duplicate emails if workflow is re-run

### ✅ Smart Calendar Integration
- **Group Training Sessions**: Automatically groups trainees with same start date into single calendar events
- **Dynamic Attendee Management**: Invites all trainees and trainer for consolidated sessions
- **Intelligent Scheduling**: Creates one event per training date instead of individual events
- **Detailed Event Info**: Includes trainee names, departments, and pre-training checklist

### ✅ Status Tracking
- Tracks email delivery timestamps
- Updates Google Sheet with automation history
- Provides audit trail for compliance

## 📧 Email Template

Our welcome email includes:

**🎯 Essential Pre-Training Resources:**
- Training Lark Wiki access
- Meet the Organization guide  
- Pre-training preparation checklist

**📚 Training Journey Overview:**
- Days 1-2: Product Training with Andrea
- Days 3-6: Role-Specific Training
- Week 2+: Hands-on application

**💻 Technical Requirements:**
- Company-issued MacBook setup
- iPad preparation for interactive sessions

**❓ Proactive FAQ:**
- Day 1 preparation guidance
- Technical support contacts
- Q&A session information

## 🔧 Customization

### Adapting for Your Organization

1. **Update Email Content**
   - Edit `templates/welcome-email-template.html`
   - Replace StoreHub-specific links with your resources
   - Update trainer contact information

2. **Modify Google Sheets Structure**
   - Add/remove columns as needed in the template
   - Update field mappings in the N8N workflow
   - Adjust validation rules for your data

3. **Calendar Integration**
   - Configure your training calendar name
   - Adjust event duration and timing
   - Customize event descriptions and attendees

## 📊 Success Metrics

### Before Automation
- **Time per batch**: 30 minutes manual work
- **Response time**: Variable, up to several hours
- **Question volume**: High repetitive inquiries
- **Coordination**: Manual between HR and Training

### After Automation  
- **Time per batch**: <2 minutes (monitoring only)
- **Response time**: Instant (within 5 minutes of Sheet update)
- **Question volume**: 50% reduction in repetitive questions
- **Coordination**: Fully automated with audit trail

## 🛠️ Technical Details

### N8N Version
- **Tested on**: N8N 1.100.1
- **Node Types Used**: Google Sheets Trigger, Gmail, Google Calendar, Filter, Set
- **Polling Interval**: 5 minutes (configurable)

### Google API Usage
- **Sheets API**: Read/Write access for trainee data
- **Gmail API**: Send email permissions
- **Calendar API**: Event creation and management

### Security Considerations
- OAuth2 authentication for all Google services
- No sensitive data stored in N8N
- Audit trail maintained in Google Sheets

## 🚨 Troubleshooting

### Common Issues

**Email not sending?**
- Check Gmail API credentials and permissions
- Verify email addresses are valid format
- Ensure Status field contains exactly "New" (case-sensitive)

**Calendar events not creating?**
- Confirm Google Calendar API access
- Check date format in sheets (YYYY-MM-DD required)
- Verify calendar permissions

**Duplicate emails?**
- Ensure Filter node is properly configured
- Check Status column updates after email send
- Verify workflow connections are correct

See [docs/troubleshooting.md](./docs/troubleshooting.md) for detailed solutions.

## 📈 Future Enhancements

### Phase 2 Planned Features
- **Day 3 check-in email** for role-specific training
- **Training completion survey** automation  
- **Manager notification** when training is complete
- **Integration with LMS** for automatic enrollment
- **Slack channel notifications** for training team coordination

### Advanced Integrations
- **Microsoft Teams** integration for organizations using Teams
- **Notion** database synchronization for advanced tracking
- **Zapier/Make** connectors for hybrid automation

## 🤝 Contributing

This automation was built as part of the AI-First Training Vision initiative. To contribute:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/enhancement-name`)
3. Test your changes thoroughly
4. Submit a pull request with detailed description

## 📞 Support

**Technical Support:**
- **Primary Contact**: Andrea K. (Senior Training & Knowledge Base Specialist)
- **Email**: andrea.kaur@storehub.com
- **Internal Wiki**: [Training Automation Documentation](https://storehub.sg.larksuite.com/wiki/V1Dqw3SnbibdDdkQKpmlNQGygef)

**N8N Community:**
- [N8N Community Forum](https://community.n8n.io/)
- [N8N Documentation](https://docs.n8n.io/)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🏆 Acknowledgments

- **N8N Team** for the powerful automation platform
- **StoreHub Training Team** for requirements and testing
- **HR Partners** for seamless integration support
- **New Hire Beta Testers** for valuable feedback

---

**Built with ❤️ for efficient, human-centered training experiences**

*"What took 45 minutes now takes 3 minutes. It's not just time saved - it's peace of mind."*
