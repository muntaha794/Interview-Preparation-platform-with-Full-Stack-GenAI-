# AI Interview Preparation Platform

An AI-powered interview preparation web application that analyzes a candidate's resume against a target job description and creates a personalized interview preparation report. Users can register, sign in, upload a resume PDF, review suggested interview questions and answers, identify skill gaps, follow a preparation roadmap, and generate a job-tailored resume PDF.

## Features

- **Authentication:** Register, log in, and retrieve the current user's profile using JWT-based authentication.
- **Resume analysis:** Upload a PDF resume for text extraction.
- **Job-specific preparation:** Analyze resume content alongside a job description and the candidate's self-description.
- **AI-generated interview report:** Generate a match score, technical and behavioral questions, suggested answers, skill gaps, and a preparation plan.
- **Saved reports:** Store interview reports in MongoDB and retrieve a user's reports later.
- **Tailored resume PDF:** Generate a resume tailored to the selected job using AI-generated content and Puppeteer.
- **Structured AI output:** Use Zod schemas and JSON-schema conversion to help constrain model responses.

## Tech Stack

### Frontend
- React 19
- Vite
- React Router
- Axios
- Sass

### Backend
- Node.js
- Express 5
- MongoDB and Mongoose
- Google Gemini via `@google/genai`
- `pdf-parse` for PDF text extraction
- Multer for file uploads
- `bcryptjs` for password hashing
- JSON Web Tokens (`jsonwebtoken`)
- Puppeteer for PDF rendering
- Zod for output validation

## Architecture

```text
React + Vite frontend
        |
        | HTTP requests (cookies / credentials)
        v
Express API
  ├── /api/auth       Authentication and user profile
  └── /api/interview  Resume analysis and interview reports
        |
        ├── MongoDB     Users, reports, token blacklist
        ├── Gemini API  Interview report and resume content
        ├── pdf-parse   Resume PDF text extraction
        └── Puppeteer   Tailored resume PDF generation
```

## Project Structure

```text
fullstack+genAI/
├── Backend/
│   ├── server.js
│   ├── package.json
│   └── src/
│       ├── app.js
│       ├── config/
│       │   └── database.js
│       ├── controllers/
│       │   ├── auth.controller.js
│       │   └── interview.controller.js
│       ├── middlewares/
│       │   ├── auth.middleware.js
│       │   └── file.middleware.js
│       ├── models/
│       │   ├── blacklist.model.js
│       │   ├── interviewReport.model.js
│       │   └── user.model.js
│       ├── routes/
│       │   ├── auth.routes.js
│       │   └── interview.routes.js
│       └── services/
│           └── ai.service.js
└── Frontend/
    ├── package.json
    └── src/
        ├── features/
        │   ├── auth/
        │   └── interview/
        └── ...
```

## Prerequisites

Install the following before running the project:

- Node.js (use a currently supported LTS release)
- npm
- MongoDB database, either local or hosted (for example, MongoDB Atlas)
- Google Gemini API key

Puppeteer may download a compatible browser during installation. Follow Puppeteer's official troubleshooting guidance if your operating system requires additional system libraries.

## Setup

### 1. Clone or download the project

If using Git:

```bash
git clone <your-repository-url>
cd fullstack+genAI
```

If you downloaded the ZIP, extract it and open a terminal in the extracted `fullstack+genAI` directory.

### 2. Configure the backend

```bash
cd Backend
npm install
```

Create a `.env` file inside `Backend/`:

```env
MONGO_URI=mongodb://127.0.0.1:27017/ai-interview-preparation
GOOGLE_GENAI_API_KEY=your_gemini_api_key
```

Replace the MongoDB URI and Gemini key with your own values. Never commit `.env` or expose API keys in frontend code.

### 3. Start the backend

From the `Backend/` directory:

```bash
npm run dev
```

The current backend code listens on **port 3000**. The database connection should be available before testing API requests.

### 4. Configure and start the frontend

Open a second terminal:

```bash
cd Frontend
npm install
npm run dev
```

Vite normally serves the frontend at `http://localhost:5173`. The backend CORS configuration currently allows that origin and supports credentials.

Open the Vite URL in your browser.

## API Overview

The backend currently exposes these routes:

### Authentication — `/api/auth`

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/register` | Create a user account |
| `POST` | `/login` | Sign in |
| `GET` | `/get-me` | Get the authenticated user's profile |
| `GET` | `/logout` | Log out and clear the authentication cookie |

### Interview preparation — `/api/interview`

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/` | Generate an interview report; authenticated; upload the resume in the `resume` multipart field |
| `GET` | `/` | List the authenticated user's interview reports |
| `GET` | `/report/:interviewId` | Retrieve one interview report |
| `POST` | `/resume/pdf/:interviewReportId` | Generate a tailored resume PDF |

Requests that require authentication use the JWT cookie expected by the backend. Use the frontend's existing Axios configuration or send requests with credentials enabled when testing from a separate client.

## Typical Workflow

1. Register an account and sign in.
2. Upload a resume PDF.
3. Enter your self-description and the target job description.
4. Generate a personalized interview preparation report.
5. Review the match score, technical and behavioral questions, suggested answers, skill gaps, and preparation plan.
6. Generate a job-tailored resume PDF if needed.

## Available Scripts

### Backend

```bash
npm run dev
```

Runs the backend with Nodemon through `npx`.

### Frontend

```bash
npm run dev      # Start the Vite development server
npm run build    # Build the frontend for production
npm run preview  # Preview the production build
npm run lint     # Run ESLint
```

The backend package currently does not include a configured automated test suite.

## Security and Production Notes

This project is a development/portfolio starting point and should be reviewed before public deployment.

- Ensure every report and generated PDF is authorized against the authenticated user's ID.
- Set authentication cookies with appropriate `httpOnly`, `secure`, and `sameSite` options; assess CSRF protection for the deployment model.
- Validate uploaded files by size, MIME type, extension, and actual file content. The current backend upload limit is 3 MB, and the resume parser is PDF-oriented.
- Keep Gemini API keys and MongoDB credentials in server-side environment variables.
- Add request-size limits, rate limiting, timeouts, and consistent error handling around expensive AI endpoints.
- Treat AI-generated HTML as untrusted. Prefer rendering validated resume data into a fixed template and restrict browser access to external resources.
- Configure production CORS origins explicitly and use HTTPS.
- Connect to MongoDB successfully before accepting requests.
- Add automated tests for authentication, authorization, uploads, AI failures, and report ownership.

## Roadmap Ideas

Potential next improvements:

- Adaptive AI mock interviews with follow-up questions
- Voice-based answers using speech-to-text and text-to-speech
- Evidence-based resume/job requirement matching
- Progress tracking across mock interviews
- Rate limiting, observability, and integration tests

## Limitations

AI-generated scores, questions, answers, and recommendations may be inaccurate or incomplete. Review the output before relying on it for career decisions. Do not upload sensitive documents unless you understand how the application and its AI provider process that data.

## License

No license is currently specified. Add a `LICENSE` file before presenting the repository as an open-source project.
