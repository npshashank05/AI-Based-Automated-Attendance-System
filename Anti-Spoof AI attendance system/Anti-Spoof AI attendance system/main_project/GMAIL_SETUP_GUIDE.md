# Gmail Email Notification Setup Guide

Follow these steps to enable automatic absence notification emails using your Gmail account.

---

## Step 1 — Enable 2-Step Verification on your Google Account

> You MUST do this first. App Passwords only appear after 2FA is enabled.

1. Open your browser and go to:
   **https://myaccount.google.com/security**

2. Scroll down to **"How you sign in to Google"**

3. Click **"2-Step Verification"**

4. Click **"Get started"** and follow the on-screen instructions
   (You'll verify with your phone number or Google prompt)

5. Complete the setup — 2-Step Verification should now show as **ON**

---

## Step 2 — Generate a Gmail App Password

> An App Password is a 16-character code that lets our app send emails
> WITHOUT using your actual Gmail password.

1. Go to:
   **https://myaccount.google.com/apppasswords**
   
   _(If you don't see this page, make sure Step 1 is fully completed)_

2. You'll see a text box that says **"App name"**

3. Type any name, for example: `AI Attendance System`

4. Click **"Create"**

5. A popup will show a **16-character password** like: `abcd efgh ijkl mnop`

6. **Copy this password** (without spaces → `abcdefghijklmnop`)
   > ⚠️ This password is shown ONLY ONCE. Copy it now.

---

## Step 3 — Update your .env file

Open the file:
```
main_project/.env
```

Find these lines at the bottom and fill them in:

```
EMAIL_SENDER=your_gmail@gmail.com        ← your full Gmail address
EMAIL_PASSWORD=abcdefghijklmnop          ← 16-char app password (no spaces)
EMAIL_ENABLED=true                       ← change false to true
EMAIL_CLASS_NAME=Lecture                 ← optional: your class name
```

**Example (filled in):**
```
EMAIL_SENDER=johndoe123@gmail.com
EMAIL_PASSWORD=xkzbqplmwrtanjcd
EMAIL_ENABLED=true
EMAIL_CLASS_NAME=Computer Vision Lab
```

---

## Step 4 — Restart the Server

Stop the running server (Ctrl+C in terminal) and restart it:

```powershell
cd "D:\hackathon\Anti-Spoof AI attendance system\Anti-Spoof AI attendance system\main_project"
python web\backend\app.py
```

---

## Step 5 — Test It

1. Open the app at **http://localhost:5000**
2. Log in as Admin
3. Go to **"Mark Attendance"** tab
4. Upload a class photo and submit
5. Any student NOT detected in the photo will automatically receive an absence email

---

## How the Email Looks

The absent student will receive an email like this:

```
Subject: Absence Notice – 2026-02-21

Dear [Student Name],

This is an automated notification from the AI Attendance System.
Our system has recorded your absence for the following session:

  Subject/Class : Computer Vision Lab
  Date          : 2026-02-21

If you believe this is an error, please contact your class administrator.

Regards,
AI Attendance System
```

_(The actual email is styled with a red header and clean layout)_

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| "App Passwords" page not visible | Complete 2-Step Verification first |
| Authentication error in terminal | Make sure there are NO spaces in the app password in .env |
| Emails not arriving | Check spam/junk folder |
| "Less secure app" warning | Not needed — App Passwords bypass this |
| EMAIL_ENABLED=false → no emails | Change to `EMAIL_ENABLED=true` in .env |

---

## Notes

- Emails are sent in a **background thread** — the server won't slow down
- If email fails, attendance is still saved normally (email failure is non-critical)
- **Never share your App Password** — treat it like a password
- You can revoke the App Password anytime at myaccount.google.com/apppasswords
