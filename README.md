#  CAPS Event Registration Desk

> A lightweight, zero-dependency event registration system with automated email delivery — built for the CAPS BYC Tech Fest.

![Status](https://img.shields.io/badge/status-production--ready-brightgreen)
![Stack](https://img.shields.io/badge/stack-HTML%20%7C%20CSS%20%7C%20JS-blue)
![Email](https://img.shields.io/badge/email-EmailJS-orange)

---

##  Overview

The **CAPS Registration Desk** is a single-file, browser-based event check-in system designed for use at the CAPS BYC Tech Fest. A volunteer simply opens one HTML file in Chrome — no server, no installation, no configuration beyond filling in three API credentials — and the desk is live. When a student is registered, their name and email are passed to the EmailJS API, which injects them into a pre-built template and instantly delivers the event WiFi password to their inbox.

###  Key Features

- **Zero setup friction** — one file, opens in any modern browser
- **Live email delivery** — sends WiFi credentials automatically via EmailJS
- **Duplicate detection** — flags re-submissions in real time with amber warnings
- **Live registration log** — right-panel feed with timestamps and initials avatars
- **Session persistence** — registrations survive page refreshes via `sessionStorage`
- **Demo / dry-run mode** — works without API keys (simulates emails to browser console)
- **Dark mode support** — respects system `prefers-color-scheme`
- **Responsive layout** — collapses to single-column on narrow screens
- **Keyboard-first** — `Enter` submits the form from any field

---

##  Repository Blueprint

```
caps-registration-desk/
│
├── registration-desk.html          # ← The entire application (single file)
│   ├── <head>
│   │   ├── EmailJS CDN script      # Browser-side email API (no backend needed)
│   │   └── <style>                 # All CSS — variables, layout, components
│   │       ├── CSS custom properties (:root)
│   │       ├── Dark mode overrides (@media prefers-color-scheme: dark)
│   │       ├── Header & stat chips
│   │       ├── Two-column grid layout (.main)
│   │       ├── Registration form & inputs
│   │       ├── Register button + spinner
│   │       ├── Feedback banners (success / warning / error)
│   │       └── Live log panel & avatars
│   │
│   └── <body>
│       ├── <header>                # Sticky top bar — CAPS logo, live dot, stat chips
│       ├── <main class="main">     # Two-column grid
│       │   ├── Left column         # Setup banner + registration card
│       │   │   ├── Setup banner    # Shown when API keys are placeholders
│       │   │   └── Registration form
│       │   │       ├── First name / Last name (grid)
│       │   │       ├── College email
│       │   │       ├── Student ID / Department (grid)
│       │   │       ├── Register button (+ spinner)
│       │   │       └── Feedback banner
│       │   └── Right column        # Live registration log
│       │       ├── Clear button
│       │       └── Scrollable log list (avatars, name, ID, time, DUP tag)
│       └── <script>
│           ├── CONFIG block        # EMAILJS_PUBLIC_KEY, SERVICE_ID, TEMPLATE_ID
│           ├── EVENT constants     # EVENT_NAME, WIFI_PASSWORD
│           ├── State management    # Map + sessionStorage persistence
│           ├── sendEmail()         # EmailJS API call / demo mode fallback
│           ├── handleRegister()    # Validation → duplicate check → send → log
│           └── UI helpers
│               ├── v()             # Input value getter
│               ├── nowStr()        # Locale time string
│               ├── updateStats()   # Refresh header counters
│               ├── showFeedback()  # Render success / warning / error banner
│               ├── addToLog()      # Prepend entry to live log
│               ├── setLoading()    # Toggle button disabled + spinner
│               ├── clearFields()   # Reset form, re-focus first name
│               ├── clearLog()      # Wipe session + reset UI
│               └── restoreLog()    # Re-render log on page refresh
│
├── README.md                       # ← This file
└── .gitignore                      # (optional) exclude OS files
```

---

##  Tech Stack

| Layer | Technology | Reason |
|---|---|---|
| **Structure** | HTML5 | Semantic, universally supported |
| **Styling** | Vanilla CSS (custom properties) | No build step; dark mode via `prefers-color-scheme` |
| **Logic** | Vanilla JavaScript (ES2020) | No framework overhead; `async/await` for email calls |
| **Email API** | [EmailJS](https://www.emailjs.com/) (CDN) | Browser-side transactional email — no backend required |
| **Persistence** | `sessionStorage` | Survives refreshes; auto-clears when tab closes |
| **Runtime** | Any modern browser (Chrome recommended) | Zero installation for the volunteer |

---

##  Prerequisites

Before going live you need a free [EmailJS](https://www.emailjs.com/) account and three credentials:

| Credential | Where to find it |
|---|---|
| **Public Key** | EmailJS Dashboard → Account → API Keys |
| **Service ID** | EmailJS Dashboard → Email Services → your connected service |
| **Template ID** | EmailJS Dashboard → Email Templates → your template |

### EmailJS Template Variables

Your template must include these exact variables:

```
{{to_name}}    — recipient's first name
{{to_email}}   — recipient's email address
{{wifi_pass}}  — the WiFi password
{{event_name}} — name of the event
```

---

##  Getting Started

### Step 1 — Clone the repository

```bash
git clone https://github.com/<your-username>/caps-registration-desk.git
cd caps-registration-desk
```

Or download the ZIP directly from GitHub:

```
Code → Download ZIP → Extract → open the folder
```

### Step 2 — Open the HTML file

No npm, no build, no server needed. Just open the file:

```bash
# macOS
open registration-desk.html

# Windows
start registration-desk.html

# Linux
xdg-open registration-desk.html
```

Or drag and drop the file into Chrome / Edge / Firefox.

### Step 3 — Fill in your EmailJS credentials

Open `registration-desk.html` in any text editor and locate the **CONFIG block** near the bottom of the file (search for `CONFIG`):

```js
/* ============================================================
   CONFIG — fill these in before going live
   ============================================================ */
const EMAILJS_PUBLIC_KEY  = 'YOUR_PUBLIC_KEY';    // e.g. 'abc123XYZ'
const EMAILJS_SERVICE_ID  = 'YOUR_SERVICE_ID';    // e.g. 'service_xxxxx'
const EMAILJS_TEMPLATE_ID = 'YOUR_TEMPLATE_ID';   // e.g. 'template_xxxxx'
```

Replace the placeholder strings with your actual credentials. Save the file.

### Step 4 — Set your event details

Just below the credentials, update the event constants:

```js
const EVENT_NAME    = 'CAPS BYC Tech Fest 2025';   // Shown in the email
const WIFI_PASSWORD = 'CAPS@BYC2025';               // Sent to each registrant
```

### Step 5 — Create your EmailJS template

In the EmailJS dashboard, create a new template. A sample body:

```
Hi {{to_name}},

Welcome to {{event_name}}! 🎉

Here's your WiFi access for today:
Password: {{wifi_pass}}

See you inside!
— The CAPS Team
```

Set the **To Email** field to `{{to_email}}`.

### Step 6 — Test in demo mode (optional)

Before deploying at the desk, you can test without live credentials. Leave the keys as placeholders — the system enters **Demo Mode** automatically, simulating a 900 ms network delay and logging output to the browser console (`F12 → Console`).

### Step 7 — Go live at the event

Open the file in Chrome on the event laptop. The blue setup banner disappears once real credentials are detected. The desk is ready.

---

##  How It Works

```
Volunteer fills form → Submit / Enter key
         │
         ▼
   Validate fields (all required + email format)
         │
         ▼
   Duplicate check (Student ID → sessionStorage Map)
    ├─ Duplicate → amber warning, log entry tagged DUP, no email sent
    └─ New student ↓
         │
         ▼
   sendEmail() — EmailJS API call
    ├─ Configured → live email to student
    └─ Demo mode  → 900 ms simulated delay, console.log
         │
         ▼
   Update state (Map + sessionStorage)
   Update header counters
   Append to live log (with initials avatar)
   Show green success banner
   Clear form, re-focus first name field
```

---

##  UI Color Language

| Color | Meaning |
|---|---|
|  Green | Successful registration — email sent |
|  Amber | Duplicate Student ID — email not re-sent |
|  Red | Validation error or EmailJS delivery failure |

---

##  Departments Supported

Out of the box the department dropdown includes:

- Computer Science
- Electronics & Communication
- Mechanical Engineering
- Civil Engineering
- MBA / BBA / BCA
- Economics, Psychology, English, Commerce
- Law
- Other

To add or remove departments, edit the `<select id="dept">` options in the HTML form section.

---

##  Security Notes

- **Never commit real API keys** to a public repository. The credentials in the CONFIG block are specific to an EmailJS free-tier account and should be rotated or restricted post-event.
- EmailJS public keys are browser-exposed by design (similar to Firebase config) but you can whitelist your domain in the EmailJS dashboard under **Account → Security**.
- `sessionStorage` is tab-scoped and is wiped when the browser tab closes — no student data persists beyond the session.

---

##  Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Setup banner still showing | `isConfigured` check failing | Ensure all three constants are non-empty strings |
| Emails not arriving | Wrong Service ID or Template ID | Re-check each key in the EmailJS dashboard |
| Silent failures on first submission | Public Key mismatch | Confirm the key under Account → API Keys, not the Service key |
| `emailjs is not defined` | CDN blocked | Check internet connection; try a different network |
| Registrations lost on refresh | `sessionStorage` cleared | Expected if the tab was closed; data is session-scoped |

---

##  Contributing

Pull requests are welcome for improvements such as:

- CSV export of the session log
- Additional department options
- Configurable WiFi password from the UI
- Multi-event support

Please open an issue first to discuss significant changes.

---

##  License

This project is licensed under the MIT License.

---

##  Author

**Dyan Reji Mathew**  
CAPS Committee — Tech Developer Position  
Christ University, Bengaluru
