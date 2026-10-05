# INFRASTRUCTURE & INTEGRATION
## Техническое задание для сессии "Роль 7"

### 🎯 КОНТЕКСТ И ЦЕЛЬ
Настройка Docker Compose, сети, мониторинга, бэкапов и интеграции всех 6 репозиториев в единую систему.

### 🛠 СТЕК
- **Containerization**: Docker + Docker Compose
- **Reverse Proxy**: Nginx/Traefik
- **Monitoring**: Promtail + Loki + Grafana
- **Backup**: pg_dump + JSON export scripts

### 📦 DOCKER COMPOSE СТРУКТУРА

**docker-compose.dev.yml:**
- **Сеть**: `app-network` (единая для всех сервисов)
- **Сервисы**:
  - `postgres` (PostgreSQL 15+)
  - `redis` (Redis 7+)
  - `repo1-1c-api` (Flask, порт 5001)
  - `repo2-propan-api` (Flask, порт 5002)
  - `repo3-orchestrator` (Flask, порт 5003)
  - `repo5-bff` (FastAPI, порт 5005)
  - `repo6-ml-service` (FastAPI, порт 5006)
  - `repo4-frontend` (Vue, порт 3000)
  - `loki` (логи)
  - `promtail` (сбор логов)
  - `grafana` (визуализация)

**docker-compose.prod.yml:**
- Те же сервисы + reverse proxy (Nginx/Traefik)
- HTTPS termination
- Resource limits
- Health checks

### 🔧 КОНФИГУРАЦИЯ

**Переменные окружения (.env):**
- `POSTGRES_HOST`, `POSTGRES_PORT`, `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`
- `REDIS_HOST`, `REDIS_PORT`
- `JWT_SECRET`, `AES_MASTER_KEY`
- `ALLOWED_ORIGINS` (гибкий список для локальных IP и внутренних доменов)
- `COOKIE_SECURE` (true/false в зависимости от HTTPS)

**Volumes:**
- `postgres_data` (БД)
- `redis_data` (кэш)
- `ml_models` (обученные модели)
- `backups` (бэкапы)
- `logs` (логи приложений)

### 📊 МОНИТОРИНГ

**Promtail + Loki + Grafana:**
- Promtail собирает логи из всех контейнеров
- Loki хранит логи
- Grafana визуализирует (дашборды для каждого сервиса)

**Health Checks:**
- Каждый сервис имеет endpoint `/health`
- Docker Compose проверяет здоровье и перезапускает упавшие контейнеры

### 💾 БЭКАПЫ

**Скрипт backup.sh:**
- `pg_dump` всех БД (postgres_data)
- Экспорт конфигураций в JSON (подключения, маппинги, правила)
- Архивация в `backups/` с датой
- Retention policy: хранить последние 7 daily + 4 weekly

**Скрипт restore.sh:**
- Восстановление БД из pg_dump
- Импорт конфигураций из JSON

### 🔗 ИНТЕГРАЦИЯ СЕРВИСОВ

**BFF (Repo 5) → API 1, 2, 3, 6:**
- HTTP-запросы через `http://repo1-1c-api:5001/api/v1/...`
- Async aggregation через `asyncio.gather`

**Orchestrator (Repo 3) → API 1, 2:**
- Read-only доступ к БД (для тяжелых запросов)
- HTTP-запросы для стандартных операций

**ML Service (Repo 6) → Orchestrator:**
- Read-only доступ к БД (для обучения)
- HTTP-запросы для предсказаний

### 📝 ПОРЯДОК РАЗРАБОТКИ
1. **Docker Compose dev**: базовая структура, сеть, volumes
2. **Docker Compose prod**: reverse proxy, HTTPS, resource limits
3. **Monitoring**: Promtail, Loki, Grafana configs
4. **Backup scripts**: backup.sh, restore.sh
5. **Health checks**: endpoints для каждого сервиса
6. **Integration testing**: проверка взаимодействия всех сервисов

### 🚀 ПЕРВЫЙ ШАГ ДЛЯ СЕССИИ
Начни с `docker-compose.dev.yml` (базовая структура, сеть, volumes).
**Действие**: Предложи структуру файла и дождись подтверждения.