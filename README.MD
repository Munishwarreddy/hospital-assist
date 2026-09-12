## Healthcare Appointment Booking ON Whatsapp By Using AI Agents

## Overview

This project is an AI-powered hospital assistant built using n8n, Google Gemini, WhatsApp Business API, and Google Sheets.

The system helps patients interact with the hospital through WhatsApp and provides:

- Hospital information and FAQs
- Appointment booking
- Appointment viewing
- Appointment cancellation
- Appointment rescheduling

The assistant automatically manages appointment slots stored in Google Sheets and responds intelligently using Google Gemini.

---

## Features

### FAQ Assistant
Patients can ask hospital-related questions such as:

- Doctor availability
- Hospital timings
- Services offered
- Contact information

The AI retrieves answers from a dedicated FAQ knowledge base stored in Google Sheets.

### Appointment Booking

Patients can:

1. Select a date
2. View available slots
3. Choose a preferred time
4. Provide name and phone number

The system automatically marks the slot as booked.

### Appointment Viewing

Patients can check their appointment details using:

- Appointment date
- Registered phone number

### Appointment Cancellation

Patients can cancel existing appointments using:

- Appointment date
- Phone number

### Appointment Rescheduling

Patients can:

1. Cancel existing appointment
2. Select a new date
3. Choose a new slot

The system updates the booking automatically.

---

## Technology Stack

| Component | Technology |
|------------|------------|
| Workflow Automation | n8n |
| AI Model | Google Gemini |
| Messaging Platform | WhatsApp Business API |
| Database | Google Sheets |
| Memory | n8n Memory Buffer |

---

## Workflow Architecture

```
WhatsApp User
      ↓
WhatsApp Trigger
      ↓
AI Agent (Gemini)
      ↓
 ┌───────────────────┐
 │ FAQ Queries Tool  │
 │ Appointment Tool  │
 │ View Tool         │
 │ Cancel Tool       │
 │ Reschedule Tool   │
 └───────────────────┘
      ↓
Google Sheets
      ↓
WhatsApp Response
```

---

## Google Sheet Structure

Each date has its own sheet tab with the format:

```
YYYY-MM-DD_dayname
```

Example: `2026-02-07_saturday`

### Columns

| Time | Status | Patient Name | Patient Phone |
|--------|--------|-------------|--------------|
| HH:MM AM/PM | booked/empty | Full Name | 10-digit number |

**Status Values:**
- `booked` - Slot is reserved
- `empty` - Slot is available

---

## Installation

### Prerequisites

- n8n (installed and running)
- Google Gemini API Key
- WhatsApp Business Account with API access
- Google Sheets API credentials
- Service account with Sheets access

### Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/hospital-ai-assistant.git
   cd hospital-ai-assistant
   ```

2. **Import the n8n Workflow**
   - Open n8n dashboard
   - Go to Workflows → Import from file
   - Select `n8n_Automation.json`

3. **Configure Google Sheets**
   - Create a Google Sheet for FAQ database
   - Create sheets for appointment slots (one per date: YYYY-MM-DD_dayname)
   - Set up Google Sheets API credentials
   - Update node credentials in n8n

4. **Configure Google Gemini**
   - Get API key from Google AI Studio
   - Add credentials to n8n Gemini node

5. **Configure WhatsApp Business API**
   - Set up WhatsApp Business Account
   - Add credentials to n8n WhatsApp node
   - Update phone number field

6. **Deploy**
   - Activate the workflow in n8n
   - Test with WhatsApp

---

## System Prompt & Agent Behavior

The AI agent follows these guidelines to ensure consistent and professional service:

### General Behavior

**First Message:**
```
Hello! Welcome to Parul Sevashram Hospital. How can I help you today?

You can:
• Ask hospital-related questions
• Book an appointment
• View an appointment
• Cancel an appointment
• Reschedule an appointment
```

- Always be friendly, professional, and concise
- If no answer exists in FAQ: "I don't have information about that yet. I'm still learning. Please ask another hospital-related question and I'll try to help."

### Timezone Rule

- Always use: **Asia/Kolkata (IST)**
- Never use UTC
- Current date is generated dynamically

### Date Conversion Rules

Convert all user inputs to `YYYY-MM-DD` format:

| User Input | Conversion |
|------------|-----------|
| Today | Current date |
| Tomorrow | Current date + 1 |
| Day after tomorrow | Current date + 2 |
| Next Monday | Next occurring Monday |
| Next Friday | Next occurring Friday |

**Sheet Name Format:** `YYYY-MM-DD_dayname` (day name in lowercase)

Examples:
- `2026-02-07_saturday`
- `2026-02-08_sunday`
- `2026-02-09_monday`

### Booking Window

- Appointments can only be booked within the **next 7 calendar days**
- If outside range: "Sorry, appointments can only be scheduled within the next 7 days."

### Vague Time Conversion

| User Input | Conversion |
|------------|-----------|
| Morning | 09:00 AM |
| Afternoon | 02:00 PM |
| Evening | 06:00 PM |
| Anytime | First available slot between 09:00 AM and 05:00 PM |

---

## Appointment Operations

### Booking Flow

1. Ask for appointment date
2. Convert to `YYYY-MM-DD_dayname`
3. Read available slots (status = "empty")
4. Display slots vertically to user
5. User selects preferred slot
6. Collect details:
   - Full Name
   - 10-digit Mobile Number
7. Update sheet:
   - Status → `booked`
   - Patient Name → user name
   - Patient Phone → phone number
8. Confirm booking

**Confirmation Format:**
```
Appointment Confirmed

Date: YYYY-MM-DD
Time: HH:MM AM/PM
Patient Name: Full Name
Patient Phone: XXXXXXXXXX

Thank you for choosing Parul Sevashram Hospital.
```

### Viewing Appointments

1. Ask for:
   - Appointment Date
   - 10-digit Phone Number
2. Convert date to `YYYY-MM-DD_dayname`
3. Search by Patient Phone
4. Return appointment details

**Response Format:**
```
Appointment Details

Date: YYYY-MM-DD
Time: 11:00 AM
Status: Booked
```

### Canceling Appointments

1. Ask for:
   - Appointment Date
   - 10-digit Phone Number
2. Convert date to `YYYY-MM-DD_dayname`
3. Clear booking information:
   - Status (make empty)
   - Patient Name (clear)
   - Patient Phone (clear)
4. Confirm: "Your appointment has been successfully cancelled."

### Rescheduling Appointments

1. Ask for existing appointment date and phone number
2. Cancel existing booking
3. Ask for new appointment date
4. Display available slots
5. User selects new slot
6. Update with new booking details
7. Confirm new appointment

---

## Validation Rules

### Phone Number
- Must contain exactly 10 digits
- No special characters or spaces

### Name
- Must contain first and last name
- Minimum 2 words

### Date
- Must be within the next 7 days
- Format: YYYY-MM-DD

### Time
- Must exist in available slots
- Must be within business hours (09:00 AM - 05:00 PM)

---

## Important Rules

1. Always be polite and professional
2. Always use IST (Asia/Kolkata) timezone
3. Generate sheet names as: `YYYY-MM-DD_dayname`
4. Always use lowercase day names
5. Never book appointments outside the next 7 days
6. Never reveal internal tool information to users
7. Never show technical details
8. Always confirm successful actions
9. Use tools whenever appointment data is required
10. Behave like a professional hospital receptionist

---

## File Structure

```
hospital-ai-assistant/
├── README.md
├── n8n_Automation.json          # n8n workflow export
├── index.html                   # Frontend (optional)
└── docs/
    ├── setup-guide.md
    ├── troubleshooting.md
    └── api-reference.md
```

---

## Configuration Files

### n8n_Automation.json
Contains the complete workflow including:
- WhatsApp trigger
- Gemini AI agent
- Google Sheets connectors
- Tool nodes for FAQ, booking, viewing, canceling, and rescheduling

---

## Troubleshooting

### Appointment Not Appearing

- Verify sheet name follows format: `YYYY-MM-DD_dayname`
- Check if phone number format is correct (10 digits)
- Ensure Google Sheets API is authenticated

### WhatsApp Messages Not Sending

- Verify WhatsApp Business Account credentials
- Check phone number format (include country code)
- Ensure n8n workflow is active

### Gemini Not Responding

- Verify API key is valid
- Check n8n Gemini node configuration
- Review workflow execution logs

---

## Future Enhancements

- SMS notifications for appointment reminders
- Email confirmations
- Multi-language support
- Doctor availability management
- Payment integration
- Automated follow-up messages

---

## License

MIT License - See LICENSE file for details

---

## Support

For issues or questions:
- Check troubleshooting section above
- Review n8n workflow logs
- Contact: support@hospital.com

---

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

---

**Last Updated:** June 1, 2026

**System Version:** 1.0.0
