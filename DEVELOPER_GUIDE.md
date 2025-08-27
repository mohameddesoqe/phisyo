# PhysioFlow Developer Guide

## Table of Contents

1. [Project Overview](#project-overview)
2. [Architecture](#architecture)
3. [Getting Started](#getting-started)
4. [Development Workflow](#development-workflow)
5. [Project Structure](#project-structure)
6. [Frontend Development](#frontend-development)
7. [Backend Development](#backend-development)
8. [Database Management](#database-management)
9. [API Documentation](#api-documentation)
10. [Testing](#testing)
11. [Deployment](#deployment)
12. [Contributing Guidelines](#contributing-guidelines)
13. [Troubleshooting](#troubleshooting)

## Project Overview

PhysioFlow is a comprehensive SaaS platform designed for physiotherapy clinics and patients. The system provides role-based access for clinic administrators, doctors, and patients, featuring appointment management, exercise programs, QR code patient registration, and automated notifications.

### Key Features

- **Multi-role Authentication**: Clinic/Doctor and Patient roles with different access levels
- **Patient Management**: Complete patient lifecycle management with QR code registration
- **Appointment Scheduling**: Advanced booking system with automated reminders
- **Exercise Programs**: Customizable exercise routines and progress tracking
- **Notification System**: Multi-channel notifications (SMS, Email, Push) with automated reminders
- **Geo-location Services**: Find nearby clinics functionality
- **File Management**: Secure file upload and management system

### Technology Stack

- **Frontend**: Next.js 14 (App Router), React 18, TypeScript, Tailwind CSS, shadcn/ui
- **Backend**: NestJS, TypeScript, Clean Architecture
- **Database**: PostgreSQL with Prisma ORM
- **Authentication**: JWT with role-based access control
- **Monorepo**: Turborepo for workspace management
- **Validation**: Zod schemas for type-safe validation
- **Notifications**: Pluggable adapter pattern for multiple channels

## Architecture

PhysioFlow follows Clean Architecture principles with clear separation of concerns:

\`\`\`
┌─────────────────────────────────────────────────────────────┐
│                    Presentation Layer                       │
│  ┌─────────────────┐              ┌─────────────────────┐   │
│  │   Next.js App   │              │  NestJS Controllers │   │
│  │   (Frontend)    │              │    (API Layer)      │   │
│  └─────────────────┘              └─────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────┐
│                   Application Layer                         │
│  ┌─────────────────┐              ┌─────────────────────┐   │
│  │  React Hooks &  │              │   NestJS Services   │   │
│  │   Components    │              │   (Business Logic)  │   │
│  └─────────────────┘              └─────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────┐
│                   Domain Layer                              │
│  ┌─────────────────────────────────────────────────────┐   │
│  │        Shared Contracts (Zod Schemas & Types)       │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────┐
│                Infrastructure Layer                         │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────┐ │
│  │   Prisma ORM    │  │   Notification  │  │   External  │ │
│  │   (Database)    │  │    Adapters     │  │   Services  │ │
│  └─────────────────┘  └─────────────────┘  └─────────────┘ │
└─────────────────────────────────────────────────────────────┘
\`\`\`

### Monorepo Structure

\`\`\`
physioflow/
├── apps/
│   ├── backend/          # NestJS API server
│   └── frontend/         # Next.js web application
├── packages/
│   └── contracts/        # Shared types and validation schemas
├── scripts/              # Database scripts and utilities
├── package.json          # Root package configuration
├── turbo.json           # Turborepo configuration
└── pnpm-workspace.yaml  # PNPM workspace configuration
\`\`\`

## Getting Started

### Prerequisites

- Node.js 18+ 
- PNPM 8+
- PostgreSQL 14+
- Git

### Installation

1. **Clone the repository**
   \`\`\`bash
   git clone <repository-url>
   cd physioflow
   \`\`\`

2. **Install dependencies**
   \`\`\`bash
   pnpm install
   \`\`\`

3. **Environment Setup**
   \`\`\`bash
   # Backend environment
   cp apps/backend/.env.example apps/backend/.env
   
   # Frontend environment  
   cp apps/frontend/.env.example apps/frontend/.env
   \`\`\`

4. **Database Setup**
   \`\`\`bash
   # Create database
   createdb physioflow_dev
   
   # Run migrations
   cd apps/backend
   npx prisma migrate dev
   
   # Seed database
   npx prisma db seed
   \`\`\`

5. **Start Development Servers**
   \`\`\`bash
   # Start all services
   pnpm dev
   
   # Or start individually
   pnpm dev:backend   # Backend on http://localhost:3001
   pnpm dev:frontend  # Frontend on http://localhost:3000
   \`\`\`

### Environment Variables

#### Backend (.env)
\`\`\`env
# Database
DATABASE_URL="postgresql://username:password@localhost:5432/physioflow_dev"

# JWT
JWT_SECRET="your-super-secret-jwt-key"
JWT_EXPIRES_IN="7d"

# API Configuration
PORT=3001
BASE_URL="http://localhost:3001"
FRONTEND_URL="http://localhost:3000"

# Notification Services
TWILIO_ACCOUNT_SID="your-twilio-sid"
TWILIO_AUTH_TOKEN="your-twilio-token"
TWILIO_PHONE_NUMBER="your-twilio-phone"

SENDGRID_API_KEY="your-sendgrid-key"
SENDGRID_FROM_EMAIL="noreply@physioflow.com"

# File Upload
UPLOAD_DEST="./uploads"
MAX_FILE_SIZE=5242880  # 5MB
\`\`\`

#### Frontend (.env.local)
\`\`\`env
NEXT_PUBLIC_API_URL="http://localhost:3001"
NEXT_PUBLIC_APP_URL="http://localhost:3000"
\`\`\`

## Development Workflow

### Branch Strategy

- `main`: Production-ready code
- `develop`: Integration branch for features
- `feature/*`: Feature development branches
- `hotfix/*`: Critical bug fixes

### Commit Convention

Follow Conventional Commits specification:

\`\`\`
feat(scope): add new feature
fix(scope): fix bug
docs(scope): update documentation
style(scope): formatting changes
refactor(scope): code refactoring
test(scope): add tests
chore(scope): maintenance tasks
\`\`\`

### Code Quality

\`\`\`bash
# Linting
pnpm lint

# Type checking
pnpm type-check

# Formatting
pnpm format

# Testing
pnpm test

# Build
pnpm build
\`\`\`

## Project Structure

### Frontend Structure (`apps/frontend/`)

\`\`\`
src/
├── app/                    # Next.js App Router
│   ├── (auth)/            # Auth route group
│   ├── dashboard/         # Protected dashboard routes
│   ├── globals.css        # Global styles
│   ├── layout.tsx         # Root layout
│   └── page.tsx          # Home page
├── components/            # React components
│   ├── ui/               # shadcn/ui components
│   ├── auth/             # Authentication components
│   ├── dashboard/        # Dashboard components
│   ├── patients/         # Patient management components
│   └── notifications/    # Notification components
├── hooks/                # Custom React hooks
├── lib/                  # Utility functions
├── providers/            # Context providers
└── types/               # TypeScript type definitions
\`\`\`

### Backend Structure (`apps/backend/`)

\`\`\`
src/
├── modules/              # Feature modules
│   ├── auth/            # Authentication module
│   ├── patients/        # Patient management
│   ├── appointments/    # Appointment scheduling
│   ├── notifications/   # Notification system
│   └── clinics/        # Clinic management
├── shared/              # Shared services and utilities
├── infrastructure/      # External service integrations
├── app.module.ts       # Root application module
└── main.ts            # Application entry point
\`\`\`

### Shared Contracts (`packages/contracts/`)

\`\`\`
src/
├── entities/           # Domain entities
├── dtos/              # Data transfer objects
├── enums/             # Shared enumerations
├── common/            # Common types and utilities
└── index.ts          # Package exports
\`\`\`

## Frontend Development

### Component Guidelines

1. **Use TypeScript**: All components must be typed
2. **Follow naming conventions**: PascalCase for components, camelCase for functions
3. **Implement proper error boundaries**: Handle errors gracefully
4. **Use custom hooks**: Extract reusable logic into hooks
5. **Optimize performance**: Use React.memo, useMemo, useCallback when appropriate

### Example Component Structure

\`\`\`tsx
// components/patients/patient-card.tsx
import { memo } from 'react'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Patient } from '@physioflow/contracts'

interface PatientCardProps {
  patient: Patient
  onEdit?: (patient: Patient) => void
  onDelete?: (patientId: string) => void
}

export const PatientCard = memo<PatientCardProps>(({ 
  patient, 
  onEdit, 
  onDelete 
}) => {
  return (
    <Card className="w-full">
      <CardHeader>
        <CardTitle>{patient.firstName} {patient.lastName}</CardTitle>
      </CardHeader>
      <CardContent>
        <p className="text-sm text-muted-foreground">
          {patient.email}
        </p>
        {/* Component content */}
      </CardContent>
    </Card>
  )
})

PatientCard.displayName = 'PatientCard'
\`\`\`

### State Management

- **Local State**: Use `useState` for component-specific state
- **Global State**: Use React Context for app-wide state
- **Server State**: Use custom hooks with fetch/SWR patterns
- **Form State**: Use `react-hook-form` with Zod validation

### Styling Guidelines

- **Use Tailwind CSS**: Utility-first approach
- **Follow design system**: Use shadcn/ui components
- **Responsive design**: Mobile-first approach
- **Dark mode support**: Use CSS custom properties

## Backend Development

### Module Structure

Each module follows clean architecture principles:

\`\`\`typescript
// modules/patients/patients.module.ts
@Module({
  imports: [DatabaseModule],
  controllers: [PatientsController],
  providers: [
    PatientsService,
    PatientsRepository,
    // Ports
    {
      provide: 'PATIENTS_REPOSITORY',
      useClass: PatientsRepository,
    },
  ],
  exports: [PatientsService],
})
export class PatientsModule {}
\`\`\`

### Service Layer Example

\`\`\`typescript
// modules/patients/services/patients.service.ts
@Injectable()
export class PatientsService {
  constructor(
    @Inject('PATIENTS_REPOSITORY')
    private readonly patientsRepository: IPatientsRepository,
    private readonly qrCodeService: QrCodeService,
  ) {}

  async createPatient(createPatientDto: CreatePatientDto): Promise<Patient> {
    // Business logic
    const patient = await this.patientsRepository.create(createPatientDto)
    
    // Generate QR code
    const qrCode = await this.qrCodeService.generatePatientQR(patient.id)
    
    return this.patientsRepository.update(patient.id, { qrCode })
  }
}
\`\`\`

### Repository Pattern

\`\`\`typescript
// modules/patients/repositories/patients.repository.ts
export interface IPatientsRepository {
  create(data: CreatePatientDto): Promise<Patient>
  findById(id: string): Promise<Patient | null>
  findByEmail(email: string): Promise<Patient | null>
  update(id: string, data: Partial<Patient>): Promise<Patient>
  delete(id: string): Promise<void>
}

@Injectable()
export class PatientsRepository implements IPatientsRepository {
  constructor(private readonly prisma: PrismaService) {}

  async create(data: CreatePatientDto): Promise<Patient> {
    return this.prisma.patient.create({ data })
  }

  // Implementation...
}
\`\`\`

### Validation and DTOs

\`\`\`typescript
// modules/patients/dto/create-patient.dto.ts
import { createZodDto } from 'nestjs-zod'
import { CreatePatientSchema } from '@physioflow/contracts'

export class CreatePatientDto extends createZodDto(CreatePatientSchema) {}
\`\`\`

## Database Management

### Prisma Schema

The database schema is defined in `apps/backend/prisma/schema.prisma`:

\`\`\`prisma
model Patient {
  id          String   @id @default(cuid())
  firstName   String
  lastName    String
  email       String   @unique
  phone       String?
  dateOfBirth DateTime?
  qrCode      String?  @unique
  clinicId    String
  
  clinic       Clinic        @relation(fields: [clinicId], references: [id])
  appointments Appointment[]
  programs     ExerciseProgram[]
  
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  @@map("patients")
}
\`\`\`

### Migrations

\`\`\`bash
# Create migration
npx prisma migrate dev --name add_patient_qr_code

# Apply migrations
npx prisma migrate deploy

# Reset database (development only)
npx prisma migrate reset
\`\`\`

### Seeding

\`\`\`typescript
// prisma/seed.ts
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

async function main() {
  // Create sample clinic
  const clinic = await prisma.clinic.create({
    data: {
      name: 'PhysioFlow Demo Clinic',
      email: 'demo@physioflow.com',
      phone: '+1234567890',
      address: '123 Health St, Medical City, MC 12345',
    },
  })

  // Create sample patients
  await prisma.patient.createMany({
    data: [
      {
        firstName: 'John',
        lastName: 'Doe',
        email: 'john.doe@example.com',
        phone: '+1234567891',
        clinicId: clinic.id,
      },
      // More sample data...
    ],
  })
}

main()
  .catch((e) => {
    console.error(e)
    process.exit(1)
  })
  .finally(async () => {
    await prisma.$disconnect()
  })
\`\`\`

## API Documentation

### Authentication Endpoints

\`\`\`typescript
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123",
  "role": "CLINIC" | "PATIENT"
}

Response:
{
  "access_token": "jwt-token",
  "user": {
    "id": "user-id",
    "email": "user@example.com",
    "role": "CLINIC"
  }
}
\`\`\`

### Patient Management Endpoints

\`\`\`typescript
// Get all patients (Clinic only)
GET /patients
Authorization: Bearer <token>

// Create patient
POST /patients
Authorization: Bearer <token>
Content-Type: application/json

{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com",
  "phone": "+1234567890",
  "dateOfBirth": "1990-01-01"
}

// Get patient by ID
GET /patients/:id
Authorization: Bearer <token>

// Update patient
PATCH /patients/:id
Authorization: Bearer <token>

// Delete patient
DELETE /patients/:id
Authorization: Bearer <token>
\`\`\`

### Notification Endpoints

\`\`\`typescript
// Send custom notification
POST /notifications/send
Authorization: Bearer <token>
Content-Type: application/json

{
  "recipientId": "patient-id",
  "type": "SMS" | "EMAIL" | "PUSH",
  "title": "Appointment Reminder",
  "message": "Your appointment is tomorrow at 2 PM",
  "scheduledFor": "2024-01-15T14:00:00Z" // Optional
}

// Get notification history
GET /notifications
Authorization: Bearer <token>
\`\`\`

## Testing

### Unit Testing

\`\`\`typescript
// patients.service.spec.ts
describe('PatientsService', () => {
  let service: PatientsService
  let repository: jest.Mocked<IPatientsRepository>

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        PatientsService,
        {
          provide: 'PATIENTS_REPOSITORY',
          useValue: {
            create: jest.fn(),
            findById: jest.fn(),
            // Mock methods...
          },
        },
      ],
    }).compile()

    service = module.get<PatientsService>(PatientsService)
    repository = module.get('PATIENTS_REPOSITORY')
  })

  it('should create a patient', async () => {
    const createPatientDto = {
      firstName: 'John',
      lastName: 'Doe',
      email: 'john@example.com',
    }

    repository.create.mockResolvedValue(mockPatient)

    const result = await service.createPatient(createPatientDto)

    expect(repository.create).toHaveBeenCalledWith(createPatientDto)
    expect(result).toEqual(mockPatient)
  })
})
\`\`\`

### Integration Testing

\`\`\`typescript
// patients.controller.e2e-spec.ts
describe('PatientsController (e2e)', () => {
  let app: INestApplication
  let prisma: PrismaService

  beforeEach(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile()

    app = moduleFixture.createNestApplication()
    prisma = moduleFixture.get<PrismaService>(PrismaService)
    
    await app.init()
  })

  it('/patients (POST)', () => {
    return request(app.getHttpServer())
      .post('/patients')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        firstName: 'John',
        lastName: 'Doe',
        email: 'john@example.com',
      })
      .expect(201)
      .expect((res) => {
        expect(res.body.firstName).toBe('John')
        expect(res.body.email).toBe('john@example.com')
      })
  })
})
\`\`\`

### Frontend Testing

\`\`\`typescript
// components/patients/patient-card.test.tsx
import { render, screen } from '@testing-library/react'
import { PatientCard } from './patient-card'

const mockPatient = {
  id: '1',
  firstName: 'John',
  lastName: 'Doe',
  email: 'john@example.com',
}

describe('PatientCard', () => {
  it('renders patient information', () => {
    render(<PatientCard patient={mockPatient} />)
    
    expect(screen.getByText('John Doe')).toBeInTheDocument()
    expect(screen.getByText('john@example.com')).toBeInTheDocument()
  })
})
\`\`\`

## Deployment

### Production Environment Setup

1. **Environment Variables**
   \`\`\`bash
   # Production .env
   DATABASE_URL="postgresql://user:pass@prod-db:5432/physioflow"
   JWT_SECRET="production-secret-key"
   NODE_ENV="production"
   \`\`\`

2. **Database Migration**
   \`\`\`bash
   npx prisma migrate deploy
   \`\`\`

3. **Build Applications**
   \`\`\`bash
   pnpm build
   \`\`\`

### Docker Deployment

\`\`\`dockerfile
# Dockerfile
FROM node:18-alpine AS base
RUN npm install -g pnpm

FROM base AS dependencies
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
COPY apps/backend/package.json ./apps/backend/
COPY apps/frontend/package.json ./apps/frontend/
COPY packages/contracts/package.json ./packages/contracts/
RUN pnpm install --frozen-lockfile

FROM base AS build
WORKDIR /app
COPY . .
COPY --from=dependencies /app/node_modules ./node_modules
RUN pnpm build

FROM base AS runtime
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules
EXPOSE 3000 3001
CMD ["pnpm", "start"]
\`\`\`

### Vercel Deployment (Frontend)

\`\`\`json
// vercel.json
{
  "builds": [
    {
      "src": "apps/frontend/package.json",
      "use": "@vercel/next"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "apps/frontend/$1"
    }
  ]
}
\`\`\`

## Contributing Guidelines

### Code Review Process

1. **Create Feature Branch**
   \`\`\`bash
   git checkout -b feature/patient-qr-codes
   \`\`\`

2. **Make Changes**
   - Follow coding standards
   - Add tests for new features
   - Update documentation

3. **Submit Pull Request**
   - Clear description of changes
   - Link to related issues
   - Include screenshots for UI changes

4. **Code Review**
   - At least one approval required
   - All tests must pass
   - No merge conflicts

### Coding Standards

- **TypeScript**: Strict mode enabled
- **ESLint**: Follow configured rules
- **Prettier**: Consistent formatting
- **Naming**: Descriptive and consistent
- **Comments**: Document complex logic
- **Error Handling**: Proper error boundaries and logging

### Performance Guidelines

- **Bundle Size**: Monitor and optimize
- **Database Queries**: Use indexes and optimize N+1 queries
- **Caching**: Implement appropriate caching strategies
- **Images**: Optimize and use Next.js Image component
- **API**: Implement pagination and filtering

## Troubleshooting

### Common Issues

#### Database Connection Issues
\`\`\`bash
# Check database status
pg_isready -h localhost -p 5432

# Reset database
npx prisma migrate reset
npx prisma db seed
\`\`\`

#### Build Failures
\`\`\`bash
# Clear cache
pnpm clean
rm -rf node_modules
pnpm install

# Type check
pnpm type-check
\`\`\`

#### Authentication Issues
\`\`\`bash
# Verify JWT secret
echo $JWT_SECRET

# Check token expiration
# Use jwt.io to decode tokens
\`\`\`

### Development Tips

1. **Hot Reload Issues**: Restart development server
2. **Type Errors**: Run `pnpm type-check` to identify issues
3. **Database Schema Changes**: Always create migrations
4. **Environment Variables**: Restart server after changes
5. **CORS Issues**: Check frontend/backend URL configuration

### Logging and Monitoring

\`\`\`typescript
// Use structured logging
import { Logger } from '@nestjs/common'

const logger = new Logger('PatientsService')

logger.log('Patient created successfully', { patientId: patient.id })
logger.error('Failed to create patient', error.stack)
logger.warn('Patient email already exists', { email })
\`\`\`

### Performance Monitoring

- **Database**: Use Prisma query logging
- **API**: Implement request/response logging
- **Frontend**: Use React DevTools Profiler
- **Bundle**: Analyze with `@next/bundle-analyzer`

---

## Support

For technical support or questions:

- **Documentation**: Check this guide first
- **Issues**: Create GitHub issues for bugs
- **Discussions**: Use GitHub Discussions for questions
- **Code Review**: Request reviews for complex changes

## License

This project is licensed under the MIT License. See LICENSE file for details.

---

*Last updated: January 2024*
*Version: 1.0.0*
