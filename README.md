# cursedapi v1.1

Стандарт проектирования REST API: единая структура путей, query-параметров, формата ответов (**CursedJSON**) и системы кодов (`"XXX"` -> HTTP).

## Документация

### Основное

| Файл | Описание |
|------|----------|
| [docs/paths.md](docs/paths.md) | Структура путей: CRUD-объекты, bulk-операции, вложенные ресурсы, кастомные и virtual-эндпоинты, служебные пути, версионирование |
| [docs/requests.md](docs/requests.md) | Приём данных: тело запроса, Content-Type, валидация входа, флаг `partial_allowed` |
| [docs/query.md](docs/query.md) | Query-параметры: сортировка, пагинация, поиск, мультипоиск через запятые, `expand` |
| [docs/responses.md](docs/responses.md) | Формат ответов CursedJSON: сценарии (список, одинарный, bulk, ошибки, стриминг, не-JSON) |
| [docs/headers.md](docs/headers.md) | Заголовки ответа (`cursed-version`, `cursed-duration`, ...) и `OPTIONS` / `cursed-supported-functions` |
| [docs/codes.md](docs/codes.md) | Таблица кодов `"XXX"` с маппингом на HTTP и зарезервированными диапазонами |

### Объекты (схемы CursedJSON)

| Объект | Файл | Описание |
|--------|------|----------|
| Envelope object | [docs/objects/envelope.md](docs/objects/envelope.md) | Корневой конверт любого JSON-ответа |
| Data object | [docs/objects/data.md](docs/objects/data.md) | Полезная нагрузка: список, одинарный, bulk, virtual |
| Settings object | [docs/objects/settings.md](docs/objects/settings.md) | Эхо применённых query-параметров + `total` |
| Bulk object | [docs/objects/bulk.md](docs/objects/bulk.md) | Результат частичной bulk-операции: `succeeded` / `failed` |
| Error object | [docs/objects/error.md](docs/objects/error.md) | Детали ошибки: `error`, `fields`, `hint`, `docs` |
| Field object | [docs/objects/field.md](docs/objects/field.md) | Ошибка валидации одного поля: типы, длины, диапазоны, enum, pattern, nested, items |
| Metadata object | [docs/objects/metadata.md](docs/objects/metadata.md) | Служебная информация: request_id, пагинация, warnings |
| Request object | [docs/objects/request.md](docs/objects/request.md) | Конверт тела запроса: `data` + `options` |
| Options object | [docs/objects/options.md](docs/objects/options.md) | Опции операции: `partial_allowed`, кастомные флаги сервисов |

## Быстрый пример

Запрос:

```
GET /v1/users?search=john,doe&search_column=name,surname&search_method=exactly,or,include&limit=10
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
            "order": "ascending",
            "sort_by": null,
            "search": "john,doe",
            "search_method": "exactly,or,include",
            "search_column": "name,surname"
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
4. **Мультипоиск из коробки** - запятые в `search` / `search_column` / `search_method` позволяют строить составные условия.
5. **Приём данных** - тело запроса в [Request object](docs/objects/request.md): `data` + `options` (флаг `partial_allowed`, кастомные опции любого сервиса).
6. **Интроспекция** - `OPTIONS` + `cursed-supported-functions` сообщают, что поддерживает эндпоинт.
