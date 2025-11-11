# Dutch Rose CMS (Content Management Service)

## Introduction

A Content Management System for managing applications, templates, projects, and content delivery via AWS S3.

## Prerequisites

- Docker (version 20.10 or higher)
- Docker Compose (version 1.29 or higher)

## Installation

### 1. Fetch the Docker Image from the DockerHub

WIP: You can fetch the Docker Image from the DockerHub

### 2. Build from Git

Clone the repository:

```bash
git clone https://github.com/Dutch-Rose-Media/CMS.git
cd CMS
```

Then follow the [Setup Instructions](#setup-instructions) below.

## Setup Instructions

### 1. Create your own .env file

For more info check the [Environment Variables Reference](#environment-variables-reference)

- Copy `.env.example` to `.env`
- Set `AWS_ACCESS_KEY_ID` in `.env`
- Set `AWS_SECRET_ACCESS_KEY` in `.env`
- Set a strong `DB_PASSWORD` in `.env`
- Configure `AWS_REGION` if not using us-east-1
- Update `CORS_ORIGINS` with your actual domains

### 2. Generate Secret Key Base

Generate a secure secret key base:

```bash
# If you have Ruby installed locally:
rake secret

# Or use openssl:
openssl rand -hex 64
```

Add this to your `.env` file as `SECRET_KEY_BASE`.

??? info "AWS Configuration"
    - Verify AWS credentials have S3 access permissions
    - Confirm S3 buckets exist and are accessible
    - Check IAM policy allows required S3 operations (PutObject, GetObject, DeleteObject, ListBucket)
    - Verify AWS region matches your S3 bucket locations

### 3. File Permissions
- `docker-setup.sh` is executable (`chmod +x docker-setup.sh`)

??? info "Git Configuration"
    - Verify `.env` is in `.gitignore`
    - Verify `config/database.yml` is in `.gitignore` (if not using the template)
    - Never commit actual credentials to version control

### 4. Setup

#### Easy setup
```bash
# Run automated setup
./docker-setup.sh
```

#### Normal setup

```bash
# Build docker service
docker-compose -f docker-compose.yml build

# Start services
docker-compose -f docker-compose.yml up -d

# Check logs
docker-compose -f docker-compose.yml logs -f
```

### 5. Database Management (Not Needed)

```bash
# Create database
docker-compose exec web bundle exec rake db:create

# Run migrations
docker-compose exec web bundle exec rake db:migrate

# Rollback migration
docker-compose exec web bundle exec rake db:rollback

# Reset database (!!! WARNING: deletes all data)
docker-compose exec web bundle exec rake db:reset

# Access MySQL console
docker-compose exec db mysql -u root -p
```

## Environment Variables Reference

### Required Variables

These variables must be set by the developer.

| Variable | Description | Example |
|----------|-------------|---------|
| `AWS_ACCESS_KEY_ID` | AWS Access Key for S3 access | `AAAAAAAAADNN7EXAMPLE` |
| `AWS_SECRET_ACCESS_KEY` | AWS Secret Access Key | `wJalgXUtnFEiI/K7MDEwG/bPxRfxCYEEAGPLEKEY` |
| `SECRET_KEY_BASE` | Rails secret key for sessions | Generate with `rake secret` |
| `DB_PASSWORD` | MySQL root password | `secure_password_123` |
| `CORS_ORIGINS` | Allowed CORS origins (comma-separated) | `http://localhost:3000,https://development.dutchrosemedia.com` |

### Optional Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `DB_NAME` | Database name | `dutchrose_cms` |
| `DB_USERNAME` | Database username | `dutchrose` |
| `DB_HOST` | Database host | `db` |
| `DB_PORT` | Database port | `3306` |
| `DB_ROOT_PASSWORD` | Database root password | `rootpassword` |
| `SESSION_KEY` | Session Configuration | `_dutchrose_cms_session` |
| `RAILS_ENV` | Rails environment (development or production) | `development` |
| `AWS_REGION` | AWS Region for S3 | `us-east-1` |
| `RAILS_SERVE_STATIC_FILES` | Serve static files from the `/public` folder | `true` |
| `RAILS_LOG_TO_STDOUT` | Log to standard output | `true` |

## Q&A & Precautions

!!! Danger "Be careful!"
    - **Never commit `.env` files to version control**
    - Rotate AWS credentials regularly
    - Use IAM roles with minimal required permissions
    - Keep `SECRET_KEY_BASE` secret and unique per environment
    - Use strong database passwords
    - Enable SSL/TLS for production deployments

??? question "Common Issues and Solutions"
    **Issue: Permission denied errors**

    Solution:

    - Make scripts executable: `chmod +x docker-setup.sh docker-entrypoint.sh`
    - Check Docker socket permissions
    - Try running with sudo (not recommended for production)

    **Issue: Port 3000 already in use**

    Solution:

    - Stop conflicting service
    - Or change port in docker-compose.yml: `ports: - "8080:3000"`

    **Issue: Complete Reset (Development Only - DESTROYS DATA)**
    
    Solution:

    ```bash
    docker-compose down -v
    docker-compose build --no-cache
    ./docker-setup.sh
    ```