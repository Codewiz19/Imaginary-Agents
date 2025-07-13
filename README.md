# 🤖 AI Scheduling Assistant

A fully autonomous Agentic AI-based calendar scheduler built for the AMD Hackathon.  
This agent eliminates the tedious back-and-forth of meeting planning by intelligently parsing email requests, identifying time constraints, coordinating with Google Calendar, and scheduling conflict-free meetings.

---

## 🚀 Features

- ✅ **Agentic AI Planning** – No manual micromanagement required  
- ✅ **Google Calendar Integration** – Real-time calendar sync  
- ✅ **Natural Language Understanding** – Extracts intent from email text  
- ✅ **Conflict-Free Scheduling** – Evaluates availability across participants  
- ✅ **Time-Zone Aware** – Defaults to IST (Asia/Kolkata)  

---

## 🛠 Tech Stack

| Component            | Stack/Tool                                    |
|----------------------|-----------------------------------------------|
| Backend Agent        | Python + vLLM + Google Calendar API           |
| LLM Server           | vLLM (DeepSeek or LLaMA-3.1)                  |
| Inference Model      | `deepseek-llm-7b-chat` / `Meta-Llama-3.1-8B`  |
| Calendar API         | Google Calendar v3 API                        |
| Timezone Management  | `dateutil`, `datetime`                        |

---

## 🧠 Agent Flow

The core scheduling flow is handled by the `MeetingSchedulerAgent`, which orchestrates the following steps:

---

### 1. 📨 Email Parsing (`parse_email`)

The agent uses an LLM to extract structured data from free-form email content. Extracted fields include:

- **Participants**: List of email addresses (names converted to `@amd.com` if needed)
- **Meeting Duration**: In minutes (inferred if not provided)
- **Time Constraints**: Phrases like "next Thursday", "Monday at 10am"
- **Subject**: Optional fallback from email body

📤 **Example Output from LLM**:
```json
{
  "participants": ["user1@amd.com", "user2@amd.com"],
  "meeting_duration": 30,
  "time_constraints": "next Thursday",
  "subject": "Project Sync"
}
```

---

### 2. 📅 Constraint Resolution (`extract_meeting_details`)

This function processes the natural language constraints into exact calendar time windows:

| Constraint Example        | Start Time           | End Time             |
|---------------------------|----------------------|----------------------|
| "next Monday at 10:00"    | `10:00`              | `23:59`              |
| "Thursday"                | `00:00`              | `23:59`              |
| "next week"               | Next Monday `00:00`  | That day's `23:59`   |

📌 **Time Defaults**:
- No time → Use `00:00` to `23:59`
- Time mentioned → Use as start, end of day as end

---

### 3. 📥 Fetch Events (`retrive_calendar_events`)

Using the Google Calendar API, the agent pulls existing events for:

- The sender (`From`)
- All attendees (`Attendees`)

📄 **Example Event Data**:
```json
[
  {
    "StartTime": "2025-07-17T10:00:00+05:30",
    "EndTime": "2025-07-17T10:30:00+05:30",
    "NumAttendees": 3,
    "Attendees": [
      "userone@amd.com",
      "usertwo@amd.com",
      "userthree@amd.com"
    ],
    "Summary": "Team Sync"
  }
]
```

---

### 4. 🧠 Scheduling Logic (`_schedule_internal`)

The agent iterates through working hours (9 AM to 6 PM IST), looking for a common conflict-free time slot across all users.

✅ **Steps**:
- For each hour block:
  - Check for scheduling conflicts across all attendees
  - If available, assign that slot and populate it into each user's calendar view

📤 **Final Output Format**:
```json
{
  "Request_id": "1234",
  "Datetime": "09-07-2025T12:34:55",
  "Location": "IIT Mumbai",
  "From": "userone.amd@gmail.com",
  "Attendees": [
    {
      "email": "userone.amd@gmail.com",
      "events": [ ... ]
    },
    {
      "email": "usertwo.amd@gmail.com",
      "events": [ ... ]
    },
    {
      "email": "userthree.amd@gmail.com",
      "events": [ ... ]
    }
  ],
  "Subject": "Project Status",
  "EmailContent": "Hi team, let's meet next Thursday at 10:00 AM for 30 minutes.",
  "EventStart": "2025-07-17T10:30:00+05:30",
  "EventEnd": "2025-07-17T11:00:00+05:30",
  "Duration_mins": "30"
}
```

---

## 📦 Example Input/Output

### Input JSON
```json
{
  "Request_id": "1234",
  "Datetime": "09-07-2025T12:34:55",
  "Location": "IIT Mumbai",
  "From": "userone.amd@gmail.com",
  "Attendees": [
    { "email": "usertwo.amd@gmail.com" },
    { "email": "userthree.amd@gmail.com" }
  ],
  "Subject": "Project Status",
  "EmailContent": "Hi team, let's meet next Thursday at 10:00 AM for 30 minutes."
}
```

### Output JSON
```json
{
  "Request_id": "1234",
  "Datetime": "09-07-2025T12:34:55",
  "Location": "IIT Mumbai",
  "From": "userone.amd@gmail.com",
  "Attendees": [
    {
      "email": "userone.amd@gmail.com",
      "events": [ ... ]
    },
    {
      "email": "usertwo.amd@gmail.com",
      "events": [ ... ]
    },
    {
      "email": "userthree.amd@gmail.com",
      "events": [ ... ]
    }
  ],
  "Subject": "Project Status",
  "EmailContent": "Hi team, let's meet next Thursday at 10:00 AM for 30 minutes.",
  "EventStart": "2025-07-17T10:30:00+05:30",
  "EventEnd": "2025-07-17T11:00:00+05:30",
  "Duration_mins": "30"
}
```

---

## 🧪 Testing the Agent

To run the scheduler agent:
```python
agent = MeetingSchedulerAgent(model_client=client, model_path="deepseek-llm-7b-chat")
result = agent.schedule(meeting_input)
print(result)
```

---

## 🏁 Final Notes

- Ensure Google Calendar tokens are stored as `Keys/<username>.token`
- Timezone default: `Asia/Kolkata` (IST)
- vLLM server should be running on MI300 with selected model loaded
- All output formats are automatically validated for hackathon evaluation
