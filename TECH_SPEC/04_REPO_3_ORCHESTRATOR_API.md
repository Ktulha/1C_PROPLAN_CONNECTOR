# РЕПОЗИТОРИЙ 3: ORCHESTRATOR API
## Техническое задание для сессии "Роль 3"

### 🎯 КОНТЕКСТ И ЦЕЛЬ
Flask-сервис для агрегации данных из Repo 1 и Repo 2, трансформации по правилам и сопоставления сущностей между системами.

### 🛠 СТЕК
- Backend: Flask + SQLAlchemy + PostgreSQL + pandas
- Структура: Feature-based (`core/`, `rules/`, `entity_links/`, `aggregation/`)

### 🗄 МОДЕЛИ БД (SQLAlchemy + Alembic)
1. **TransformationRule**: `id`, `name`, `rule_type` (concat/regex/lookup/conditional), `priority`, `enabled`, `config` (JSONB), `created_at`, `updated_at`
2. **EntityMappingRule**: `id`, `source_entity_type`, `target_entity_type`, `rule_id`, `priority`
3. **NameHistory**: `id`, `entity_type`, `old_name`, `new_name`, `changed_at`
4. **EntityLink (универсальная связь)**: `id`, `source_system` (1c/proplan), `source_entity_type`, `source_guid`, `target_system`, `target_entity_type`, `target_guid`, `confidence_score` (0-1), `verified` (bool), `created_at`, `updated_at`

### ⚙️ БИЗНЕС-ЛОГИКА
- **Применение правил трансформации**: concat, regex, lookup, conditional (конфиги в JSONB)
- **Сопоставление объектов** из 1С и Proplan через EntityLink
- **Агрегация данных** через pandas
- **Прямой доступ к БД API 1 и 2** для тяжелых аналитических задач (read-only)

### 🔌 API ЭНДПОИНТЫ (REST, `/api/v1/`)
- `POST /transformation-rules` — создать правило
- `GET /transformation-rules` — список правил
- `POST /entity-links` — создать связь
- `GET /entity-links?source_guid=...` — найти связанные объекты
- `POST /aggregate` — агрегировать данные

### 🧪 ТРЕБОВАНИЯ К ТЕСТИРОВАНИЮ (TDD)
- **Unit**: Каждый тип правил трансформации (concat, regex, lookup, conditional)
- **Property-based (hypothesis)**: Граничные условия для правил
- **Integration**: Агрегация данных из нескольких источников

### 📝 ПОРЯДОК РАЗРАБОТКИ
1. **Модуль `core`**: config, database, exceptions
2. **Модуль `rules`**: models, schemas, services, executors (concat, regex, lookup, conditional)
3. **Модуль `entity_links`**: models, schemas, services, routes
4. **Модуль `aggregation`**: pandas processors, query builder, routes

### 🚀 ПЕРВЫЙ ШАГ ДЛЯ СЕССИИ
Начни с модуля `core` (конфигурация, SQLAlchemy).
**Действие**: Предложи структуру файлов для модуля `core` и дождись подтверждения.