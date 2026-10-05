# РЕПОЗИТОРИЙ 4: FRONTEND
## Техническое задание для сессии "Роль 4"

### 🎯 КОНТЕКСТ И ЦЕЛЬ
Vue 3 SPA для управления системой интеграции 1С и Proplan с AI-аналитикой.

### 🛠 СТЕК
- **Framework**: Vue 3 (Composition API) + Vite
- **Styling**: Tailwind CSS / Material Design
- **State Management**: Pinia
- **Router**: Vue Router
- **HTTP Client**: Axios

### 📄 СТРАНИЦЫ (20+)
**Базовые:**
1. Login
2. Dashboard (с AI-виджетами)
3. Connections (CRUD подключений 1С и Proplan)
4. Entity Mappings
5. Field Mappings
6. Transformation Rules
7. Data Explorer (с подсветкой аномалий от ML)
8. Data Detail
9. Sync Logs
10. Settings (включая настройки AI)
11. Users & Roles
12. Schedules
13. Rule Tester
14. Health & Monitoring (Docker)
15. Linked Data Explorer
16. SQL Editor (только SELECT)
17. Backup & Restore

**AI-специфичные:**
18. AI Suggestions (подтверждение/отклонение предложений ML)
19. ML Models (управление моделями, метрики, переобучение)
20. AI Insights (прогнозы, аномалии, рекомендации)

### 🎨 ФУНКЦИИ UI
- **RBAC** (admin/editor/viewer) на клиенте
- **Экспорт данных** (CSV/Excel)
- **Тестирование правил** трансформации
- **Мастер первоначальной настройки** (первый запуск)
- **AI-интеграция**: кнопки авто-маппинга, подсветка аномалий, виджеты прогнозов
- **Настройки AI**: слайдер порога автоматизации (0-100%), тумблер отключения автоматики

### 🗂 КОМПОНЕНТЫ
- Таблицы с пагинацией и фильтрацией
- Формы с валидацией
- Модальные окна
- Графики (Chart.js/ECharts)
- AI-виджеты (предложения, метрики)

### 📦 STORE (Pinia)
- `auth` (токены, пользователь)
- `connections`
- `mappings`
- `rules`
- `ml` (предложения, модели)
- `ui` (настройки)

### 🧪 ТЕСТЫ
- **Component-тесты**: Vitest
- **E2E-тесты**: Cypress/Playwright

### 📝 ПОРЯДОК РАЗРАБОТКИ
1. **Setup**: Vite config, Tailwind, Pinia, Router
2. **Core components**: Tables, Forms, Modals, Layout
3. **Auth**: Login page, auth store, JWT handling
4. **Basic pages**: Dashboard, Connections, Mappings
5. **Advanced pages**: Data Explorer, Rule Tester, SQL Editor
6. **AI integration**: AI Suggestions, ML Models, AI Insights
7. **Admin pages**: Users & Roles, Settings, Health & Monitoring

### 🚀 ПЕРВЫЙ ШАГ ДЛЯ СЕССИИ
Начни с setup проекта (Vite config, Tailwind, Pinia, Router).
**Действие**: Предложи структуру папок и базовые конфиги.