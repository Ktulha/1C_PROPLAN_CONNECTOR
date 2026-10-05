# РЕПОЗИТОРИЙ 2: PROPAN API
## Техническое задание для сессии "Роль 2"

### 🎯 КОНТЕКСТ И ЦЕЛЬ
Flask-сервис для получения данных из Proplan через GraphQL, отдача через REST. Должен работать автономно как универсальный коннектор к Proplan со встроенным UI.

### 🛠 СТЕК
- Backend: Flask + SQLAlchemy + PostgreSQL + APScheduler
- GraphQL Client: `gql` или `requests`
- Admin UI: Flask-Admin (кастомизированный)
- Структура: Feature-based (`core/`, `connections/`, `mappings/`, `sync/`, `graphql_client/`, `admin_ui/`)

### 🗄 МОДЕЛИ БД (SQLAlchemy + Alembic)
1. **Connection**: `id`, `name`, `api_url`, `api_token_encrypted` (AES-256), `sync_interval`, `last_sync_at`, `is_active`
2. **EntityMapping**: `id`, `connection_id`, `entity_name_proplan`, `entity_name_local`, `graphql_query_template_id`
3. **FieldMapping**: `id`, `entity_mapping_id`, `field_name_proplan`, `field_name_local`, `field_type`
4. **GraphQLQueryTemplate**: `id`, `name`, `query_text`, `variables_schema` (JSONB)
5. **SyncLog**: `id`, `connection_id`, `entity_mapping_id`, `started_at`, `finished_at`, `status`, `records_processed`, `errors` (JSONB)
6. **Нормализованные таблицы сущностей**: Для каждой сущности (Specification, Operation, Material и т.д.) — отдельная таблица с связями One-to-Many

### ⚙️ БИЗНЕС-ЛОГИКА
- **GraphQL только для чтения** из proplan.work
- **Полная нормализация связей** (родитель → дочерние таблицы)
- **Синхронизация по расписанию и on-demand**
- **Плоские данные предпочтительны**
- **Автономный UI (Flask-Admin)**:
  - Dashboard: статус подключений, счетчики спецификаций/операций/материалов
  - Custom Actions: "Тестировать GraphQL-запрос", "Запустить синхронизацию"
  - Smart Forms: редактор GraphQL-запросов с подсветкой синтаксиса

### 🔌 API ЭНДПОИНТЫ (REST, `/api/v1/`)
- `POST /connections` — создать подключение
- `GET /connections` — список подключений
- `POST /connections/{id}/sync` — запустить синхронизацию
- `GET /specifications/{id}` — спецификация с вложенными операциями и материалами
- `GET /sync-logs` — логи синхронизации

### 🧪 ТРЕБОВАНИЯ К ТЕСТИРОВАНИЮ (TDD)
- **Unit**: GraphQL-парсинг, нормализация связей
- **Integration**: Mock Proplan GraphQL API, проверка One-to-Many связей
- **Property-based (hypothesis)**: Валидация GraphQL-шаблонов

### 📝 ПОРЯДОК РАЗРАБОТКИ
1. **Модуль `core`**: config, database, exceptions, Flask-Admin setup
2. **Модуль `graphql_client`**: client, query builder, response parser
3. **Модуль `connections`**: models, schemas, services, routes, admin views
4. **Модуль `mappings`**: models, schemas, services, routes, admin views
5. **Модуль `sync`**: tasks, services, normalizer
6. **Модуль `admin_ui`**: dashboard, custom actions, GraphQL editor

### 🚀 ПЕРВЫЙ ШАГ ДЛЯ СЕССИИ
Начни с модуля `core` (конфигурация, SQLAlchemy, Flask-Admin).
**Действие**: Предложи структуру файлов для модуля `core` и дождись подтверждения.