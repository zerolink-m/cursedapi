# Error object

Детали ошибки. Содержится в [Envelope object](envelope.md) -> `error`. `{}` при успехе.

## Схема

| Поле | Тип | Обязательное | Описание |
|------|-----|--------------|----------|
| error | str | Да | Основная ошибка, которая произошла |
| fields | object | Нет | Ошибки по полям: `{ "имя_поля": Field object }` - для кода `109` (Validation error) |
| hint | str | Нет | Подсказка, как исправить (от себя) |
| docs | str | Нет | Ссылка на документацию по этой ошибке (от себя) |

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

## Правила

1. `error` - всегда строка, даже если ошибка сложная; детали - в `fields`.
2. `fields` присутствует только при ошибках валидации полей (`109`, `119`, `120` и т.п.).
3. `hint` и `docs` опциональны - сервер может их не отдавать.
4. Для bulk-ошибок используется вложенный Error object внутри [Bulk object](bulk.md) -> `failed[].error`.
