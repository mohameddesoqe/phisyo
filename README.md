# PhysioFlow

A comprehensive SaaS system for physiotherapy clinics and patients.

## Architecture

This is a monorepo built with Turborepo containing:

- **Backend** (`apps/backend`): NestJS API with clean architecture
- **Frontend** (`apps/frontend`): Next.js 14 with App Router
- **Contracts** (`packages/contracts`): Shared Zod schemas and DTOs

## Features

### Clinic/Doctor Role
- ✅ Patient management with unique QR codes
- ✅ Exercise program creation and assignment
- ✅ Progress tracking and history
- ✅ Session scheduling with automated reminders
- ✅ Patient search by phone/ID
- ✅ Custom notification system

### Patient Role
- ✅ Clinic discovery (geo-based search ready)
- ✅ Doctor profiles and reviews
- ✅ Appointment booking system
- ✅ Exercise program access
- ✅ Session notes and progress tracking
- ✅ QR code registration
- ✅ Notification preferences

### System Features
- ✅ Multi-payment options support
- ✅ Responsive UI (mobile + web)
- ✅ Extensible architecture
- ✅ Pluggable notifications (SMS, Email, Push)
- ✅ Unique patient ID & QR code system
- ✅ Clean architecture with DDD principles

## Getting Started

### Prerequisites

- Node.js 18+
- pnpm 8+
- PostgreSQL 14+

### Installation

\`\`\`bash
# Install dependencies
pnpm install

# Setup environment variables
cp apps/backend/.env.example apps/backend/.env
# Edit the .env file with your database credentials

# Generate Prisma client
cd apps/backend && pnpm db:generate

# Run database migrations
pnpm db:migrate

# Seed the database with sample data
pnpm db:seed

# Start development servers
pnpm dev
\`\`\`

### Development

- Backend API: http://localhost:3001
- Frontend: http://localhost:3000
- API Documentation: http://localhost:3001/api/docs
- Database Studio: `cd apps/backend && pnpm db:studio`

### Database Management

\`\`\`bash
# Generate Prisma client after schema changes
pnpm db:generate

# Create and apply migrations
pnpm db:migrate

# Push schema changes without migration (development)
pnpm db:push

# Seed database with sample data
pnpm db:seed

# Reset database (⚠️ destructive)
pnpm db:reset

# Open Prisma Studio
pnpm db:studio
\`\`\`

### Sample Login Credentials

After seeding the database:

- **Clinic**: admin@wellnessclinic.com / password123
- **Doctor**: dr.smith@wellnessclinic.com / password123  
- **Patient**: john.doe@email.com / password123

## Tech Stack

- **Monorepo**: Turborepo + pnpm
- **Backend**: NestJS + Prisma + PostgreSQL
- **Frontend**: Next.js 14 + Tailwind + shadcn/ui
- **Database**: PostgreSQL with Prisma ORM
- **Shared**: Zod schemas for type safety
- **Architecture**: Clean Architecture, Domain-Driven Design
- **Notifications**: Pluggable adapters (SMS, Email, Push)

## Project Structure

\`\`\`
physioflow/
├── apps/
│   ├── backend/           # NestJS API
│   │   ├── src/
│   │   │   ├── modules/   # Feature modules
│   │   │   ├── shared/    # Shared services
│   │   │   └── infrastructure/ # Database, external services
│   │   ├── prisma/        # Database schema and migrations
│   │   └── scripts/       # SQL scripts
│   └── frontend/          # Next.js app
│       └── src/
│           ├── app/       # App Router pages
│           ├── components/ # React components
│           └── providers/ # Context providers
├── packages/
│   └── contracts/         # Shared Zod schemas
└── scripts/               # Database setup scripts
\`\`\`

## API Documentation

The API documentation is available at http://localhost:3001/api/docs when running the backend server.

## Contributing

1. Follow the established patterns for clean architecture
2. Use the shared contracts for type safety
3. Write tests for new features
4. Update documentation as needed

## License

This project is licensed under the UNLICENSED license.
