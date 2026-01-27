# airdb

This repository orchestrates all airdb modules using Docker Compose for local development and deployment.

## 🚀 Quick Start

### 1. Clone all modules

```bash
git clone git@github.com:nsysu-5018/airdb-web.git
git clone git@github.com:nsysu-5018/airdb-airBox.git
git clone git@github.com:nsysu-5018/airdb-aqi.git
git clone git@github.com:nsysu-5018/airdb-TB.git
git clone git@github.com:nsysu-5018/airdb-COPD.git
```

### 2. Configure environment variables

Copy the example environment file and configure as needed:

```bash
export GOOGLE_API_KEY="your_api_key_here"
export MOE_API_KEY="your_api_key_here"
export MYSQL_ROOT_PASSWORD="your_mysql_root_password"
```

### 3. Create Docker volume
Create the required volume for database persistence:
```bash
docker volume create airdb_db
```

### 4. Start the stack

```bash
docker compose up --build
```

### 5. Initialize the database

On first run, you need to set up the database schema and user:

```bash
docker compose exec db mysql -u root -p
```

Then run the following SQL commands:

```sql
CREATE DATABASE web;
USE web;

CREATE TABLE `user` (
  `id` int NOT NULL AUTO_INCREMENT,
  `account` text NOT NULL,
  `password` text NOT NULL,
  `department` text NOT NULL,
  `state` text NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
-- Example insert (replace values as needed)
INSERT INTO user (account, password, department, state) VALUES (?, ?, ?, ?);

CREATE USER 'web'@'%' IDENTIFIED BY 'your_password_here';
GRANT ALL PRIVILEGES ON web.* TO 'web'@'%';
FLUSH PRIVILEGES;
```

Exit the container with `exit`.

Once complete, access the web application at [http://localhost/Disease](http://localhost/Disease)

## 🛠️ Common Commands

### View logs

```bash
# All services
docker compose logs -f

# Specific service
docker compose logs -f <service_name>
```

### Enter a container

```bash
docker compose exec <service_name> bash
```

### Stop the stack

```bash
docker compose down
```

## 📦 Services

| Service | Description | Port |
|---------|-------------|------|
| `web` | AirDB Web Interface | 8080 |
| `model` | AirBox Model Service | 8000 |
| `db` | MySQL Database | 3306 |

## 🔧 Development

### Updating modules

To pull the latest changes for all modules:

```bash
cd airdb-model_airBox && git pull && cd ..
cd airdb-web && git pull && cd ..
```

### Hot reloading

TODO

## 🐛 Troubleshooting

### Database

### Connection issues

Ensure the database is fully initialized before the application starts. Check logs:

```bash
docker compose logs db
```

### User-Related Issues

To view existing MySQL users and their allowed hosts, connect to the MySQL instance and run:

```bash
SELECT user, host FROM mysql.user;
```

### Rebuilding from scratch

```bash
docker compose down -v
docker compose up --build
```

## 📚 Project Structure

```
airdb-stack/
├── airdb-model_airBox/    # AirBox model service
├── airdb-web/             # Web interface
├── docker-compose.yml     # Service orchestration
├── .env.example           # Environment template
└── README.md              # This file
```

## 🤝 Contributing

1. Make changes in the individual module repositories
2. Test changes locally using this stack
3. Submit pull requests to the respective module repos
