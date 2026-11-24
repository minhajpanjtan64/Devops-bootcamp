# DevOps Bootcamp

A hands-on DevOps bootcamp repository documenting practical work with Linux, Git, GitHub, Docker, Docker Compose, and Laravel application deployment.

## 📌 Project Overview

This repository contains a Laravel Jobs Portal application deployed using Docker and Docker Compose.

The project demonstrates how to:

- Work with Linux commands and server administration.
- Manage source code using Git and GitHub.
- Containerize an application using Docker.
- Run multiple containers using Docker Compose.
- Configure and connect a Laravel application to a MySQL database.
- Execute Laravel database migrations.
- Verify application availability on an Ubuntu server.

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Ubuntu Linux | Server operating system |
| Git | Version control |
| GitHub | Source code hosting |
| Docker | Application containerization |
| Docker Compose | Multi-container management |
| Laravel | PHP application framework |
| PHP | Backend programming language |
| MySQL 8.4 | Database management |
| Apache | Web server |

## 🏗️ Project Architecture

The application uses two main Docker services:

```text
                 User / Browser
                       |
                       | HTTP :80
                       |
                Docker Host (EC2)
                       |
                 Docker Compose
                       |
              +--------+--------+
              |                 |
         Application         Database
           Container          Container
              |                 |
          Laravel / PHP       MySQL 8.4
              |                 |
              +------ Database Connection
```

### Docker Services

**1. Application Container (`app`)**

- Runs the Laravel application.
- Uses PHP and Apache.
- Exposes the application through port 80.
- Handles incoming HTTP requests.

**2. Database Container (`db`)**

- Runs MySQL 8.4.
- Stores application data.
- Provides database connectivity to Laravel.
- Uses a Docker named volume for persistent database storage.

### Why Two Containers?

The application and database have separate responsibilities. Running them independently makes the deployment easier to manage, maintain, and scale.

## 📂 Repository Structure

```text
Devops-bootcamp/
│
└── jobs-portal-laravel/
    │
    ├── app/
    ├── bootstrap/
    ├── config/
    ├── database/
    ├── public/
    ├── resources/
    ├── routes/
    ├── storage/
    ├── tests/
    │
    ├── Dockerfile
    ├── docker-compose.yml
    ├── .env.example
    ├── .gitignore
    ├── artisan
    └── composer.json
```

*The structure above illustrates the expected Laravel project layout. Actual files may vary depending on the project.*

## ⚙️ Prerequisites

Make sure the following tools are installed:

- Ubuntu Linux
- Git
- Docker
- Docker Compose plugin

Check installations:

```bash
git --version
docker --version
docker compose version
```

## 🚀 Deployment Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/minhajpanjtan64/Devops-bootcamp.git
```

```bash
cd Devops-bootcamp/jobs-portal-laravel
```

### 2. Configure Environment Variables

Create your environment file:

```bash
cp .env.example .env
```

Edit the environment configuration:

```bash
nano .env
```

Configure your database credentials according to your Docker Compose configuration.

For a standard Docker Compose network, Laravel should use the database service name:

```env
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=jobs_portal
DB_USERNAME=jobs_user
DB_PASSWORD=your_database_password
```

**Important:** Never upload your actual `.env` file or real credentials to GitHub.

### 3. Build and Start Containers

```bash
docker compose up -d --build
```

This command builds the application image if necessary and starts the application and database containers in detached mode.

### 4. Check Running Containers

```bash
docker compose ps
```

### 5. Run Database Migrations

```bash
docker compose exec app php artisan migrate
```

### 6. Clear Laravel Cache

```bash
docker compose exec app php artisan optimize:clear
```

### 7. Access the Application

Open your EC2 public IP address in your browser:

```text
http://YOUR_EC2_PUBLIC_IP
```

Make sure the EC2 security group allows inbound HTTP traffic on port 80.

## 🐳 Useful Docker Commands

| Command | Description |
|---|---|
| `docker compose up -d` | Start containers |
| `docker compose up -d --build` | Build and start containers |
| `docker compose ps` | List containers |
| `docker compose down` | Stop and remove containers |
| `docker compose restart app` | Restart application |
| `docker compose logs app` | View application logs |
| `docker compose logs db` | View database logs |
| `docker compose exec app bash` | Enter application container |
| `docker compose exec app php artisan --version` | Check Laravel version |
| `docker compose exec app php artisan migrate:status` | Check migrations |

## 🐧 Useful Linux Commands

```bash
pwd                 # Display current directory
ls -la              # List all files
cd directory        # Change directory
mkdir folder        # Create directory
cp file destination # Copy files
mv old new          # Move or rename files
rm file             # Remove a file
sudo apt update     # Update package information
df -h               # Check disk usage
free -h             # Check memory usage
```



- Never commit `.env` files.
- Never upload database passwords or private keys.
- Use `.gitignore` to exclude sensitive and unnecessary files.
- Avoid exposing database ports publicly unless required.
- Use secure credentials for database access.
- Keep Docker images and server packages updated.

## 🎯 Learning Outcomes

Through this project, the following practical skills are demonstrated:

1. Linux command-line operations.
2. Git version control and GitHub repository management.
3. Docker image and container management.
4. Docker Compose multi-container deployment.
5. Laravel application configuration.
6. MySQL database connectivity.
7. Database migrations and application verification.
8. Basic deployment and security practices.

## 👨‍💻 Author

**Syed Minhaj Panjtan**

GitHub: [@minhajpanjtan64](https://github.com/minhajpanjtan64)

---

*Part of my hands-on DevOps Bootcamp learning and practical deployment work.*
