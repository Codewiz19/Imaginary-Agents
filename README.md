# 🧠 AI Meeting Scheduler Assistant

This is a smart meeting assistant built with **Flask**, **OpenAI-compatible LLMs** (like LLaMA), and **Google Calendar API**. It automatically reads meeting emails, extracts key details, checks calendars, and finds a free slot to schedule the meeting.

---

## 🚀 Features

- Extracts meeting **participants**, **duration**, **subject**, and **time constraints** from plain-text emails.
- Uses a **LLM (like LLaMA 3.1)** via a local server (OpenAI-compatible).
- Checks each participant’s **Google Calendar** for conflicts.
- Finds the earliest **common free time slot** and returns a schedule.
- Runs on a lightweight **Flask API** server.

---

## 📁 Folder Structure

```
.

├── submission.ipynb
├── Keys/
│   └── user.token         # Google Calendar OAuth token files
```

---

## ⚙️ Requirements

### 1. Python Libraries

Install required libraries:

```bash
pip install flask requests google-api-python-client google-auth-oauthlib python-dateutil
```

### 2. OpenAI-Compatible Server (e.g., LLaMA via `text-generation-webui`)

- Your LLM must support the OpenAI API format (`/v1/chat/completions`).
- Update the URL and model path in `app.py`.

### 3. Google Calendar Token

Place Google OAuth token files (from each user) in a folder called `Keys/`  
Filename format: `<username>.token`  
E.g. for `userone.amd@gmail.com`, filename should be `userone.token`

---

 

## ✅ Output Example

```json
{
  "EventStart": "2025-07-17T09:00:00+05:30",
  "EventEnd": "2025-07-17T09:30:00+05:30",
  "Subject": "Agentic AI Project Status Update",
  "Attendees": [
    {"email": "userone.amd@gmail.com"},
    {"email": "usertwo.amd@gmail.com"},
    {"email": "userthree.amd@gmail.com"}
  ],
  "Location": "IIT Mumbai"
}
```

---

## 🧠 How It Works

1. **Input**: User sends an email text and attendees list.  
2. **LLM**: Extracts subject, duration, participants, and constraints.  
3. **Date Parsing**: Converts “Thursday” or “next week” to exact dates.  
4. **Calendar Check**: Fetches events from Google Calendar.  
5. **Scheduling**: Picks the first free slot between 9 AM–6 PM.  
6. **Returns**: A suggested schedule JSON.

---

## 📌 Notes

- Pre-authorize Google Calendar and save tokens in `Keys/`.  
- Use an OpenAI API-compatible LLM.  
- For production, deploy with **Gunicorn** or **Docker**.

---

## 🔐 Security

- Add authentication and rate limits before public exposure.  
- Store `.token` files securely.

---

## 👨‍💻 Author

**Imaginary Agents** — AI Meeting Scheduler using LLMs, Flask & Calendar APIs.
