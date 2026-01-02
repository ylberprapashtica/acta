# Acta - Invoice Management System

A full-stack invoice management application built with NestJS (backend) and React + Vite (frontend).

## Development Setup

```bash
# 1. Clone repository with submodules
git clone --recursive <repository-url>
cd acta

# 2. Initialize submodules (if not cloned with --recursive)
git submodule update --init --recursive

# 3. Set up backend environment
cd backend
cp .env.example .env

# 4. Set up frontend environment
cd ../frontend
cp .env.example .env

# 5. Set up root Docker Compose environment
cd ..
cp .env.example .env

# 6. Add DATABASE_URL to backend/.env from root .env
echo "DATABASE_URL=postgresql://$(grep POSTGRES_USER .env | cut -d '=' -f2):$(grep POSTGRES_PASSWORD .env | cut -d '=' -f2)@postgres:5432/$(grep POSTGRES_DB .env | cut -d '=' -f2)" >> backend/.env

# 7. Build and start production services
docker compose -f docker-compose.dev.yml up -d --build

# 8. Run database migrations
docker exec -it acta_backend_dev npm run migration:run

# 9. (Optional) Seed the database
docker exec -it acta_backend_prod npm run seed
```

**Access:**
- Frontend: `http://localhost` (or port specified in FRONTEND_PORT)
- Backend API: `http://localhost:3000` (or port specified in BACKEND_PORT)

## Production Setup

```bash
# 1. Clone repository with submodules
git clone --recursive https://github.com/ylberprapashtica/acta.git
cd acta

# 2. Initialize submodules (if not cloned with --recursive)
git submodule update --init --recursive

# 3. Set up backend environment
cd backend
cp .env.example .env

# 4. Set up frontend environment
cd ../frontend
cp .env.example .env

# 5. Set up root Docker Compose environment
cd ..
cp .env.example .env

# 6. Add DATABASE_URL to backend/.env from root .env
echo "DATABASE_URL=postgresql://$(grep POSTGRES_USER .env | cut -d '=' -f2):$(grep POSTGRES_PASSWORD .env | cut -d '=' -f2)@postgres:5432/$(grep POSTGRES_DB .env | cut -d '=' -f2)" >> backend/.env

# 7. Build and start production services
docker compose -f docker-compose.prod.yml up -d --build

# 8. Run database migrations
docker exec -it acta_backend_prod npm run migration:run

# 9. (Optional) Seed the database
docker exec -it acta_backend_prod npm run seed
```

**Access:**
- Frontend: `http://localhost` (or port specified in FRONTEND_PORT)
- Backend API: `http://localhost:3000` (or port specified in BACKEND_PORT)

## Useful Commands

```bash
# View logs
docker compose -f docker-compose.prod.yml logs -f
docker compose -f docker-compose.dev.yml logs -f

# Stop services
docker compose -f docker-compose.prod.yml down
docker compose -f docker-compose.dev.yml down

# Rebuild services
docker compose -f docker-compose.prod.yml up -d --build
docker compose -f docker-compose.dev.yml up --build

# Execute commands in containers
docker exec -it acta_backend_prod sh
docker exec -it acta_postgres_prod psql -U postgres -d ${POSTGRES_DB}
```
