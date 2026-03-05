# GestionSalles

GestionSalles is a Java desktop information system for university room governance, reservation control, and role-scoped academic operations.

## Abstract

The platform centralizes institutional room planning through a layered architecture that combines Swing-based interfaces, service-driven business workflows, DAO-based persistence, and a MySQL relational model. The system enforces conflict-aware reservation processing, role-bounded visibility, authenticated session control, and auditable operational behavior.

## Functional Domain Coverage

- Identity-aware access for `Admin`, `Chef_Departement`, and `Enseignant` roles
- Reservation lifecycle management: create, update, search, filter, delete
- Conflict-safe allocation across room, teacher, and level dimensions
- Organizational entity administration: departments, blocs, levels, rooms, users
- Role-scoped schedule visualization and timetable navigation
- Password recovery, account security, and session continuity controls
- Activity and audit visibility for operational monitoring

## User Experience Model

- Swing desktop UI with FlatLaf styling
- Card-based navigation to avoid multi-window fragmentation
- Asynchronous processing (`SwingWorker`) for long-running actions
- Validation-first form handling with contextual feedback
- Confirmation-gated destructive actions
- Data-table centric management with search, filtering, export, and print

## Runtime Lifecycle

### Startup Sequence

1. Logging and UI theme initialization
2. Database connectivity and schema validation
3. Remember-me token authentication attempt
4. Login screen fallback when token authentication is invalid

### Access Flows

- Credential login through `AuthService`
- Remember-me token validation with active-session issuance
- Role-based routing to dashboard context
- Immediate fallback to login on invalid or expired token state

### Session Revalidation

- Periodic session validity checks during active dashboard runtime
- Forced logout on token invalidation, supersession, or security-triggered revocation

## Role-Specific Operational Boundaries

### Admin

- Global administrative scope
- Full management modules: departments, rooms, levels, users, reservations
- Broad schedule viewer filters and organization-wide analytics

### Chef_Departement

- Department/bloc constrained scope
- Managed access to reservations, rooms, users, and schedule views within assigned context

### Enseignant

- Teacher-focused experience
- Personal schedule orientation with reduced navigation complexity

## Critical Workflow Specifications

### Password Recovery

Three-step recovery pipeline:

1. Email submission and verification-code issuance
2. Code validation with expiry-bound lifecycle controls
3. Password reset with mandatory session invalidation

Security controls include rate-limiting, active-user checks, and bounded verification windows.

### Reservation Processing

Reservation orchestration includes:

- Dynamic form constraints (activity mode, recurrence, online/physical room context)
- Server-side conflict verification before persistence
- Structured conflict reporting for duplicate, single-conflict, and multi-conflict states
- Blocked commit path until conflict-free validation is achieved

### Schedule Visualization

- Multi-context filtering (`Salle`, `Enseignant`, `Niveau`) for privileged roles
- Department-scoped variants for constrained roles
- Identity-scoped teacher schedule rendering
- Print and PDF export integrations

## Architecture

### Layered Structure

1. Entry Layer: bootstrap and startup routing (`Main`, `MainApp`)
2. Presentation Layer: role and shared UI modules (`views/*`)
3. Service Layer: business orchestration (`services/*`)
4. Data Access Layer: SQL gateways and mappings (`dao/*`, `database/*`)
5. Domain Layer: business entities and enums (`models/*`)
6. Infrastructure Layer: session/security/audit utilities (`utils/*`)
7. Schema Layer: SQL schema, migrations, and seed assets (`db/*`)

### Core Execution Path

1. UI interaction triggers application intent
2. Service layer applies business validation and orchestration
3. DAO layer executes relational operations
4. Domain objects propagate back to the presentation layer
5. UI state is refreshed on EDT with asynchronous work isolation

## Security Architecture

### Authentication and Credential Safety

- Password hashing and verification through dedicated cryptographic utilities
- Token and credential authentication entry points with role-consistent routing
- Controlled handling of sensitive authentication data in runtime flows

### Session Security

- Active session persistence in `active_sessions`
- Single active session policy per user identity
- Constant-time token comparison for validation paths
- Heartbeat-based session continuity checks

### Recovery Security

- Verification-code generation and expiry enforcement
- Non-plaintext persisted verification state
- Request/attempt protection to reduce brute-force risk

### Secret and Configuration Hardening

Secret resolution precedence:

1. JVM property: `app.verification.secret`
2. Environment variable: `GESTION_SALLES_SECRET`
3. Local secure secret file: `~/.gestion-salles/app.secret`

Configuration hardening includes mandatory value checks, placeholder rejection, and insecure-default DB configuration rejection.

### Auditing

- Security-significant operations are tracked through `AuditLogger`
- Recovery and password events are audit-visible for traceability

## Data and Persistence Model

### Principal Domain Models

- `User`, `Reservation`, `Room`, `Departement`, `Bloc`, `Niveau`
- `ScheduleEntry`, `Conflict`, `ActivityLog`, `ActivityType`, `DashboardScope`, `ActivityItemData`

### Principal Tables

- `utilisateurs`, `reservations`, `salles`, `niveaux`, `blocs`, `departements`
- `activity_log`, `audit_log`, `active_sessions`, `verification_codes`

### Conflict Procedures

- `verifier_conflit_salle`
- `verifier_conflit_enseignant`
- `verifier_conflit_niveau`

### SQL Assets

- `db/schema.sql`
- `db/migrations/001_conflict_procedures.sql`
- `db/seed/minimal_seed.sql`
- `gestion_salles.sql`

## Technical Component Inventory

### DAO Layer

- `ActivityLogDAO`, `ActivityTypeDAO`, `BlocDAO`, `DashboardDAO`, `DepartementDAO`
- `NiveauDAO`, `ReservationDAO`, `ReservationMapper`, `ReservationConflictException`
- `RoomDAO`, `ScheduleDAO`, `UserDAO`

### Service Layer

- `AuthService`
- `ConflictDetectionService`
- `EmailService`
- `VerificationCodeManager`

### Infrastructure and Utilities

- `AppConfig`, `SessionManager`, `SessionContext`, `TokenStorage`, `SecretManager`
- `AuditLogger`, `PasswordUtils`, `PasswordStrengthChecker`, `PasswordFormHelper`, `HealthCheck`
- Additional UI/infra support classes in `utils/`

### Presentation Packages

- `views/Admin/*`
- `views/ChefDepartement/*`
- `views/Enseignant/*`
- `views/Login/*`
- `views/shared/*`

## Quality and Test Coverage

Representative test suites:

- `ReservationDAOTest`
- `DatabaseConnectionTest`
- `AuthServiceTest`
- `ConflictDetectionServiceTest`
- `VerificationCodeManagerTest`
- `DashboardDevTest`
- `LoginFlowDevTest`

Quality posture:

- Coverage targets service and persistence logic plus selected UI/dev flows
- Runtime robustness depends on valid environment and database provisioning

## Build and Packaging Profile

- Build system: Maven
- Packaging includes shaded runtime artifact generation
- Installer profile support for desktop distribution targets
- Operational scripts for icon extraction, post-install setup, and health checks

## Repository Structure

- `src/main/java/com/gestion/salles`: application source
- `src/main/resources`: icons, configuration templates, runtime scripts
- `src/test/java/com/gestion/salles`: test suites
- `db/`: schema, migration, and seed SQL assets
- `docs/`: subsystem-specific technical notes
- `APP_FULL_REFERENCE.md`: full product and UX reference baseline

## Architecture Diagram

![GestionSalles Layered Architecture](docs/screenshots/architecture-layering.png)

## Documentation References

- `APP_FULL_REFERENCE.md`
- `docs/security-and-session.md`
- `docs/database.md`
- `docs/packaging.md`

## Intellectual Property and Usage Restrictions

Copyright (c) 2026 Abdelrahman Benmoulai.
All rights reserved.

This repository is published for documentation and evaluation purposes only.
No right is granted to copy, reuse, modify, redistribute, or integrate any part of this codebase into another work without explicit prior written authorization from the copyright holder.
