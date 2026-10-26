# TuitionDesk

A web-based enquiry management system for a tuition centre with two branches (SS2 and SS15).  
Parents submit enquiries via a form; AI (Google Gemini) auto-categorises each enquiry and drafts a reply based on the centre's FAQ. Staff review and approve replies on a protected admin dashboard.

## Live Demo

**🌐 https://tuition-desk.onrender.com**

- Parent enquiry form: `/`
- Admin dashboard: `/admin/` (Basic auth — credentials set via environment variables)

> The Render free tier spins down after inactivity; the first request may take ~30 seconds.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | ASP.NET Core 8 Minimal API (C#) |
| Database | PostgreSQL (Neon) in production, SQLite locally |
| ORM | Entity Framework Core 8 |
| AI | Google Gemini 2.5 Flash (free tier) |
| Frontend | Plain HTML + vanilla JS (no build step) |
| Deployment | Docker → Render |

## Features

- **Parent Form** — collects name, phone, branch (SS2/SS15), child's age, and question.
- **AI Triage** — Gemini categorises enquiries into `Fees`, `Schedule`, `Courses`, or `Other` and drafts a polite reply using only FAQ facts. Understands both English and Chinese.
- **Needs Human flag** — questions outside the FAQ are flagged for manual follow-up; the AI never invents answers.
- **Admin Dashboard** — lists all enquiries, filterable by branch, shows total and pending-human counts, and provides an editable draft reply with an Approve button.
- **Rate Limiting** — 20 requests/minute on the enquiry endpoint.
- **Basic Auth** — admin routes are protected by password.

## FAQ (AI answers only from this)

| Topic | Details |
|-------|---------|
| Fees | Primary RM280/month, Secondary RM350/month, Registration RM50 |
| Hours | Mon–Fri 2 pm–8 pm, Sat 9 am–1 pm, closed Sunday |
| Class size | Max 12 students |
| Subjects | BM, English, Maths, Science; Secondary adds History & Geography |
| Trial | 1 free trial lesson, by appointment |
| Ages | 7–17 |
| Payment | Bank transfer or cash, due before the 5th |
| Leave | 1 day notice, max 2 make-up lessons/month |
| Transport | No school bus |

## Run Locally

```bash
# Prerequisites: .NET 8 SDK (8.0.408)

# Set secrets
dotnet user-secrets set "Gemini:ApiKey"   "<your-google-ai-studio-key>"
dotnet user-secrets set "Admin:Password"  "<your-admin-password>"
# Optional — omit to use SQLite
dotnet user-secrets set "ConnectionStrings:Default" "Host=...;Database=...;Username=...;Password=..."

# Run
dotnet run
```

- Parent form → http://localhost:5000/
- Admin dashboard → http://localhost:5000/admin/

## Project Structure

```
tuition-desk/
├── Program.cs              # All server code (endpoints, models, DB, AI service)
├── TuitionDesk.csproj      # .NET 8 project file
├── appsettings.json        # Default config (model name, no secrets)
├── global.json             # Pins SDK 8.0.408
├── Dockerfile              # Multi-stage build for deployment
├── .gitignore
├── .dockerignore
└── wwwroot/
    ├── index.html           # Parent enquiry form
    └── admin/
        └── index.html       # Admin dashboard
```

## Environment Variables (Production)

| Variable | Purpose |
|----------|---------|
| `ConnectionStrings__Default` | PostgreSQL connection string |
| `Gemini__ApiKey` | Google AI Studio API key |
| `ADMIN_PASSWORD` | Admin dashboard password |
