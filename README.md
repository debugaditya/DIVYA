# DIVYA - Discreet Identity Vault for Your Anonymity

An anonymous messaging web application built with Node.js, Express, MongoDB, EJS, HTML, CSS, and vanilla JavaScript.

DIVYA allows people to send messages to a username without revealing the sender to the recipient.

**Live Demo:** https://divya-cu7a.onrender.com

---

## 1. Overview

DIVYA is a lightweight server-rendered anonymous messaging application.

The main idea is simple:

```text
Anonymous User
      |
      v
Enter Recipient Username
      |
      v
Write Message
      |
      v
POST /index
      |
      v
MongoDB
      |
      v
Recipient's Message Box
```

Recipients can sign in to view messages addressed to their username.

---

# 2. Features

- Anonymous messaging
- Username-based recipients
- User signup
- User signin
- Persistent message storage
- Message history
- EJS server-rendered message box
- Seen/unseen message state
- Responsive interface
- Dark-themed UI
- Message preview cards
- Password visibility toggle
- MongoDB-backed storage
- Render-compatible deployment configuration

---

# 3. Technology Stack

| Layer | Technology |
|---|---|
| Runtime | Node.js |
| Backend | Express.js |
| Database | MongoDB |
| Templating | EJS |
| Frontend | HTML / CSS / JavaScript |
| Configuration | dotenv |
| CORS | cors |
| Deployment | Render |

---

# 4. Architecture

```text
                    +----------------+
                    |     Browser    |
                    +-------+--------+
                            |
                            v
                    +----------------+
                    |    Express     |
                    |     Server     |
                    +-------+--------+
                            |
                 +----------+----------+
                 |                     |
                 v                     v
             API Routes            EJS Views
                 |                     |
                 v                     v
              MongoDB          Rendered HTML
```

The Express server provides both:

- API endpoints
- Static HTML/CSS/JavaScript
- EJS-rendered message pages

---

# 5. User Flow

## Anonymous Sender

```text
Open DIVYA
    |
    v
Enter recipient username
    |
    v
Enter message
    |
    v
POST /index
    |
    v
Verify recipient exists
    |
    v
Insert message into MongoDB
```

The recipient only sees:

```text
ANONYMOUS USER
```

rather than the sender identity.

---

# 6. Recipient Flow

A recipient can:

1. Create an account.
2. Sign in.
3. Open the message box.
4. View messages sent to their username.

```text
Sign In
   |
   v
/messagebox?username=<username>
   |
   v
MongoDB
   |
   v
Messages sorted by timestamp
   |
   v
EJS rendering
```

---

# 7. Authentication

The application provides:

```text
/signup
/signin
```

## Signup

The backend checks whether the username already exists.

If not, a record is created in:

```text
DIVYA.CREDENTIALS
```

with:

```text
username
password
```

A welcome message is also inserted into the messages collection.

## Signin

The backend checks the submitted username and password against the stored credential document.

On successful login, the frontend stores the username in:

```text
localStorage
```

and redirects to the user's message box.

---

# 8. Anonymous Message Flow

The anonymous messaging endpoint is:

```text
POST /index
```

The request contains:

```json
{
  "username": "recipient",
  "message": "Hello!"
}
```

The backend:

1. Validates the username and message.
2. Checks that the recipient exists.
3. Inserts a message document.
4. Returns a success response.

Stored messages have the structure:

```text
username
message
timestamp
seen
```

---

# 9. Message Storage

Messages are stored in:

```text
DIVYA.MESSAGES
```

Example:

```json
{
  "username": "aditya",
  "message": "Hello!",
  "timestamp": "2026-09-20T00:00:00.000Z",
  "seen": false
}
```

Messages belonging to a username are fetched using:

```text
find({ username })
```

and sorted using:

```text
timestamp: -1
```

so the newest messages appear first.

---

# 10. Message Box

The recipient message page is rendered using EJS.

The view:

```text
view/messagebox.ejs
```

renders every message as:

```text
ANONYMOUS USER
----------------
message content
```

Clicking a message opens a larger message card.

The card can be dismissed by clicking outside it or clicking the card itself.

---

# 11. Frontend Structure

The application uses separate HTML, CSS, and JavaScript files for the major flows.

```text
DIVYA/
│
├── index.html
├── signup.html
├── signin.html
│
├── script.js
├── signup_script.js
├── signin_script.js
│
├── styles.css
├── signup_style.css
├── signin_style.css
│
├── view/
│   └── messagebox.ejs
│
├── app.js
├── package.json
└── README.md
```

---

# 12. Express Server

The main backend entry point is:

```text
app.js
```

The server:

- Loads environment variables
- Enables CORS
- Parses JSON requests
- Parses URL-encoded requests
- Serves static files
- Configures EJS
- Connects to MongoDB
- Registers application routes
- Starts the HTTP server

The port is taken from:

```text
process.env.PORT
```

with a local fallback of:

```text
3000
```

---

# 13. Static File Serving

The repository uses a flat project structure.

Express serves:

```text
HTML
CSS
JavaScript
Images
```

directly from the project root.

Explicit routes are provided for:

```text
/
 /signup
 /signin
 /signup.html
 /signin.html
```

The EJS views are loaded from:

```text
view/
```

---

# 14. MongoDB Connection

MongoDB is initialized through the environment variable:

```text
MONGO_KEY
```

The server creates a MongoDB client using the official Node.js MongoDB driver.

The connection is verified with a database ping before the Express server starts accepting requests.

The primary application database is:

```text
DIVYA
```

---

# 15. API Endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/signup` | Create account |
| POST | `/signin` | Authenticate user |
| POST | `/index` | Send anonymous message |
| GET | `/messagebox` | Render recipient's messages |
| GET | `/` | Serve main page |
| GET | `/signup` | Serve signup page |
| GET | `/signin` | Serve signin page |

---

# 16. Signup Flow

```text
POST /signup
      |
      v
Validate username/password
      |
      v
Check CREDENTIALS
      |
      +---- User exists ----> 409
      |
      v
Insert user
      |
      v
Insert welcome message
      |
      v
Success
```

---

# 17. Signin Flow

```text
POST /signin
      |
      v
Validate credentials
      |
      v
Find user in MongoDB
      |
      v
Compare submitted credentials
      |
      +---- Invalid ----> 401
      |
      v
Success
      |
      v
localStorage.username
      |
      v
Message Box
```

---

# 18. Message Box Rendering

The `/messagebox` endpoint takes:

```text
?username=<username>
```

It queries:

```text
MESSAGES
```

for the specified username and sorts records by newest timestamp first.

The resulting data is passed into:

```text
messagebox.ejs
```

for server-side rendering.

---

# 19. UI Design

The message box uses a dark interface with:

- Dark GitHub-inspired background
- Green accent color
- Responsive layout
- Message cards
- Full-screen message preview
- Mobile-specific layout adjustments
- Hover interactions

The application uses the Inter font for the message UI.

---

# 20. Deployment

The application is compatible with Render.

The server uses:

```javascript
process.env.PORT || 3000
```

which allows Render to inject its assigned port.

Live application:

```text
https://divya-cu7a.onrender.com
```

---

# 21. Local Setup

## Requirements

- Node.js
- MongoDB
- npm

Clone the repository:

```bash
git clone https://github.com/debugaditya/DIVYA.git
cd DIVYA
```

Install dependencies:

```bash
npm install
```

Create environment configuration:

```text
MONGO_KEY=your_mongodb_connection_string
PORT=3000
```

Run:

```bash
node app.js
```

Open:

```text
http://localhost:3000
```

---

# 22. Database Structure

```text
DIVYA
│
├── CREDENTIALS
│   ├── username
│   └── password
│
└── MESSAGES
    ├── username
    ├── message
    ├── timestamp
    └── seen
```

---

# 23. Current Security Considerations

This repository is a lightweight anonymous messaging project rather than a hardened production authentication system.

The current implementation stores credentials directly in MongoDB and does not implement a server-side session/token system.

For a production deployment, the authentication layer should be strengthened with:

- Password hashing
- Secure sessions
- HTTP-only cookies
- CSRF protection
- Rate limiting
- Input validation
- Request size limits
- Message abuse prevention
- Username authorization
- Stronger MongoDB validation

These are future hardening opportunities rather than claims about the current implementation.

---

# 24. Project Goals

DIVYA was built to explore:

```text
Node.js
    +
Express
    +
MongoDB
    +
EJS
    +
REST APIs
    +
Anonymous Messaging
    +
Server-Side Rendering
```

The project focuses on implementing a small end-to-end web application where a Node.js backend handles authentication, persistence, message delivery, and server-rendered message history.

---

# 25. Conclusion

DIVYA is a compact anonymous messaging application built with a traditional Node.js + Express architecture.

It demonstrates:

- User registration
- User authentication
- Anonymous message submission
- MongoDB persistence
- Server-side EJS rendering
- Static file serving
- Responsive frontend UI
- Message history
- Seen-state tracking
- Render deployment compatibility

The project provides a foundation for extending anonymous messaging with stronger authentication, message expiration, moderation, abuse detection, notifications, rate limiting, and richer media support.
