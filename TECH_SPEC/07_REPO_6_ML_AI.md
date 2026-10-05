# РЕПОЗИТОРИЙ 6: ML/AI СЕРВИС
## Техническое задание для сессии "Роль 6"

### 🎯 КОНТЕКСТ И ЦЕЛЬ
FastAPI-сервис для прогнозирования, автоматического маппинга, анализа аномалий и генерации рекомендаций на основе данных из 1С и Proplan.

### 🛠 СТЕК
- **Backend**: FastAPI + Scikit-learn/LightGBM + Sentence Transformers + Celery
- **Database**: PostgreSQL (метаданные моделей, предсказания, предложения)
- **Структура**: Feature-based (`core/`, `models/`, `training/`, `predictions/`, `suggestions/`)

### 🎯 ЗАДАЧИ ML
1. **Прогнозирование**: спрос, сроки производства, загрузка мощностей
2. **Авто-маппинг**: семантическое сопоставление номенклатуры 1С ↔ Proplan
3. **Анализ аномалий**: отклонения в спецификациях, ошибки в данных
4. **Рекомендации**: оптимизация спецификаций, выбор аналогов

### 🗄 МОДЕЛИ БД (SQLAlchemy + Alembic)
1. **ml_models**: `id`, `name`, `version`, `metrics` (JSONB), `training_date`, `status` (active/archived), `file_path`
2. **ml_predictions**: `id`, `model_id`, `entity_type`, `entity_id`, `prediction`, `confidence_score`, `created_at`
3. **ml_suggestions**: `id`, `model_id`, `suggestion_type`, `source_entity_id`, `target_entity_id`, `confidence_score`, `status` (pending/accepted/rejected), `created_at`
4. **ml_training_log**: `id`, `model_id`, `started_at`, `finished_at`, `status`, `metrics` (JSONB), `data_size`

### ⚙️ БИЗНЕС-ЛОГИКА
- **Стандартные запросы**: через REST API от BFF
- **Массовое обучение**: read-only доступ к БД Orchestrator
- **Гибридный режим**:
  - Уверенность >95%: автоматическое применение (создание EntityLink)
  - Уверенность <95%: сохранение в `ml_suggestions` для ручного подтверждения
- **Настройка**: администратор может отключить автоматические действия через UI
- **Хранение моделей**: файлы `.pkl`/`.joblib` в volume + метаданные в БД
- **Обучение по расписанию** через Celery Beat

### 🔌 API ЭНДПОИНТЫ (REST, `/api/v1/`)
- `POST /predict` — получить предсказание
- `GET /suggestions` — список предложений (pending)
- `POST /suggestions/{id}/accept` — принять предложение
- `POST /suggestions/{id}/reject` — отклонить предложение
- `GET /models` — список моделей
- `POST /models/{id}/retrain` — переобучить модель
- `GET /anomalies` — список аномалий
- `GET /insights` — аналитические отчеты

### 🧪 ТРЕБОВАНИЯ К ТЕСТИРОВАНИЮ (TDD)
- **Unit**: Каждая ML-модель (прогнозирование, маппинг, аномалии)
- **Integration**: Обучение на тестовых данных
- **Property-based (hypothesis)**: Граничные условия для confidence_score

### 📝 ПОРЯДОК РАЗРАБОТКИ
1. **Модуль `core`**: config, database, exceptions
2. **Модуль `models`**: базовые ML-модели (Scikit-learn wrappers)
3. **Модуль `training`**: data loaders, training pipelines, Celery tasks
4. **Модуль `predictions`**: inference engine, confidence calculation
5. **Модуль `suggestions`**: suggestion generator, auto-apply logic
6. **Модуль `api`**: FastAPI routes, request/response schemas

### 🚀 ПЕРВЫЙ ШАГ ДЛЯ СЕССИИ
Начни с модуля `core` (конфигурация, SQLAlchemy).
**Действие**: Предложи структуру файлов для модуля `core` и дождись подтверждения.