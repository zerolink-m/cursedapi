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

## Правила

1. `error` - всегда строка, даже если ошибка сложная; детали - в `fields`/`query`/`path`/`headers`.
2. `fields` присутствует только при ошибках валидации полей тела (`109`, `119`, `120` и т.п.).
3. `query`/`path`/`headers` присутствуют только при ошибках в соответствующей части запроса.
4. `hint` и `docs` опциональны - сервер может их не отдавать.
5. Для bulk-ошибок используется вложенный Error object внутри [Bulk object](bulk.md) -> `failed[].error`.
