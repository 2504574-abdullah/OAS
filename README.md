# Doctor Online Appointment System (OAS)

This repository contains a clear implementation guide for building the semester project as a **full-stack Doctor Online Appointment System** using:
- **Backend:** ASP.NET Core Web API (.NET 8 or .NET 6+)
- **Database:** SQLite + Entity Framework Core
- **Frontend:** Angular 17 (or Angular 15+)
- **Architecture:** Clean Architecture + Repository + Service Layer + DTOs + DI

---

## 1) Project Goal
Build a role-based appointment platform with three roles:
- **Patient**
- **Doctor**
- **Admin**

Patients book appointments, doctors manage availability and appointment status, and admin manages hospitals, doctor fields, and system statistics.

---

## 2) Core Modules to Implement

### A. Authentication & User Management
- Patient registration/login (email/phone + password)
- Doctor registration/login (phone + password)
- Admin login
- Password hashing with BCrypt
- JWT-based authentication with role claims

### B. Master Data (Admin)
- Doctor fields/specializations CRUD
- Hospitals CRUD

### C. Doctor Availability
- Doctor defines date/time slots by hospital
- Slot status: Available / Booked / Cancelled

### D. Appointment Management
- Patient can create, view, reschedule, cancel appointments
- Doctor can confirm, complete, cancel appointments
- Status flow: Pending -> Confirmed -> Completed / Cancelled

### E. Dashboards
- Patient dashboard: personal appointments + status
- Doctor dashboard: pending/all/completed views + statistics
- Admin dashboard: totals + management tables

---

## 3) Recommended Clean Architecture Structure

```text
DoctorAppointmentSystem/
├── DoctorAppointmentSystem.API/
│   ├── Controllers/
│   ├── Program.cs
│   └── appsettings.json
├── DoctorAppointmentSystem.Application/
│   ├── DTOs/
│   ├── Interfaces/
│   └── Services/
├── DoctorAppointmentSystem.Domain/
│   └── Entities/
├── DoctorAppointmentSystem.Infrastructure/
│   ├── Data/
│   ├── Repositories/
│   └── Migrations/
└── DoctorAppointmentSystem.AngularClient/
    └── src/app/
        ├── auth/
        ├── patient/
        ├── doctor/
        ├── admin/
        ├── shared/
        ├── services/
        └── models/
```

---

## 4) Database Design (Tables)
Create these tables/entities first:
1. **Patients**
2. **Doctors**
3. **DoctorFields**
4. **Hospitals**
5. **DoctorAvailability**
6. **Appointments**
7. **Admins**

Define foreign keys:
- `Doctors.FieldId -> DoctorFields.Id`
- `DoctorAvailability.DoctorId -> Doctors.Id`
- `DoctorAvailability.HospitalId -> Hospitals.Id`
- `Appointments.PatientId -> Patients.Id`
- `Appointments.DoctorId -> Doctors.Id`
- `Appointments.HospitalId -> Hospitals.Id`

---

## 5) API Endpoints Checklist

### Auth
- `POST /api/auth/patient-register`
- `POST /api/auth/doctor-register`
- `POST /api/auth/login`
- `POST /api/auth/admin-login`

### Patients
- `GET /api/patients` (Admin)
- `GET /api/patients/{id}`
- `PUT /api/patients/{id}`
- `GET /api/patients/{id}/appointments`

### Doctors
- `GET /api/doctors` (Admin)
- `GET /api/doctors/{id}`
- `GET /api/doctors/field/{fieldId}`
- `PUT /api/doctors/{id}`

### Appointments
- `GET /api/appointments`
- `POST /api/appointments`
- `GET /api/appointments/{id}`
- `PUT /api/appointments/{id}`
- `DELETE /api/appointments/{id}`
- `PUT /api/appointments/{id}/confirm`
- `PUT /api/appointments/{id}/complete`
- `PUT /api/appointments/{id}/cancel`

### Availability
- `GET /api/availability/doctor/{doctorId}`
- `GET /api/availability/hospital/{hospitalId}`
- `POST /api/availability`
- `PUT /api/availability/{id}`
- `DELETE /api/availability/{id}`
- `GET /api/availability/search`

### Admin
- `GET /api/fields` / `POST` / `PUT` / `DELETE`
- `GET /api/hospitals` / `POST` / `PUT` / `DELETE`
- `GET /api/admin/statistics`
- `GET /api/admin/doctors-by-field`

---

## 6) Where to Start (Step-by-Step)

### Step 1: Backend Solution Setup
1. Create solution + 4 projects (API, Application, Domain, Infrastructure)
2. Add project references according to Clean Architecture
3. Install EF Core + SQLite + JWT + BCrypt packages

### Step 2: Domain First
1. Implement all entities and enums (AppointmentStatus, SlotStatus, UserRole)
2. Add navigation properties and validation annotations

### Step 3: Infrastructure
1. Create `AppDbContext`
2. Configure Fluent API relationships and constraints
3. Add repositories for each aggregate
4. Create initial migration and SQLite database

### Step 4: Application Layer
1. Create DTOs (auth, availability, appointment, dashboard)
2. Define interfaces (`IAuthService`, `IAppointmentService`, etc.)
3. Implement services with business rules:
   - Prevent double booking (same doctor + hospital + date + time cannot be assigned twice)
   - Only allow valid status transitions (e.g., `Pending -> Confirmed -> Completed`, and cancellation from `Pending`/`Confirmed` only)
   - Ensure doctor has an `Available` slot for the selected hospital/date/time before appointment creation or reschedule

### Step 5: API Layer
1. Add controllers by module (Auth, Appointments, Doctors, Patients, Admin)
2. Configure JWT auth + role policies
3. Add global exception handling and validation responses
4. Enable Swagger with JWT support

### Step 6: Angular Frontend
1. Create Angular app and feature modules (`auth`, `patient`, `doctor`, `admin`)
2. Create role-based routing and guards
3. Add typed models and HTTP services
4. Build pages in this order:
   - Auth pages
   - Patient booking + appointments list
   - Doctor appointment actions + availability management
   - Admin hospitals/fields/statistics

### Step 7: Integration & Testing
1. Connect Angular environment API base URL
2. Test all CRUD flows and role restrictions
3. Validate conflict cases (already booked slot, invalid status transition)
4. Verify responsive behavior for mobile/tablet/desktop

---

## 7) Suggested Milestones
- **Milestone 1:** Auth + master data complete
- **Milestone 2:** Availability + appointment booking complete
- **Milestone 3:** Role dashboards complete
- **Milestone 4:** Validation, polish, screenshots, deployment

---

## 8) Minimum Definition of Done
- All listed entities and CRUD endpoints implemented
- JWT role-based auth working for Patient/Doctor/Admin
- Appointment lifecycle fully functional
- Angular UI integrated with backend APIs
- SQLite DB with migrations
- Swagger documentation available
- Responsive UI and clear success/error messages

---

## 9) Local Development Commands (Reference)
The commands below assume you follow the exact folder names shown in the architecture section. If you choose different names, run the equivalent commands in your own project folders:

```bash
# Backend
cd DoctorAppointmentSystem.API
dotnet restore
dotnet ef database update
dotnet run

# Frontend
cd DoctorAppointmentSystem.AngularClient
npm install
ng serve
```

---

If you want, the next step after this guide is to scaffold the backend solution first (projects, references, packages, and initial entities), then we can continue module-by-module.
