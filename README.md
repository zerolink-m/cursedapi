# cursedapi v1.2

**Новичкам:** начните с [краткой спецификации](docs/quickstart.md) — минимальные правила для путей, запросов и ответов со ссылками на полное описание.

Стандарт проектирования REST API: единая структура путей, query-параметров, формата ответов (**CursedJSON**) и системы кодов (`"XXX"` -> HTTP). Версия 1.2 добавляет продвинутый фильтр и необязательную машиночитаемую схему запроса в ответах об ошибках.

## Документация

### Основное

| Файл | Описание |
|------|----------|
| [docs/paths.md](docs/paths.md) | Структура путей: CRUD-объекты, bulk-операции, вложенные ресурсы, кастомные и virtual-эндпоинты, бинарные данные, служебные пути, версионирование |
| [docs/requests.md](docs/requests.md) | Приём данных: авторизация, тело запроса, Content-Type, валидация входа, флаг `partial_allowed` |
| [docs/query.md](docs/query.md) | Query-параметры: сортировка, пагинация, простой поиск, экранирование, `expand`, `partial_allowed` |
| [docs/filter.md](docs/filter.md) | Необязательный продвинутый `filter`: Base64url, язык условий, ограничения и обнаружение поддержки |
| [docs/responses.md](docs/responses.md) | Формат ответов CursedJSON: сценарии (список, одинарный, bulk, ошибки, стриминг, не-JSON) |
| [docs/headers.md](docs/headers.md) | Обязательные и необязательные заголовки, авторизация, `OPTIONS` / `cursed-supported-functions` |
| [docs/schema.md](docs/schema.md) | Схема по методу и шаблону пути: запрос, возможные ответы, вложенные ресурсы |
| [docs/codes.md](docs/codes.md) | Таблица кодов `"XXX"` с маппингом на HTTP и зарезервированными диапазонами |

### Объекты (схемы CursedJSON)

| Объект | Файл | Описание |
|--------|------|----------|
| Envelope object | [docs/objects/envelope.md](docs/objects/envelope.md) | Корневой конверт любого JSON-ответа |
| Data object | [docs/objects/data.md](docs/objects/data.md) | Полезная нагрузка: список, одинарный, bulk, virtual |
| Settings object | [docs/objects/settings.md](docs/objects/settings.md) | Эхо query-параметров и `total` только для обычного списка коллекции |
| Bulk object | [docs/objects/bulk.md](docs/objects/bulk.md) | Результат частичной bulk-операции: `succeeded` / `failed` |
| Error object | [docs/objects/error.md](docs/objects/error.md) | Детали ошибки: `error`, `fields`, `query`, `path`, `headers`, `hint`, `docs` |
| Field object | [docs/objects/field.md](docs/objects/field.md) | Ошибка валидации одного поля: типы, длины, диапазоны, enum, pattern, nested, items |
| Metadata object | [docs/objects/metadata.md](docs/objects/metadata.md) | Служебная информация: request_id, пагинация, warnings |
| Request object | [docs/objects/request.md](docs/objects/request.md) | Конверт тела запроса: `data` + `options` |
| Options object | [docs/objects/options.md](docs/objects/options.md) | Опции операции: `partial_allowed`, кастомные флаги сервисов |

## Быстрый пример

Запрос:

```
GET /v1/users?search=john,doe&search_column=name,surname&search_method=exactly,include&limit=10
```

Ответ (HTTP 200):

```json
{
    "code": "000",
    "code_detail": "Success.",
    "error": {},
    "metadata": {},
    "data": {
        "settings": {
            "total": 42,
            "limit": 10,
            "offset": 0,
            "order": "asc",
            "sort_by": null,
            "search": "john,doe",
            "search_method": "exactly,include",
            "search_column": "name,surname",
            "filter": null
        },
        "users": [
            {
                "id": 1,
                "name": "john",
                "surname": "doe",
                "added_at": 123456789
            }
        ]
    }
}
```

## Принципы

1. **Единый конверт** - любой JSON-ответ обёрнут в [Envelope object](docs/objects/envelope.md) (`code`, `code_detail`, `error`, `metadata`, `data`).
2. **Коды вместо догадок** - фронтенд определяет сценарий по строковому коду `"XXX"`, а не парсит тексты ошибок.
3. **Однородные пути** - одинаковая структура для всех CRUD-объектов.
4. **Два уровня поиска** - простой поиск объединяет условия через AND; продвинутый [filter](docs/filter.md) поддерживает скобки, AND/OR/NOT, сравнения, диапазоны и наборы значений. Он выключен по умолчанию.
5. **Приём данных** - тело запроса в [Request object](docs/objects/request.md): `data` + `options` (флаг `partial_allowed`, кастомные опции любого сервиса).
6. **Интроспекция** - `OPTIONS` + `cursed-supported-functions` сообщают, что поддерживает эндпоинт; неподдерживаемая функция - код `153`.
7. **Ограничения сервера** - лимит запросов, bulk и другие возможности сервер объявляет заголовками; отключённая функция возвращает значение `off`.
8. **Помощь при ошибках** - сервер может вернуть схему и пример ожидаемого тела запроса в [Error object](docs/objects/error.md), не раскрывая недоступные поля.
