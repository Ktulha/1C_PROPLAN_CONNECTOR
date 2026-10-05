# СИСТЕМА ИНТЕГРАЦИИ 1С И PROPLAN С AI-АНАЛИТИКОЙ
## Master Document

### ⚠️ КРИТИЧЕСКИЕ ПРАВИЛА ДЛЯ QWEN CODE

#### 1. ЗАПРЕТ НА ГЕНЕРАЦИЮ ВСЕГО ПРОЕКТА СРАЗУ
**СТРОГО ЗАПРЕЩЕНО:**
- Пытаться написать весь репозиторий за один запрос
- Генерировать все модули, модели, эндпоинты одновременно
- Создавать "скелет" проекта без детализации
- Использовать заглушки (`# TODO`, `pass`, `...`) вместо полной реализации

**ПРИЧИНА:** Проект состоит из 7 репозиториев, каждый содержит десятки модулей. Попытка генерации всего сразу приведет к поверхностной реализации и галлюцинациям.

#### 2. ПРАВИЛА РАБОТЫ В ОДНОЙ СЕССИИ
**Одна сессия = Один модуль (или часть модуля)**

**Формат работы:**
1. ШАГ 1: Обсуждение структуры (файлы, классы, функции)
2. ШАГ 2: Зависимости и интерфейсы (импорты, типы)
3. ШАГ 3: Реализация (TDD: сначала тесты pytest+hypothesis, затем код)
4. ШАГ 4: Проверка качества (Ruff, mypy --strict)
5. ШАГ 5: Завершение модуля (краткое описание, список файлов)

#### 3. ЧЕК-ЛИСТ ГОТОВНОСТИ МОДУЛЯ
- [ ] Все файлы созданы и содержат полную реализацию (без заглушек)
- [ ] Все публичные функции имеют type hints и docstrings (Google Style)
- [ ] Написаны unit-тесты с покрытием >80%
- [ ] Написаны integration-тесты для внешних зависимостей
- [ ] Ruff не выдает ошибок
- [ ] mypy --strict проходит без ошибок
- [ ] Все тесты проходят (`pytest -v`)
- [ ] Код отформатирован (`ruff format`)

#### 4. ПЕРЕДАЧА КОНТЕКСТА МЕЖДУ СЕССИЯМИ
**Формат передачи:**
- Контекст проекта: [Вставь этот Master Document]
- Текущий статус: Репозиторий, завершенные модули, текущий модуль, что сделано, что осталось.
- Следующий шаг: [Опиши, что нужно сделать в этой сессии]

### 🎭 РАСПРЕДЕЛЕНИЕ РОЛЕЙ МЕЖДУ СЕССИЯМИ

**Роль 1: "Repo 1 - 1C OData API"**
- Стек: Flask + SQLAlchemy + PostgreSQL + APScheduler + Flask-Admin
- Зона ответственности: Синхронизация с 1С, гибридное хранение, OData-клиент, автономный UI
- Модули: core, connections, mappings, sync, data_access, admin_ui

**Роль 2: "Repo 2 - Propan API"**
- Стек: Flask + SQLAlchemy + PostgreSQL + APScheduler + Flask-Admin
- Зона ответственности: GraphQL→REST, нормализация связей Proplan, автономный UI
- Модули: core, connections, mappings, sync, graphql_client, admin_ui

**Роль 3: "Repo 3 - Orchestrator API"**
- Стек: Flask + SQLAlchemy + PostgreSQL + pandas
- Зона ответственности: Трансформация данных, сопоставление сущностей, агрегация
- Модули: core, rules, entity_links, aggregation

**Роль 4: "Repo 4 - Frontend"**
- Стек: Vue 3 + Vite + Tailwind + Pinia
- Зона ответственности: UI для всех функций системы, AI-интеграция
- Модули: components, pages, store, router, api-client

**Роль 5: "Repo 5 - BFF (Backend for Frontend)"**
- Стек: FastAPI + async SQLAlchemy + PostgreSQL + Redis + Docker SDK
- Зона ответственности: Агрегация запросов, управление Docker, JWT-проксирование
- Модули: core, auth, aggregation, docker_manager, sql_executor

**Роль 6: "Repo 6 - ML/AI сервис"**
- Стек: FastAPI + Scikit-learn/LightGBM + Sentence Transformers + Celery
- Зона ответственности: Прогнозирование, авто-маппинг, аномалии, рекомендации
- Модули: core, models, training, predictions, suggestions

**Роль 7: "Infrastructure & Integration"**
- Стек: Docker Compose, Nginx/Traefik, Loki, Prometheus
- Зона ответственности: Инфраструктура, сеть, мониторинг, бэкапы
- Модули: docker-compose files, nginx configs, monitoring configs, backup scripts

### 🛠 ТЕХНИЧЕСКИЙ СТЕК (Python-репозитории)
- Язык: Python 3.11+
- Зависимости: `uv` + `pyproject.toml` + `uv.lock`
- Линтеры: Ruff (lint + format + isort)
- Типизация: Строгая (`mypy --strict`) + Google Style docstrings
- Тесты: `pytest` + `pytest-asyncio` + `pytest-cov` + `hypothesis`
- Логирование: `structlog` (JSON для Loki)
- Ошибки: Кастомная иерархия (`AppError`, `ValidationError`, `ExternalAPIError`)
- Структура: Feature-based (папки по фичам: `connections/`, `mappings/`, `sync/`)

### 🏛 АРХИТЕКТУРА И БЕЗОПАСНОСТЬ
- API: REST, URL-версионирование (`/api/v1/`), OpenAPI 3.0 (автогенерация)
- Формат ответа: `{"data": {...}, "meta": {...}, "errors": [...]}`
- БД: PostgreSQL + Alembic (миграции)
- Auth: Access Token (память) + Refresh Token (`HttpOnly`, `Secure`, `SameSite` cookie)
- RBAC: Роль в токене + кэш прав в Redis (TTL 5 мин)
- Шифрование: AES-256 (`cryptography`) для credentials в БД, ключ в `.env`
- Сеть: Гибкий `ALLOWED_ORIGINS` для локальных IP и внутренних доменов
- Инфраструктура: Docker Compose (dev/prod), Loki+Promtail+Grafana, бэкапы (JSON + pg_dump)

### 📦 АВТОНОМНОСТЬ REPO 1 И REPO 2
Репозитории 1 и 2 должны работать как standalone-продукты:
- Встроенный **Flask-Admin** по пути `/admin`
- Dashboard: статус подключений, графики активности, счетчики записей
- Кастомные действия: "Тестировать подключение", "Запустить синхронизацию"
- Умные формы: динамические dropdown для выбора полей из 1С/Proplan
- Нулевая зависимость от Node.js/npm