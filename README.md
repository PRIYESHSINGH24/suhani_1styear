# Employer–Worker Registration System

A desktop application built with **Java 17 + Swing** and **PostgreSQL** for managing employers, workers, jobs, work assignments, payments, and invoices.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Database Setup](#database-setup)
- [Windows Quick Start](#windows-quick-start)
- [How to Run](#how-to-run)
- [Default Login](#default-login)
- [Seed Data](#seed-data)

---

## Features

| Module | Description |
|---|---|
| **Employers** | Add, update, search, and view employer profiles with phone numbers |
| **Workers** | Add, update, search workers with IBAN and phone numbers |
| **Jobs** | Create jobs linked to an employer and a price plan |
| **Prices** | Define full-time, half-time, and overtime rates |
| **Work Assignment** | Assign workers to jobs with work type and work group |
| **Worker Payments** | Record payments to workers per job and payment type |
| **Job Payments (Invoices)** | Record invoice payments received for jobs |
| **Pay Types** | Manage payment methods (e.g. Cash, Bank Transfer) |
| **Work Types** | Manage work categories |

---

## Tech Stack

- **Language**: Java 17
- **UI**: Java Swing (null layout)
- **Database**: PostgreSQL 15
- **JDBC Driver**: `postgresql-42.7.5.jar`
- **Build**: Plain `javac` (no Maven or Gradle)

---

## Project Structure

```
employer-worker-registration-system/
├── db/
│   └── init.sql               # Database schema + seed data
├── libs/
│   └── postgresql-42.7.5.jar  # JDBC driver
├── out/                       # Compiled .class files (generated)
├── src/
│   └── com/cbozan/
│       ├── dao/               # Database access layer
│       ├── entity/            # Model classes
│       ├── exception/         # Custom exceptions
│       ├── main/              # Entry point + main window
│       ├── util/              # Constants
│       └── view/              # Swing UI panels
│           ├── add/           # Work/payment entry panels
│           ├── component/     # Reusable UI components
│           ├── display/       # Employer/worker detail views
│           ├── helper/        # Interfaces
│           └── record/        # Record form panels
└── src/icon/                  # Application icons
```

---

## Prerequisites

1. **Java 17+** — verify with:
   ```bash
   java -version
   ```

2. **PostgreSQL 15+** — install via Homebrew (macOS):
   ```bash
   brew install postgresql@15
   brew services start postgresql@15
   ```
   Verify:
   ```bash
   psql --version
   ```

---

## Database Setup

### 1. Create the database user and database

Open a PostgreSQL shell (macOS uses your system username by default):
```bash
psql postgres
```

Run the following SQL:
```sql
CREATE USER "Hesap-eProject" WITH PASSWORD '.hesap-eProject.';
CREATE DATABASE "Hesap-eProject" OWNER "Hesap-eProject";
\q
```

### 2. Create the `auth` table and admin user

```bash
psql -d "Hesap-eProject"
```

```sql
CREATE TABLE IF NOT EXISTS auth (
  id SERIAL PRIMARY KEY,
  username VARCHAR(50) NOT NULL UNIQUE,
  password VARCHAR(255) NOT NULL,
  date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO auth (username, password)
SELECT 'admin', 'admin'
WHERE NOT EXISTS (SELECT 1 FROM auth WHERE username = 'admin');
```

### 3. Run the schema + seed script

```bash
psql -d "Hesap-eProject" -f db/init.sql
```

This creates all 11 tables and inserts default sample data.

---

## Windows Quick Start

Use this section if you want the fastest way to run the project on Windows.

### 1. Install prerequisites

- Install Java 17 or newer.
- Install PostgreSQL 15 or newer.
- Confirm JDBC driver exists: `libs\postgresql-42.7.5.jar`

### 2. Open PowerShell in project folder

In File Explorer, open this project folder and then open PowerShell there.

### 3. Open `psql`

If `psql` is in your PATH:

```powershell
psql -U postgres -d postgres -h localhost -p 5432
```

If not in PATH, use full path (adjust version if needed):

```powershell
& "C:\Program Files\PostgreSQL\15\bin\psql.exe" -U postgres -d postgres -h localhost -p 5432
```

### 4. Create project database and user

Run in `psql`:

```sql
CREATE USER "Hesap-eProject" WITH PASSWORD '.hesap-eProject.';
CREATE DATABASE "Hesap-eProject" OWNER "Hesap-eProject";
\q
```

### 5. Create login table and default admin

```powershell
psql -U postgres -d "Hesap-eProject" -h localhost -p 5432
```

```sql
CREATE TABLE IF NOT EXISTS auth (
   id SERIAL PRIMARY KEY,
   username VARCHAR(50) NOT NULL UNIQUE,
   password VARCHAR(255) NOT NULL,
   date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO auth (username, password)
SELECT 'admin', 'admin'
WHERE NOT EXISTS (SELECT 1 FROM auth WHERE username = 'admin');
\q
```

### 6. Run schema and seed data

```powershell
psql -U postgres -d "Hesap-eProject" -h localhost -p 5432 -f db\init.sql
```

### 7. Compile

```powershell
New-Item -ItemType Directory -Force out | Out-Null
$files = Get-ChildItem -Recurse -Filter *.java src | ForEach-Object { $_.FullName }
javac -d out -cp "libs\postgresql-42.7.5.jar" $files
```

### 8. Run

```powershell
java -cp "out;libs\postgresql-42.7.5.jar" com.cbozan.main.Main
```

### 9. Login

- Username: `admin`
- Password: `admin`

---

## How to Run

### Step 1 — Compile

From the project root directory:

```bash
javac -d out -cp "libs/postgresql-42.7.5.jar" $(find src -name "*.java")
```

> The compiled `.class` files are placed in the `out/` directory.

### Step 2 — Run

```bash
java -cp "out:libs/postgresql-42.7.5.jar" com.cbozan.main.Main
```

> **Windows users**: replace `:` with `;` in the classpath:
> ```cmd
> java -cp "out;libs/postgresql-42.7.5.jar" com.cbozan.main.Main
> ```

---

## Default Login

| Field | Value |
|---|---|
| Username | `admin` |
| Password | `admin` |

---

## Seed Data

The `db/init.sql` script inserts the following default records (only if the tables are empty):

| Table | Default Data |
|---|---|
| `employer` | John Doe |
| `worker` | Jane Smith |
| `price` | Full: ₺1000, Half: ₺500, Overtime: ₺1500 |
| `paytype` | Cash, Bank Transfer |
| `worktype` | Standard |
| `job` | "Sample Job" linked to default employer + price |

---

## Database Connection

The connection settings are hardcoded in `src/com/cbozan/dao/DB.java`:

```
URL:      jdbc:postgresql://localhost:5432/Hesap-eProject
User:     Hesap-eProject
Password: .hesap-eProject.
```

To use a different database, update those values in `DB.java` and recompile.
