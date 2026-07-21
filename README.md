# Student Management System — ASP.NET Core Web API

A layered Web API for managing student records, built with ASP.NET Core, EF Core, and SQL Server.

## Features

- CRUD APIs for students (Get All, Add, Update, Delete)
- JWT Authentication (login required to access student endpoints)
- Global exception handling middleware (consistent JSON error responses)
- Structured logging with Serilog (console + rolling file logs)
- Swagger / OpenAPI documentation with JWT "Authorize" support
- Layered architecture: Controller → Service → Repository → EF Core

## Tech Stack

- .NET 8 / ASP.NET Core Web API
- Entity Framework Core (SQL Server provider)
- JWT Bearer Authentication
- Serilog
- Swashbuckle (Swagger)

## Project Structure

```
StudentManagementAPI/
├── Controllers/       # HTTP endpoints (AuthController, StudentsController)
├── Services/           # Business logic
├── Repositories/       # Data access (EF Core)
├── Models/             # EF Core entities
├── DTOs/               # Request/response shapes
├── Data/               # DbContext
├── Middleware/          # Global exception handling + custom exceptions
├── Program.cs           # App startup & DI wiring
└── appsettings.json     # Config (connection string, JWT settings)
```

## Setup Steps

### 1. Prerequisites
- [.NET 8 SDK](https://dotnet.microsoft.com/download)
- SQL Server (LocalDB, Express, or Docker container)

### 2. Configure the database connection
Edit `appsettings.json` and update `ConnectionStrings:DefaultConnection` to point to your SQL Server instance.

### 3. Restore packages
```bash
dotnet restore
```

### 4. Create the database
This project uses EF Core Migrations.
```bash
dotnet tool install --global dotnet-ef   # only needed once
dotnet ef migrations add InitialCreate
dotnet ef database update
```

### 5. Run the API
```bash
dotnet run
```
The API will be available at `https://localhost:<port>` and Swagger UI at `https://localhost:<port>/swagger`.

### 6. Authenticate
1. In Swagger, call `POST /api/auth/login` with:
   ```json
   { "username": "admin", "password": "Password123!" }
   ```
2. Copy the returned `token` value.
3. Click **Authorize** in Swagger (top right), paste the token, and click **Authorize**.
4. You can now call the `/api/students` endpoints.

> Note: the demo login uses a single hardcoded user for simplicity, as is standard for this kind of assignment. In production, this would be replaced with a Users table and hashed passwords (e.g. ASP.NET Core Identity).

## API Endpoints

| Method | Endpoint             | Auth required | Description         |
|--------|-----------------------|:-------------:|----------------------|
| POST   | `/api/auth/login`     | No            | Get a JWT token      |
| GET    | `/api/students`       | Yes           | Get all students     |
| GET    | `/api/students/{id}`  | Yes           | Get one student      |
| POST   | `/api/students`       | Yes           | Add a student        |
| PUT    | `/api/students/{id}`  | Yes           | Update a student      |
| DELETE | `/api/students/{id}`  | Yes           | Delete a student      |

## Error Handling

All errors return a consistent JSON shape via global middleware:
```json
{ "statusCode": 404, "message": "Student with id 5 was not found." }
