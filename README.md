🛟 Universal Support System

A self-hosted, multi-website customer-support platform with:

- 🤖 Streaming AI support
- 👤 Human-agent transfers
- 💬 Real-time WebSocket chat
- 🌐 Website/page context
- 🏢 Multiple websites from one server
- ⚙️ "config.txt" website configuration
- 💾 SQLite conversation storage
- 🔐 Protected support dashboard
- 🍪 Agent sessions
- 🚦 Basic rate limiting
- 🔄 AI ↔ human handoff
- 📦 Simple website integration

The goal is simple:

«Website creators paste one script tag. The support team uses a separate dashboard.»

---

📁 Project Structure

support-server/
│
├── server.py
├── config.txt
├── requirements.txt
├── widget.js
├── support.db
│
└── dashboard/
    └── index.html

"support.db" is created automatically when the server starts.

---

🧩 How It Works

┌──────────────────────────┐
│      Customer Website   │
│                          │
│  <script ...>            │
│                          │
│  💬 Support Widget       │
└────────────┬─────────────┘
             │
             │ HTTPS / WSS
             ▼
┌──────────────────────────┐
│      Support Server      │
│                          │
│  FastAPI                 │
│  WebSockets              │
│  SQLite                  │
│  AI API                  │
│  Authentication          │
│  config.txt              │
└────────────┬─────────────┘
             │
             │ Authenticated
             ▼
┌──────────────────────────┐
│     Support Dashboard    │
│                          │
│  👤 Agents               │
│  💬 Conversations        │
│  🔴 Transfer requests    │
│  🤖 AI status            │
└──────────────────────────┘

---

🚀 Installation

Requirements

You need:

- Python 3
- Internet access
- A server or computer capable of running Python
- An AI API endpoint compatible with the Anthropic Messages API
- HTTPS for a public production deployment

The system can be hosted on many different environments, including:

- VPS
- Oracle Cloud
- Replit-style environments
- Raspberry Pi
- Linux server
- Termux
- Home server
- Other Python-capable hosting

---

📦 Install Dependencies

From the project directory:

pip install -r requirements.txt

The required packages are:

fastapi
uvicorn[standard]
httpx

---

⚙️ Configuration

The main configuration file is:

config.txt

It contains:

- Server settings
- Agent credentials
- AI configuration
- Website definitions

---

🔐 Server Configuration

Example:

HOST=0.0.0.0
PORT=8000

SERVER_SECRET=CHANGE_THIS_TO_A_LONG_RANDOM_SECRET

AGENT_USERNAME=admin
AGENT_PASSWORD=CHANGE_THIS_AGENT_PASSWORD

"SERVER_SECRET"

This is used to protect authentication session tokens.

Use a long random value.

For example, you can generate one with Python:

python -c "import secrets; print(secrets.token_urlsafe(48))"

Do not publish the resulting value.

---

👤 Agent Login

The dashboard credentials are configured here:

AGENT_USERNAME=admin
AGENT_PASSWORD=CHANGE_THIS_AGENT_PASSWORD

Change the password before deploying.

The password is only used by the server.

It is not included in "widget.js".

---

🤖 AI Configuration

Example:

AI_URL=https://api.openapis.online/anthropic/v1/messages
AI_KEY=admin
AI_MODEL=claude-opus-4-7
AI_MAX_TOKENS=1024

The important security rule is:

AI_KEY
   ↓
server.py
   ↓
AI API

Never:

AI_KEY
   ↓
widget.js
   ↓
customer browser

The browser should never receive the AI API key.

---

🏢 Adding Websites

Websites are defined using sections beginning with:

[SITE:site_id]

For example:

[SITE:store]

NAME=My Store
DOMAIN=https://example.com
WIDGET_NAME=Customer Support
GREETING=Hi! 👋 How can I help you today?

Another website can be added:

[SITE:gaming]

NAME=Gaming Website
DOMAIN=https://game.example.com
WIDGET_NAME=Gaming Support
GREETING=Hey! 🎮 What can I help you with?

And another:

[SITE:school]

NAME=School Website
DOMAIN=https://school.example.com
WIDGET_NAME=School Support
GREETING=Hi! How can we help?

There is no need to create a separate server for every website.

---

🆔 Site IDs

The value after:

[SITE:

is the site's ID.

For example:

[SITE:store]

means:

site_id = store

The website uses that ID when loading the widget.

---

🌐 Installing the Widget

A website owner only needs to add one script.

For the "store" website:

<script
    src="https://YOUR-SUPPORT-SERVER.com/widget.js"
    data-site-id="store">
</script>

Replace:

https://YOUR-SUPPORT-SERVER.com

with the actual support server address.

---

💬 Customer Experience

The widget appears as a floating button:

                       ┌──────────────┐
                       │              │
                       │   Website    │
                       │              │
                       │              │
                       │          💬  │
                       └──────────────┘

Clicking it opens the support window.

The customer can then talk normally.

Example:

Customer:
How do I reset my password?

AI:
You can reset your password from
the account settings page...

---

🌐 Website Context

The widget automatically sends information about the current page.

Examples include:

Page URL
Page title
Visible page text

This lets the AI answer questions about the page the customer is currently viewing.

For example:

Customer:
What does this page do?

The AI can use the page context to explain the page.

---

👤 Human Support Transfers

Customers don't need to know a special command.

They can naturally say:

Can I talk to a human?

or:

I need an agent.

or:

Can you transfer me to someone?

The server recognizes common human-support requests.

The conversation changes to:

AI
 ↓
Waiting for agent
 ↓
Human agent

---

🔴 Transfer Requests

When a transfer happens, the dashboard receives a notification.

The conversation is marked:

waiting

The support agent can then open it.

---

👤 Accepting a Conversation

When an agent clicks:

👤 Accept transfer

the conversation changes to:

human

The AI stops handling new messages.

The customer sees:

Hi! I'm a human support agent.
I can take it from here.

---

💬 Human Chat

After an agent accepts the conversation:

Customer
   │
   ▼
Support Server
   │
   ▼
Human Agent

Messages are delivered through WebSockets.

This means the conversation can happen in real time without constantly refreshing the page.

---

🤖 Returning to AI

The dashboard contains:

🤖 Return to AI

When selected, the conversation changes back to:

ai

The AI can continue handling the conversation.

---

🗃️ Database

The system automatically creates:

support.db

SQLite stores:

- Conversations
- Messages
- Website IDs
- Conversation status
- Page information
- Timestamps
- Agent sessions

---

📊 Conversation Statuses

A conversation can have several states.

"ai"

The AI is currently handling the conversation.

Customer → AI

"waiting"

The customer has requested human support.

Customer
   ↓
Waiting for agent

"human"

A support agent has accepted the conversation.

Customer ↔ Human

---

🔐 Authentication

The dashboard is protected by a login screen.

The flow is:

Agent
 ↓
Username + password
 ↓
/api/login
 ↓
Secure session cookie
 ↓
Dashboard

Protected API endpoints verify the session before returning support information.

---

🍪 Sessions

The server creates a random session token.

Only a hash of the token is stored in the database.

The browser receives the session as an HTTP-only cookie.

This prevents normal JavaScript running on the dashboard from reading the session cookie.

Sessions expire automatically.

---

🔒 HTTPS

For public deployment, use HTTPS.

The production architecture should look like:

Customer Browser
       │
       │ HTTPS
       ▼
Reverse Proxy
       │
       │ HTTP/WSS
       ▼
FastAPI

Examples of reverse proxies include:

Nginx
Caddy
Cloudflare

The exact setup depends on the hosting provider.

---

⚠️ Local HTTP Testing

The login cookie in "server.py" uses:

secure=True

This is appropriate for HTTPS.

If you are testing locally with:

http://127.0.0.1:8000

the secure cookie will not behave like a normal production HTTPS cookie.

For local-only testing, you can temporarily change:

secure=True

to:

secure=False

Do not use the insecure setting for a public deployment.

---

🛡️ Rate Limiting

The server includes a basic message rate limiter.

A conversation cannot send messages continuously without a short delay.

This helps reduce accidental message spam.

For a large production deployment, use a more robust rate limiter backed by something such as Redis.

---

🔑 Important Security Rules

Never put secrets in "widget.js"

Do not do this:

const API_KEY = "my-secret-key";

The browser can see it.

Instead:

Browser
   ↓
Support Server
   ↓
AI API

---

Protect "config.txt"

Do not serve:

/config.txt

from the web server.

Do not put it inside a public website directory.

---

Protect "support.db"

Do not make:

support.db

directly downloadable over HTTP.

---

Use HTTPS

Public deployment should use:

https://

and:

wss://

rather than plain HTTP/WS.

---

▶️ Starting the Server

The simplest method is:

python server.py

The server listens using the configured:

HOST=0.0.0.0
PORT=8000

You can also run it directly through Uvicorn:

python -m uvicorn server:app --host 0.0.0.0 --port 8000

---

🧪 Testing

After starting the server, open:

http://YOUR-SERVER:8000/

You should see the support login page.

Log in using:

AGENT_USERNAME
AGENT_PASSWORD

Then create a small HTML test page:

<!DOCTYPE html>

<html>

<head>
    <title>Support Test</title>
</head>

<body>

<h1>My Test Website</h1>

<p>
This is a test page for the support system.
</p>

<script
    src="http://YOUR-SERVER:8000/widget.js"
    data-site-id="store">
</script>

</body>

</html>

Open the page and click:

💬

---

🧪 Testing Human Transfer

Open the widget and type:

Can you transfer me to a human?

The conversation should become:

waiting

The dashboard should display the transfer request.

Click:

👤 Accept transfer

The conversation becomes:

human

---

🧠 AI Streaming

The AI endpoint is requested with:

{
    "stream": true
}

The server receives the streaming response and forwards generated text to the customer through the WebSocket.

The customer therefore sees the response appear progressively rather than waiting for the entire response.

---

🔌 API Endpoints

Public

Get website configuration

GET /api/site/{site_id}

Example:

GET /api/site/store

---

Create conversation

POST /api/conversation

Example request:

{
    "site_id": "store",
    "page_url": "https://example.com/shop",
    "page_title": "Shop"
}

---

Customer WebSocket

/ws/customer/{conversation_id}

---

Widget

GET /widget.js

---

🔐 Protected Endpoints

These require an authenticated agent session.

Current agent

GET /api/me

Login

POST /api/login

Logout

POST /api/logout

Conversations

GET /api/conversations

Individual conversation

GET /api/conversation/{conversation_id}

Agent WebSocket

/ws/agent

---

🏢 Adding Another Website

Suppose you want to add:

https://my-new-site.com

Add:

[SITE:newsite]

NAME=My New Site
DOMAIN=https://my-new-site.com
WIDGET_NAME=My New Support
GREETING=Hello! How can we help?

Then add this to the website:

<script
    src="https://YOUR-SUPPORT-SERVER.com/widget.js"
    data-site-id="newsite">
</script>

That's it.

---

🔄 Multi-Site Architecture

One server can handle:

store
gaming
school
newsite
...

All conversations remain associated with their site ID.

The dashboard can therefore distinguish:

store
gaming
school

without needing separate servers.

---

🗂️ Recommended Production Layout

A production server can look like:

/opt/support-server/

├── server.py
├── config.txt
├── requirements.txt
├── widget.js
├── support.db
│
└── dashboard/
    └── index.html

Restrict filesystem permissions so ordinary users cannot read the configuration file.

---

💾 Backups

The most important persistent file is:

support.db

Back it up regularly.

For example:

cp support.db support-backup.db

For a real deployment, use an automated backup system instead of relying on manual copies.

---

🧯 Troubleshooting

"config.txt is missing"

Make sure:

server.py

and:

config.txt

are in the same directory.

---

"Change SERVER_SECRET"

You haven't replaced:

SERVER_SECRET=CHANGE_THIS_TO_A_LONG_RANDOM_SECRET

Generate a new random secret and put it in the configuration.

---

"Change AGENT_PASSWORD"

Replace:

AGENT_PASSWORD=CHANGE_THIS_AGENT_PASSWORD

with your real password.

---

Widget says support is unavailable

Check:

1. The server is running.
2. The "site_id" exists.
3. The website can reach the server.
4. The server URL is correct.
5. Browser developer-console errors.
6. HTTPS/WSS configuration.

---

Dashboard login doesn't work

Check:

AGENT_USERNAME=
AGENT_PASSWORD=

Then restart the server.

---

AI doesn't respond

Check:

AI_URL=
AI_KEY=
AI_MODEL=

Also check the server's terminal output for an AI API error.

---

WebSocket connection fails

If using HTTPS, the widget automatically uses:

wss://

If using HTTP locally, it uses:

ws://

A reverse proxy must be configured to support WebSocket connections when deploying behind one.

---

🧱 Production Improvements

The included system is a solid small deployment, but larger deployments should consider:

- Redis for distributed WebSocket state
- PostgreSQL instead of SQLite
- Proper multi-agent accounts
- Role-based permissions
- Password hashing with a dedicated password-hashing library
- CSRF protection where applicable
- Stronger rate limiting
- Conversation search
- Agent presence
- Unread-message counters
- File attachments
- Email notifications
- Push notifications
- Audit logs
- Automatic backups
- Monitoring
- Error tracking
- Reverse proxy
- TLS certificates
- Domain-based site verification

---

🔒 Privacy

The system receives information such as:

Page URL
Page title
Visible page text
Conversation messages
Browser language

Only collect information that is actually necessary for support.

Avoid sending sensitive information from the page to the AI unless the website and its users are comfortable with that handling.

The visible-page-text feature should especially be considered carefully on pages containing private account information.

---

📜 License

No license is specified by this project.

If you distribute or deploy this project publicly, add a license that matches how you want others to use it.

---

🎯 Quick Start

1. Create project directory
        ↓
2. Add server.py
        ↓
3. Add config.txt
        ↓
4. Add requirements.txt
        ↓
5. Add widget.js
        ↓
6. Add dashboard/index.html
        ↓
7. pip install -r requirements.txt
        ↓
8. Change secrets/passwords
        ↓
9. python server.py
        ↓
10. Put the widget script on a website
        ↓
11. Open the support dashboard
        ↓
12. Start supporting customers 🚀

---

🌟 Example Website Integration

The entire customer website integration can be:

<script
    src="https://support.example.com/widget.js"
    data-site-id="store">
</script>

That's the main idea behind the system:

«One support server. Multiple websites. One dashboard. AI until a human is needed.»
