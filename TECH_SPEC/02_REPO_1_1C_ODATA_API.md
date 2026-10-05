# РЕПОЗИТОРИЙ 1: 1C ODATA API
## Техническое задание для сессии "Роль 1"

### 🎯 КОНТЕКСТ И ЦЕЛЬ
Flask-сервис для синхронизации данных из 1С через OData. Должен работать как в составе общей системы, так и автономно (как универсальный коннектор/маппер данных) со встроенным легким UI.

### 🛠 СТЕК
- Backend: Flask + SQLAlchemy + PostgreSQL + APScheduler
- Admin UI: Flask-Admin (кастомизированный)
- Структура: Feature-based (`core/`, `connections/`, `mappings/`, `sync/`, `data_access/`, `admin_ui/`)

### 🗄 МОДЕЛИ БД (SQLAlchemy + Alembic)
1. **Connection**: `id`, `name`, `base_url`, `username`, `password_encrypted` (AES-256), `sync_interval` (cron), `last_sync_at`, `is_active`
2. **EntityMapping**: `id`, `connection_id`, `entity_name_1c`, `entity_name_local`, `storage_type` ('normalized' | 'jsonb'), `key_fields` (JSONB), `created_at`
3. **FieldMapping**: `id`, `entity_mapping_id`, `field_name_1c`, `field_name_local`, `field_type`, `is_primary_key`
4. **SyncLog**: `id`, `connection_id`, `entity_mapping_id`, `started_at`, `finished_at`, `status`, `records_processed`, `errors` (JSONB)
5. **Data (Гибридное хранение)**:
   - *Normalized*: Динамические таблицы с колонками под основные поля + `guid` (PK), `is_deleted` (bool), `synced_at`
   - *JSONB*: Единая таблица `data_jsonb` с `entity_mapping_id`, `guid`, `payload` (JSONB), `is_deleted`, `synced_at`

### ⚙️ БИЗНЕС-ЛОГИКА
- **Синхронизация**: Upsert по GUID (`INSERT ... ON CONFLICT`), Soft Delete (`is_deleted = true`)
- **Адаптивность**: Система анализирует частоту запросов к полям в JSONB и предлагает миграцию в отдельные колонки (через UI)
- **Автономный UI (Flask-Admin)**:
  - Dashboard: виджеты статуса подключений, последней синхронизации, счетчики записей, графики активности
  - Custom Actions: кнопка "Тестировать подключение" в списке, кнопка "Запустить синхронизацию" для маппинга
  - Smart Forms: динамические dropdown для выбора полей из 1С при создании маппинга

### 🔌 API ЭНДПОИНТЫ (REST, `/api/v1/`)
- `POST /connections` — создать подключение
- `GET /connections` — список подключений
- `POST /connections/{id}/sync` — запустить синхронизацию вручную
- `GET /data/{entity_mapping_id}` — получить данные (пагинация, фильтры)
- `GET /sync-logs` — логи синхронизации

### 🧪 ТРЕБОВАНИЯ К ТЕСТИРОВАНИЮ (TDD)
- **Unit**: Парсинг OData-ответов, логика шифрования/дешифрования AES-256
- **Integration**: Mock 1C OData-сервера, проверка upsert и soft delete
- **Property-based (hypothesis)**: Валидация маппинга полей и генерации динамических SQL-запросов

### 📝 ПОРЯДОК РАЗРАБОТКИ
1. **Модуль `core`**: config, database, exceptions, Flask-Admin setup
2. **Модуль `connections`**: models, schemas, services, routes, admin views
3. **Модуль `mappings`**: models, schemas, services, routes, admin views
4. **Модуль `sync`**: odata_client, tasks, services
5. **Модуль `data_access`**: динамические модели, query builder, routes
6. **Модуль `admin_ui`**: dashboard, custom actions, smart forms

### 🚀 ПЕРВЫЙ ШАГ ДЛЯ СЕССИИ
Начни с модуля `core` (конфигурация из `.env`, инициализация SQLAlchemy, кастомные исключения, настройка Flask-Admin). 
**Действие**: Предложи структуру файлов для модуля `core` и дождись подтверждения перед написанием кода.