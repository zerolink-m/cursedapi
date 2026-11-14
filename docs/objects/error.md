# Error object

Детали ошибки. Содержится в [Envelope object](envelope.md) -> `error`. `{}` при успехе.

## Схема

| Поле | Тип | Обязательное | Описание |
|------|-----|--------------|----------|
| error | str | Да | Основная ошибка, которая произошла |
| fields | object | Нет | Ошибки по полям тела: `{ "имя_поля": Field object }` - для кода `109` (Validation error) |
| query | object | Нет | Ошибки по query-параметрам: `{ "имя_параметра": Field object }` |
| path | object | Нет | Ошибки по частям пути: `{ "имя_параметра_пути": Field object }`, например `{ "user_id": {...} }` |
| headers | object | Нет | Ошибки по заголовкам: `{ "имя_заголовка": Field object }` |
| hint | str | Нет | Подсказка, как исправить (от себя) |
| docs | str | Нет | Ссылка на документацию по этой ошибке (от себя) |
| request_schema | object | Нет | JSON Schema ожидаемого тела запроса для данного эндпоинта и операции; только доступные этому клиенту поля |
| request_example | object | Нет | Пример тела запроса в формате [Request object](request.md), соответствующий схеме; образец структуры, а не гарантия успешного выполнения |

`query`, `path` и `headers` заполняются по тем же правилам, что и `fields`:
значение - [Field object](field.md) с `error` внутри. Присутствуют, только
если ошибка относится к соответствующей части запроса (коды `118`, `145`,
`160`, `181`-`186`, `188`, `189` и другие).

## Примеры

Простая ошибка:

```json
"error": {
    "error": "Object not found"
}
```

Ошибка валидации:

```json
"error": {
    "error": "Some fields are invalid",
    "fields": {
        "name": {
            "error": "is required",
            "required": true,
            "type": "string"
        }
    },
    "hint": "Check the required fields and try again",
    "docs": "https://api.example.com/docs/errors/109"
}
```

Ошибка в query и path:

```json
"error": {
    "error": "Invalid query parameters",
    "query": {
        "limit": {
            "error": "must be an integer between 1 and 255",
            "value": "abc",
            "actual_type": "string"
        }
    },
    "path": {
        "user_id": {
            "error": "must be an integer",
            "value": "john"
        }
    }
}
```

Ошибка авторизации:

```json
"error": {
    "error": "The token has expired",
    "headers": {
        "Authorization": {
            "error": "token expired",
            "format": "datetime"
        }
    }
}
```

Пример ответа с моделью ожидаемого запроса (фрагмент `error`):

```json
{
    "error": "Some fields are invalid",
    "fields": {
        "name": { "error": "is required", "required": true, "type": "string" }
    },
    "request_schema": {
        "$schema": "https://json-schema.org/draft/2020-12/schema",
        "type": "object",
        "required": ["data"],
        "properties": {
            "data": {
                "type": "object",
                "required": ["user"],
                "properties": {
                    "user": {
                        "type": "object",
                        "required": ["name"],
                        "properties": { "name": { "type": "string", "minLength": 1 } }
                    }
                }
            }
        }
    },
    "request_example": { "data": { "user": { "name": "example" } } }
}
```

## Правила

1. `error` - всегда строка, даже если ошибка сложная; детали - в `fields`/`query`/`path`/`headers`.
2. `fields` присутствует только при ошибках валидации полей тела (`109`, `119`, `120` и т.п.).
3. `query`/`path`/`headers` присутствуют только при ошибках в соответствующей части запроса.
4. `hint`, `docs`, `request_schema` и `request_example` опциональны. Для ошибок валидации тела сервер может приложить схему и пример. `request_schema` — JSON Schema Draft 2020-12 для тела с корнем `data`/`options`; `request_example` соответствует ей. Для ошибок query вместо схемы тела указываются `query`, `hint` и/или `docs`.
5. Схема и пример описывают текущий эндпоинт, метод и доступные этому клиенту поля; они не должны включать секреты, реальные чужие данные или внутренние поля. Пример не гарантирует прохождения бизнес-правил (например, уникальности). При наличии `cursed-schema-url` можно вместо встроенной схемы дать ссылку в `docs`.
6. Для bulk-ошибок используется вложенный Error object внутри [Bulk object](bulk.md) -> `failed[].error`; большие схемы не следует дублировать для каждого элемента.
