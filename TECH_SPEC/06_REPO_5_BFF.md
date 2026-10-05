# РЕПОЗИТОРИЙ 5: BACKEND FOR FRONTEND (BFF)
## Техническое задание для сессии "Роль 5"

### 🎯 КОНТЕКСТ И ЦЕЛЬ
FastAPI-сервис для агрегации запросов от UI, управления Docker-контейнерами, кэширования и проксирования JWT.

### 🛠 СТЕК
- **Backend**: FastAPI + async SQLAlchemy + PostgreSQL + Pydantic v2 + Redis
- **Docker**: Docker SDK (через `/var/run/docker.sock`)
- **Структура**: Feature-based (`core/`, `auth/`, `aggregation/`, `docker_manager/`, `sql_executor/`)

### 🗄 МОДЕЛИ БД (SQLAlchemy + Alembic)
1. **cache_entries**: `key`, `value` (JSONB), `ttl`, `created_at`
2. **user_sessions**: `user_id`, `token_hash`, `expires_at`
3. **ui_settings**: `user_id`, `key`, `value`
4. **query_history**: `user_id`, `query`, `execution_time`, `created_at`
5. **export_jobs**: `user_id`, `entity`, `format`, `status`, `file_path`, `created_at`, `finished_at`
6. **dashboard_widgets**: `user_id`, `widget_type`, `config` (JSONB), `position`

### ⚙️ БИЗНЕС-ЛОГИКА
- **Агрегация данных** из API 1, 2, 3, 6 (ML) через `asyncio.gather`
- **Проксирование JWT** (обновление Access/Refresh токенов)
- **Управление Docker-контейнерами** через `/var/run/docker.sock`
- **Кеширование** частых запросов в Redis
- **Строгая валидация SQL** (только SELECT, запрет DROP/DELETE/UPDATE/INSERT/ALTER/TRUNCATE)
- **RBAC с кэшем прав** в Redis (TTL 5 мин)

### 🔌 API ЭНДПОИНТЫ (REST, `/api/v1/`)
- `POST /auth/login` — получить токены
- `POST /auth/refresh` — обновить Access Token
- `GET /bff/dashboard` — агрегированные данные для Dashboard
- `GET /bff/nomenclature/{id}/full` — полная номенклатура из 1С + Proplan
- `POST /bff/sync/all` — запустить синхронизацию во всех API
- `GET /bff/export/{entity}` — экспорт данных
- `GET /docker/containers` — список контейнеров
- `POST /docker/containers/{id}/restart` — перезапуск контейнера
- `POST /sql/execute` — выполнение SQL (только SELECT)

### 🧪 ТРЕБОВАНИЯ К ТЕСТИРОВАНИЮ (TDD)
- **Unit**: Агрегация данных, валидация SQL
- **Integration**: Docker API, Redis-кэш
- **Property-based (hypothesis)**: Валидация SQL-запросов (генерация случайных SQL и проверка, что пропускается только SELECT)

### 📝 ПОРЯДОК РАЗРАБОТКИ
1. **Модуль `core`**: config, database, exceptions, Redis setup
2. **Модуль `auth`**: JWT handling, RBAC, user sessions
3. **Модуль `aggregation`**: API clients (1, 2, 3, 6), data merger
4. **Модуль `docker_manager`**: Docker SDK wrapper, container operations
5. **Модуль `sql_executor`**: SQL parser, validator, executor
6. **Модуль `cache`**: Redis wrapper, TTL management

### 🚀 ПЕРВЫЙ ШАГ ДЛЯ СЕССИИ
Начни с модуля `core` (конфигурация, async SQLAlchemy, Redis).
**Действие**: Предложи структуру файлов для модуля `core` и дождись подтверждения.