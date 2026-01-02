# Acta - Invoice Management System

A full-stack invoice management application built with NestJS (backend) and React + Vite (frontend).

## Table of Contents

- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Local Development Setup](#local-development-setup)
- [Docker Setup (Production)](#docker-setup-production)
- [Database Setup](#database-setup)
- [Running the Application](#running-the-application)
- [Troubleshooting](#troubleshooting)

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** 20.11.1 (or compatible version)
- **npm** (comes with Node.js)
- **PostgreSQL** 16+ (for local development) or Docker (for containerized setup)
- **Docker** and **Docker Compose** (for production deployment)
- **Git** (for cloning and managing submodules)

## Project Structure

This repository uses Git submodules for the backend and frontend:

```
acta/
├── backend/          # NestJS backend API (submodule)
├── frontend/         # React + Vite frontend (submodule)
├── docker-compose.yml # Production Docker Compose configuration
└── README.md         # This file
```

## Getting Started

### Cloning the Repository

Since this repository uses submodules, you need to clone it with the `--recursive` flag:

```bash
git clone --recursive <repository-url>
cd acta
```

If you've already cloned the repository without submodules, initialize them:

```bash
git submodule init
git submodule update
```

Or in one command:

```bash
git submodule update --init --recursive
```

### Updating Submodules

To update the submodules to their latest commits:

```bash
git submodule update --remote
```

## Environment Variables

### Backend Environment Variables

Create a `.env` file in the `backend/` directory with the following variables:

```env
# Database Configuration
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/acta_db
# OR use individual connection parameters:
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=acta_db

# Application Configuration
NODE_ENV=development
PORT=3000
HOST=0.0.0.0

# JWT Configuration
JWT_SECRET=your_jwt_secret_key_here_minimum_32_characters

# Optional: Schema name (defaults to 'public' or 'acta_foughtsave' in development)
SCHEMA_NAME=public
```

### Frontend Environment Variables

Create a `.env` file in the `frontend/` directory (optional for local development):

```env
# API URL (optional - defaults to dynamic detection based on hostname)
VITE_API_URL=http://localhost:3000
```

### Docker Environment Variables

For Docker Compose, create a `.env` file in the root directory:

```env
# Database Configuration
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_secure_password_here
POSTGRES_DB=acta_db
POSTGRES_PORT=5432

# Backend Configuration
BACKEND_PORT=3000
JWT_SECRET=your_jwt_secret_key_here_minimum_32_characters
NODE_ENV=production

# Frontend Configuration
FRONTEND_PORT=80
VITE_API_URL=http://localhost:3000
```

**Important Security Notes:**
- Use strong, unique passwords for `POSTGRES_PASSWORD` in production
- Use a strong, random `JWT_SECRET` (at least 32 characters) in production
- Update `VITE_API_URL` to match your production backend URL
- Never commit `.env` files to version control

## Local Development Setup

### 1. Backend Setup

```bash
cd backend

# Install dependencies
npm install

# Create .env file (see Environment Variables section above)
# Make sure to configure your database connection

# Start PostgreSQL (if not using Docker)
# Make sure PostgreSQL is running on localhost:5432

# Run database migrations
npm run migration:run

# (Optional) Seed the database
npm run seed

# Start the development server
npm run start:dev
```

The backend will be available at `http://localhost:3000`

### 2. Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# (Optional) Create .env file with VITE_API_URL
# For local development, the frontend auto-detects the backend URL

# Start the development server
npm run dev
```

The frontend will be available at `http://localhost:5173` (or the port Vite assigns)

### 3. Database Setup (Local PostgreSQL)

If you're using a local PostgreSQL instance:

```bash
# Create the database
createdb acta_db

# Or using psql:
psql -U postgres -c "CREATE DATABASE acta_db;"
```

Then run migrations from the backend directory:

```bash
cd backend
npm run migration:run
```

## Docker Setup (Production)

### Quick Start with Docker Compose

1. **Create environment file:**

   Create a `.env` file in the root directory with your production values (see [Environment Variables](#environment-variables) section).

2. **Build and start all services:**

   ```bash
   docker-compose up -d --build
   ```

   This will:
   - Build the backend and frontend Docker images
   - Start PostgreSQL database
   - Start the backend API
   - Start the frontend (served via nginx)

3. **Run database migrations:**

   ```bash
   # Enter the backend container
   docker exec -it acta_backend_prod sh

   # Run migrations
   npm run migration:run

   # (Optional) Seed the database
   npm run seed

   # Exit the container
   exit
   ```

4. **Access the application:**

   - Frontend: `http://localhost` (or the port specified in `FRONTEND_PORT`)
   - Backend API: `http://localhost:3000` (or the port specified in `BACKEND_PORT`)

### Docker Commands

```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs -f

# View logs for a specific service
docker-compose logs -f backend
docker-compose logs -f frontend
docker-compose logs -f postgres

# Rebuild services
docker-compose up -d --build

# Stop and remove volumes (WARNING: This deletes database data)
docker-compose down -v

# Execute commands in a container
docker exec -it acta_backend_prod sh
docker exec -it acta_postgres_prod psql -U postgres -d acta_db
```

## Database Setup

### Running Migrations

Migrations are located in `backend/src/database/migrations/`. To run them:

**Local development:**
```bash
cd backend
npm run migration:run
```

**Docker:**
```bash
docker exec -it acta_backend_prod npm run migration:run
```

### Reverting Migrations

```bash
# Local
cd backend
npm run migration:revert

# Docker
docker exec -it acta_backend_prod npm run migration:revert
```

### Seeding the Database

```bash
# Local
cd backend
npm run seed

# Docker
docker exec -it acta_backend_prod npm run seed
```

### Database Schema

The application uses PostgreSQL with the following main entities:
- **Users** - System users with roles (super_admin, admin, user)
- **Tenants** - Multi-tenant organization support
- **Companies** - Company information with logos
- **Articles** - Products/services that can be invoiced
- **Invoices** - Invoice records with items
- **InvoiceItems** - Individual line items on invoices

## Running the Application

### Development Mode

**Backend:**
```bash
cd backend
npm run start:dev
```

**Frontend:**
```bash
cd frontend
npm run dev
```

### Production Mode

**Using Docker (Recommended):**
```bash
docker-compose up -d --build
```

**Manual (Local):**
```bash
# Backend
cd backend
npm run build
npm run start:prod

# Frontend
cd frontend
npm run build
npm run preview
```

## Troubleshooting

### Submodule Issues

**Submodules not initialized:**
```bash
git submodule update --init --recursive
```

**Submodules out of sync:**
```bash
git submodule update --remote
```

**Working on submodule changes:**
```bash
cd backend  # or frontend
# Make your changes
git add .
git commit -m "Your changes"
git push
cd ..
git add backend  # or frontend
git commit -m "Update submodule"
```

### Backend Issues

**Database Connection Errors:**
- Verify PostgreSQL is running
- Check `DATABASE_URL` or individual database credentials in `.env`
- Ensure the database exists: `createdb acta_db`
- Check network connectivity if using Docker

**Migration Errors:**
- Ensure database is accessible
- Check that previous migrations completed successfully
- Review migration files for syntax errors

**Port Already in Use:**
- Change `PORT` in `.env` or use a different port
- Kill the process using the port: `lsof -ti:3000 | xargs kill`

### Frontend Issues

**API Connection Errors:**
- Verify backend is running on the expected port
- Check `VITE_API_URL` in frontend `.env` (if set)
- For local development, the frontend auto-detects the backend URL
- Check CORS settings in backend if accessing from different origin

**Build Errors:**
- Clear node_modules and reinstall: `rm -rf node_modules && npm install`
- Check Node.js version matches requirements (20.11.1)

### Docker Issues

**Container Won't Start:**
- Check logs: `docker-compose logs [service-name]`
- Verify environment variables are set correctly
- Ensure ports are not already in use
- Check Docker has enough resources allocated

**Database Connection from Backend Container:**
- Use service name `postgres` as hostname (not `localhost`)
- Verify `DATABASE_URL` uses the correct service name
- Check network connectivity: `docker network inspect acta_acta_network`

**Volume Permission Issues:**
- Ensure uploads and logs directories exist and have correct permissions
- Check volume mounts in docker-compose.yml

### Common Solutions

**Reset Everything (Development):**
```bash
# Stop all containers
docker-compose down -v

# Remove node_modules from submodules
rm -rf backend/node_modules frontend/node_modules

# Reinstall dependencies
cd backend && npm install
cd ../frontend && npm install

# Rebuild and start
cd ..
docker-compose up -d --build
```

**Clear Database and Start Fresh:**
```bash
# Stop containers
docker-compose down -v

# Remove database volume
docker volume rm acta_postgres_data

# Start fresh
docker-compose up -d --build
docker exec -it acta_backend_prod npm run migration:run
docker exec -it acta_backend_prod npm run seed
```

## Additional Resources

- **Backend API Documentation:** Available at `http://localhost:3000/api` (if Swagger is enabled)
- **Database Migrations:** Located in `backend/src/database/migrations/`
- **Database Seeds:** Located in `backend/src/database/seeds/`

## Support

For issues or questions, please check the logs:
- Backend logs: `backend/logs/` directory or `docker-compose logs backend`
- Frontend logs: Browser console or `docker-compose logs frontend`
- Database logs: `docker-compose logs postgres`

