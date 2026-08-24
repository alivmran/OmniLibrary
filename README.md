# OmniLibrary | Distributed Library Management System

OmniLibrary is an enterprise-grade digital library management system built using **ASP.NET Core microservices**. The solution follows a distributed architecture with isolated domain services for users, authors, and books. Each service uses its own database context in **Microsoft SQL Server**, while an **ASP.NET Core Razor Pages** frontend provides a unified interface for interacting with the system.

---

## Microservices Architecture

| Service | Port | Base Route | Description |
|---|---|---|---|
| **UserService** | `https://localhost:7175` | `/api/users` | Handles user identity, JWT authentication, and user profile CRUD operations. |
| **AuthorService** | `https://localhost:7183` | `/api/authors` | Manages author metadata, catalog records, and author lifecycle operations. |
| **BookService** | `https://localhost:7265` | `/api/books` | Handles book cataloging, inventory tracking, and circulation management. |
| **Frontend Client** | `https://localhost:5001` | `/` | ASP.NET Core Razor Pages application consuming all upstream microservices. |

---

## Tech Stack

- **Framework:** .NET 8, ASP.NET Core Web API, Razor Pages
- **Language:** C#
- **ORM:** Entity Framework Core
- **Database:** Microsoft SQL Server
- **Security & Authentication:** JSON Web Tokens (JWT)
- **Architecture:** Microservices
- **API Style:** RESTful APIs

---

## Project Structure

```text
LibraryManagement/
├── FrontEnd/
│   └── FrontEnd/                    # Razor Pages web client
│
├── Services/
│   ├── AuthorService/
│   │   └── AuthorService/           # Author CRUD & metadata API
│   ├── BookService/
│   │   └── BookService/             # Book catalog & inventory API
│   └── UserServices/
│       └── UserServices/            # User management & authentication API
│
└── README.md
```

---

## Getting Started

### Prerequisites

Before running OmniLibrary, make sure the following software is installed:

- [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- Microsoft SQL Server
  - SQL Server LocalDB
  - SQL Server Express
  - Or a remote SQL Server instance
- Entity Framework Core CLI Tool

Install the EF Core CLI tool globally using:

```bash
dotnet tool install --global dotnet-ef
```

Verify the installation:

```bash
dotnet ef --version
```

---

## Installation & Setup

### 1. Clone the Repository

```bash
git clone https://github.com/alivmran/OmniLibrary.git
cd OmniLibrary
```

---

### 2. Configure Database Connection Strings

Each microservice has its own database context. Update the `appsettings.json` or `appsettings.Development.json` file inside each service with your SQL Server connection details.

For example, in:

```text
Services/UserServices/UserServices/appsettings.json
```

use:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=OmniLibrary_UserDb;Trusted_Connection=True;TrustServerCertificate=True;"
  },
  "JwtSettings": {
    "Secret": "your_jwt_secret_key_here",
    "Issuer": "OmniLibrary",
    "Audience": "OmniLibraryUsers"
  }
}
```

Use separate databases for each microservice.

Recommended database names:

```text
OmniLibrary_UserDb
OmniLibrary_AuthorDb
OmniLibrary_BookDb
```

> **Important:** Replace the example JWT secret with a strong secret key. Do not commit production secrets or sensitive connection strings to source control.

---

## Database Architecture

OmniLibrary uses separate database contexts for its microservices.

```text
                    ┌──────────────────────┐
                    │   Razor Pages UI     │
                    │   Frontend Client    │
                    │  https://localhost:5001
                    └──────────┬───────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
              ▼                ▼                ▼
     ┌────────────────┐ ┌────────────────┐ ┌────────────────┐
     │  UserService   │ │ AuthorService  │ │  BookService   │
     │ :7175          │ │ :7183          │ │ :7265          │
     └───────┬────────┘ └───────┬────────┘ └───────┬────────┘
             │                  │                  │
             ▼                  ▼                  ▼
     ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
     │ User Database│    │Author Database│    │ Book Database│
     │ SQL Server   │    │ SQL Server   │    │ SQL Server   │
     └──────────────┘    └──────────────┘    └──────────────┘
```

This separation keeps each service independently responsible for its own domain and data.

---

## Apply Database Migrations

Run the Entity Framework Core database migrations for each microservice.

### User Service

```bash
cd Services/UserServices/UserServices
dotnet ef database update
```

### Author Service

From the project root:

```bash
cd Services/AuthorService/AuthorService
dotnet ef database update
```

### Book Service

From the project root:

```bash
cd Services/BookService/BookService
dotnet ef database update
```

After running these commands, the required databases and tables should be created in the configured SQL Server instance.

---

## Running the System Locally

OmniLibrary consists of **four applications** that should be running simultaneously:

1. User Service
2. Author Service
3. Book Service
4. Razor Pages Frontend

Open four separate terminal windows.

---

### 1. User Service

**Terminal 1:**

```bash
cd Services/UserServices/UserServices
dotnet run --urls "https://localhost:7175"
```

User Service will be available at:

```text
https://localhost:7175
```

API base route:

```text
/api/users
```

---

### 2. Author Service

**Terminal 2:**

```bash
cd Services/AuthorService/AuthorService
dotnet run --urls "https://localhost:7183"
```

Author Service will be available at:

```text
https://localhost:7183
```

API base route:

```text
/api/authors
```

---

### 3. Book Service

**Terminal 3:**

```bash
cd Services/BookService/BookService
dotnet run --urls "https://localhost:7265"
```

Book Service will be available at:

```text
https://localhost:7265
```

API base route:

```text
/api/books
```

---

### 4. Frontend Client

**Terminal 4:**

```bash
cd FrontEnd/FrontEnd
dotnet run --urls "https://localhost:5001"
```

The Razor Pages frontend will be available at:

```text
https://localhost:5001
```

Open the frontend URL in your browser:

```text
https://localhost:5001
```

---

## Service Endpoints

Once all services are running, the local architecture is:

| Component | URL |
|---|---|
| Frontend | `https://localhost:5001` |
| User Service | `https://localhost:7175` |
| Author Service | `https://localhost:7183` |
| Book Service | `https://localhost:7265` |

---

## Authentication

OmniLibrary uses **JSON Web Tokens (JWT)** for authentication.

The User Service is responsible for:

- User registration
- User login
- JWT token generation
- User profile management
- User authentication

The JWT configuration is defined in the User Service configuration:

```json
{
  "JwtSettings": {
    "Secret": "your_jwt_secret_key_here",
    "Issuer": "OmniLibrary",
    "Audience": "OmniLibraryUsers"
  }
}
```

Protected API requests can use the generated JWT token for authentication and authorization.

---

## Core Functionality

### User Management

- User registration
- User authentication
- JWT-based authorization
- User profile management
- User CRUD operations

### Author Management

- Create authors
- View author records
- Update author information
- Delete authors
- Manage author metadata

### Book Management

- Create and manage books
- Browse the book catalog
- Track inventory
- Manage book information
- Support circulation-related operations

### Frontend

The Razor Pages frontend provides a centralized interface for interacting with the different microservices.

---

## Microservices Benefits

The OmniLibrary architecture separates the system into independent services based on business domains.

### Independent Services

Each service is responsible for a specific domain:

- **UserService** → Users and authentication
- **AuthorService** → Authors and metadata
- **BookService** → Books and inventory

### Independent Databases

Each service maintains its own database context, reducing direct coupling between services.

### RESTful Communication

The frontend communicates with backend services through HTTP-based REST APIs.

### Scalability

Individual services can be developed, maintained, and scaled independently as the application grows.

---

## Troubleshooting

### SQL Server Connection Issues

If the application cannot connect to SQL Server, check:

- SQL Server is running.
- The connection string is correct.
- The database server name is correct.
- Windows Authentication or SQL Authentication is configured correctly.
- `TrustServerCertificate=True` is included when required for local development.

### HTTPS Certificate Issues

If ASP.NET Core reports an HTTPS development certificate error, run:

```bash
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Then restart the services.

### Migration Issues

Make sure you are running `dotnet ef database update` from the correct service directory and that the EF Core CLI tool is installed:

```bash
dotnet ef --version
```

---

## License

This project is licensed under the [MIT License](https://opensource.org/license/mit/).

---

## Author

**Ali Imran**

OmniLibrary is a distributed library management system designed to demonstrate **ASP.NET Core microservices, RESTful APIs, Entity Framework Core, SQL Server, JWT authentication, and Razor Pages** in a complete full-stack application.
