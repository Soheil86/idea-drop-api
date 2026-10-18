# 💡 IdeaDrop API

A secure REST API for **IdeaDrop**, a full-stack app where people share ideas, browse what others have posted, and manage their own ideas.

I built this backend from scratch with **Node.js, Express and MongoDB**. It includes **JWT authentication with refresh tokens**, ownership-based permissions and central error handling, and it is deployed to production on **Render**.

**Live API:** https://idea-drop-api-2tjy.onrender.com/api/ideas
**Live App:** https://idea-drop-ui-two.vercel.app
**Frontend code:** [idea-drop-ui](https://github.com/Soheil86/idea-drop-ui)

> ⏳ The API runs on Render's free plan, which sleeps when it's not in use. The first request can take up to a minute while it wakes up.

---

## ✨ Highlights

- **Secure authentication.** Short-lived access tokens with refresh tokens stored in **HTTP-only cookies**, which JavaScript in the browser can't read. This protects them from XSS attacks.
- **Silent session refresh.** A `/refresh` endpoint issues new access tokens, so users stay logged in without re-entering their password.
- **Password hashing.** Passwords are hashed with **bcrypt** before they reach the database. Plain-text passwords are never stored.
- **Ownership checks.** Only the author of an idea can edit or delete it. Anyone else gets `403 Forbidden`.
- **Input validation.** Missing fields, invalid IDs and duplicate emails return clear error messages with the right HTTP status codes.
- **Central error handling.** One error-handling middleware returns consistent JSON errors and hides stack traces in production.
- **Production-ready settings.** CORS limited to known frontends, cookies that adapt to the environment (`secure` and `sameSite`), and secrets kept in environment variables.

---

## 🛠 Tech Stack

| Area | Technology |
|---|---|
| Runtime | Node.js (ES Modules) |
| Framework | Express 5 |
| Database | MongoDB Atlas + Mongoose |
| Authentication | JWT ([jose](https://github.com/panva/jose)), bcryptjs, HTTP-only cookies |
| Security | CORS with an origin allowlist, cookie-parser |
| Deployment | Render (API), Vercel (frontend) |

The frontend is built with **React 19, TypeScript, TanStack Router, TanStack Query, Axios and Tailwind CSS**.

---

## 📡 API Endpoints

### Auth: `/api/auth`

| Method | Endpoint | Description | Access |
|---|---|---|---|
| POST | `/register` | Create an account | Public |
| POST | `/login` | Log in and receive an access token | Public |
| POST | `/refresh` | Get a new access token using the refresh cookie | Refresh cookie |
| POST | `/logout` | Clear the refresh token | Public |

### Ideas: `/api/ideas`

| Method | Endpoint | Description | Access |
|---|---|---|---|
| GET | `/` | Get all ideas (optional `?_limit=3`) | Public |
| GET | `/:id` | Get a single idea | Public |
| POST | `/` | Create an idea | 🔒 Logged in |
| PUT | `/:id` | Update an idea | 🔒 Owner only |
| DELETE | `/:id` | Delete an idea | 🔒 Owner only |

Protected routes expect this header: `Authorization: Bearer <accessToken>`

---

## 🔐 How Authentication Works

1. When a user **registers or logs in**, the API returns a short-lived **access token** and sets a long-lived **refresh token** in an HTTP-only cookie.
2. The frontend sends the access token with every protected request.
3. When the access token expires, the frontend calls `/api/auth/refresh`. The browser sends the cookie automatically, and the API returns a new access token.
4. **Logout** clears the cookie, which ends the session.

This keeps the risk low if an access token leaks, while users still get a smooth "stay logged in" experience.

---

## 📁 Project Structure

```
idea-drop-api/
├── config/         # MongoDB connection
├── middleware/     # Auth protection and error handling
├── models/         # Mongoose schemas (User, Idea)
├── routes/         # Auth and idea endpoints
├── utils/          # JWT helpers
└── server.js       # App entry point
```

---

## 🚀 Run It Locally

```bash
git clone https://github.com/Soheil86/idea-drop-api.git
cd idea-drop-api
npm install
```

Create a `.env` file in the project root:

```env
PORT=8000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_secret_key
```

Start the server:

```bash
npm run dev   # restarts automatically when files change
# or
npm start
```

The API will run at `http://localhost:8000`.

---

## 👋 About Me

I'm **Soheil Ghanbari**, a developer who enjoys building complete products, from the database and API to the user interface. This project shows how I approach real-world concerns like security, clean structure and deployment.

I'm **open to new opportunities**. If you like what you see, feel free to check out my other work on [GitHub](https://github.com/Soheil86) or get in touch!
