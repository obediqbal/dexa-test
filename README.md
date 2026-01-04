# WFH Attendance System

A microservices-based attendance management system built with NestJS (Backend) and React (Frontend), orchestrated via NGINX.

> **Note for Dexa recruiters**: I have included the necessary envs and GCP key file as archive in the email.

## System Architecture

The system follows a microservices architecture secured behind a reverse proxy.

![System Architecture](/docs/architecture.jpg)

1.  **Public Network**: Users interact with the **Frontend Application** (ReactJS).
2.  **Reverse Proxy**: All API requests are routed through **NGINX**, which handles routing and acts as a gateway to the private network.
3.  **Authentication**: Secure **HttpOnly cookies** containing JWTs are used for authentication. All services perform stateless JWT validation.
4.  **Private Network**: Contains the microservices:
    *   **Auth Microservice**: Handles login and token generation.
    *   **Staffs Microservice**: Manages staff profiles and CRUD operations.
    *   **Attendance Microservice**: Handles clock-in/out and attendance records.
    *   **BFF (Backend-for-Frontend) Microservice**: Orchestrates complex operations like staff registration and aggregated reporting by communicating with other services.

## Public Endpoints

The following endpoints are exposed via NGINX at port `3010`.

| Endpoint | Method | Microservice | Description |
| :--- | :--- | :--- | :--- |
| **Authentication** | | | |
| `/api/v1/auth/login` | POST | **Auth** | App login (returns HttpOnly cookie) |
| **User (Staff)** | | | |
| `/api/v1/staff/me` | GET | **Staffs** | Get current user profile |
| `/api/v1/attendance/clock-in` | POST | **Attendance** | Clock in with photo |
| `/api/v1/attendance/clock-out` | POST | **Attendance** | Clock out |
| `/api/v1/attendance/today` | GET | **Attendance** | Get today's attendance |
| `/api/v1/attendance/history` | GET | **Attendance** | Get attendance history |
| **Admin** | | | |
| `/api/v1/admin/staff/register` | POST | **BFF** | Register new staff |
| `/api/v1/admin/staff` | GET | **Staffs** | List all staff |
| `/api/v1/admin/staff/:id` | PATCH | **Staffs** | Update staff details |
| `/api/v1/admin/attendance` | GET | **Attendance** | List all attendances |
| `/api/v1/admin/staff-attendance`| GET | **BFF** | Combined staff & attendance report |

## Running the System

The entire system can be run using Docker Compose.

### Prerequisites
- Docker and Docker Compose installed
- Environment variables configured (see .env.example files in each service)

### Build and Run

1. Navigate to the nginx directory:
   ```bash
   cd nginx
   ```

2. Build and start the services:
   ```bash
   docker-compose -f .\docker-compose.dev.yml up --build -d
   ```

The API gateway will be available at `http://localhost:3010`.