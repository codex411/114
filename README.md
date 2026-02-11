# Staff Record Management System

## Project Summary

This is a full-stack **staff and career record management platform** that transforms audio recordings into structured career documents using AI. The system enables companies to upload voice memos or interview recordings, automatically transcribe them with speech-to-text (Whisper), and generate skill sheets, letters of recommendation (LoR), and Salesforce-compatible data through an AI workflow (Dify).

### Core Capabilities

- **Multi-tenant architecture** — Companies with distinct dashboards, slug-based routing, and isolated data
- **AI-powered audio pipeline** — Upload audio → transcribe (Whisper) → extract structured data (Dify workflow) → store records
- **Async job processing** — BullMQ + Redis for background processing, chunked handling for long recordings
- **Salesforce integration** — Career field mappings, credential storage, and account sync
- **Role-based access** — Admin, company manager, and member roles with granular permissions
- **Records management** — Paginated records with skill sheets, LoR, Salesforce data, and memos

### Main Workflows

1. **Record creation**: User uploads audio → job queued → audio converted/split → STT per chunk → Dify workflow extracts skill sheet, LoR, Salesforce fields → record saved
2. **Follow-up tracking**: Separate "follows" table for follow-up records with same schema
3. **Company administration**: Invitations, user CRUD, Salesforce settings, career mappings

---

## Tech Stack

- **Frontend**: Next.js 15, React 19, TypeScript, Tailwind CSS, App Router
- **Backend**: Express.js (Node.js)
- **Database**: MySQL
- **Queue**: BullMQ, Redis
- **Audio**: FFmpeg, nodejs-whisper (STT)
- **AI**: Dify workflow API
- **CRM**: Salesforce (jsforce)

---

## Features

- Authentication (register, login, forgot password)
- Role-based access control (admin, company manager, member)
- Company management (CRUD, slug, logo)
- User management (CRUD, avatar)
- Record management (create, list, paginate, delete)
- Audio upload with async processing
- Skill sheet sidebar viewer
- Letter of recommendation (LoR) sidebar viewer
- Salesforce sidebar and sync
- Follow-up records
- Invitations
- Responsive layout

---

## Project Structure

```
.
├── frontend/               # Next.js frontend
│   ├── app/                # App Router pages
│   │   ├── [companySlug]/  # Company-scoped routes (dashboard, follow)
│   │   ├── admin/          # Admin routes
│   │   ├── companies/      # Company list
│   │   ├── users/          # User list
│   │   ├── login/          # Auth
│   │   ├── register/
│   │   └── salesforce-settings/
│   ├── components/         # React components
│   ├── contexts/           # Auth context
│   ├── hooks/              # useRecords, useRecordDetail
│   ├── lib/                # API client, types, utils
│   └── services/           # recordsService
└── backend/                # Express backend
    ├── src/
    │   ├── config/         # DB, Redis, axios
    │   ├── controllers/    # records, salesforce, follow, auth
    │   ├── middleware/     # auth, cache, upload
    │   ├── queues/         # audioQueue (BullMQ)
    │   ├── routes/         # API routes
    │   ├── services/       # audioProcessing, asyncProcessing, jobReaper
    │   ├── workers/        # audioWorker
    │   └── utils/          # logger, cache, jobLock
    └── fonts/              # NotoSansJP (Whisper)
```

---

## Getting Started

### Prerequisites

- Node.js 18+
- MySQL 8+
- Redis
- npm or yarn

### Frontend Setup

1. Go to the frontend directory:
```bash
cd frontend
```

2. Install dependencies:
```bash
npm install
```

3. Create `.env.local`:
```
NEXT_PUBLIC_API_URL=http://localhost:5000
```

4. Run the dev server:
```bash
npm run dev
```

### Backend Setup

1. Go to the backend directory:
```bash
cd backend
```

2. Install dependencies:
```bash
npm install
```

3. Create `.env`:
```
PORT=5000
DB_HOST=localhost
DB_USER=your_username
DB_PASSWORD=your_password
DB_NAME=company_management
JWT_SECRET=your_jwt_secret
REDIS_URL=redis://localhost:6379
```

4. Run the API server:
```bash
npm run dev
```

5. Run the audio worker (separate terminal):
```bash
npm run worker:dev
```

### Database Setup

1. Create a MySQL database named `company_management`
2. Tables are created on backend startup via migrations

---

## API Overview

### Authentication
- `POST /api/auth/register`
- `POST /api/auth/login`
- `POST /api/auth/forgot-password`
- `GET /api/auth/me`

### Companies
- `GET /api/companies`
- `POST /api/companies`
- `PUT /api/companies/:id`
- `DELETE /api/companies/:id`

### Users
- `GET /api/users`
- `POST /api/users`
- `PUT /api/users/:id`
- `DELETE /api/users/:id`

### Records
- `GET /api/records` (paginated)
- `POST /api/records` (upload)
- `GET /api/records/:id`
- `DELETE /api/records/:id`

### Salesforce
- `GET /api/salesforce/settings`
- `PUT /api/salesforce/settings`
- `POST /api/salesforce/sync-account`

### Jobs
- `GET /api/jobs` (user jobs)
- `POST /api/jobs/:id/retry`
- `POST /api/jobs/auto-delete` (optional scheduler)

---

## License

MIT
