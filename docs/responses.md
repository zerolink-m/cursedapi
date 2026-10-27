# Формат ответов (CursedJSON) для cursedapi v1.1

Любой JSON-ответ API оборачивается в единый конверт - [Envelope object](objects/envelope.md):

```json
{
    "code": "000",
    "code_detail": "Success.",
    "error": {},
    "metadata": {},
    "data": {}
}
```

Составные объекты описаны отдельно:

| Объект | Файл |
|--------|------|
| Envelope object (корневой) | [objects/envelope.md](objects/envelope.md) |
| Data object (полезная нагрузка) | [objects/data.md](objects/data.md) |
| Settings object (эхо query + total) | [objects/settings.md](objects/settings.md) |
| Bulk object (частичные bulk-операции) | [objects/bulk.md](objects/bulk.md) |
| Error object (детали ошибки) | [objects/error.md](objects/error.md) |
| Field object (валидация поля) | [objects/field.md](objects/field.md) |
| Metadata object (служебное) | [objects/metadata.md](objects/metadata.md) |

Заголовки ответа и `OPTIONS` / `cursed-supported-functions` - в [headers.md](headers.md).

## Сценарии ответов

### Массовая отдача (список)

HTTP 200, код `000`

```json
{
    "code": "000",
    "code_detail": "Success.",
    "error": {},
    "metadata": {},
    "data": {
        "settings": {
            "total": 42,
            "limit": 25,
            "offset": 0,
            "order": "ascending",
            "sort_by": null,
            "search": null,
            "search_method": null,
            "search_column": null
        },
        "users": [
            {
                "id": 1,
                "name": "user_name",
                "added_at": 123456789
            }
        ]
    }
}
```

### Одинарная: получение, изменение, создание, удаление

HTTP 200, коды `000`/`003`/`005`/`007`; создание - HTTP 201, код `001`

```json
{
    "code": "000",
    "code_detail": "Success.",
    "error": {},
    "metadata": {},
    "data": {
        "user": {
            "id": 1,
            "name": "user_name",
            "added_at": 123456789
        }
    }
}
```

### Bulk-операции

Полный успех - коды `001`/`003`/`007`, `data` содержит массив объектов.
Частичный успех - коды `002`/`004`/`008` и [Bulk object](objects/bulk.md):

```json
{
    "code": "002",
    "code_detail": "Partially created succesfully.",
    "error": {},
    "metadata": {},
    "data": {
        "users": {
            "succeeded": [
                { "id": 1, "name": "ok_user" }
            ],
            "failed": [
                {
                    "id": 2,
                    "error": {
                        "error": "Validation error: field 'name' is required"
                    }
                }
            ]
        }
    }
}
```

### Вложенное получение связанных объектов

Есть две таблицы:

```
users:
id: int
avatar: int
name: varchar(64)
added_at: int
```

и

```
avatars:
id: int
type: varchar(16)
```

Одинарный ответ с раскрытой связью (через `?expand=avatar`):

```json
{
    "code": "000",
    "code_detail": "Success.",
    "error": {},
    "metadata": {},
    "data": {
        "user": {
            "id": 1,
            "avatar": {
                "id": 2,
                "type": "png"
            },
            "name": "user_name",
            "added_at": 123456789
        }
    }
}
```

Глубина раскрытия связей управляется query-параметром `expand` (через запятую):

```
GET /v1/users/1?expand=avatar
GET /v1/users?expand=avatar,friends
```

Без `expand` связи возвращаются как идентификаторы.

### Ошибки

HTTP 4xx/5xx, [Error object](objects/error.md) в `error`, [Field object](objects/field.md) при валидации:

```json
{
    "code": "109",
    "code_detail": "Validation error.",
    "error": {
        "error": "Some fields are invalid",
        "fields": {
            "name": {
                "error": "is required",
                "required": true,
                "type": "string"
            },
            "added_at": {
                "error": "type mismatch",
                "type": "int",
                "actual_type": "string"
            }
        }
    },
    "metadata": {},
    "data": {}
}
```

### Стриминг

Для потоковых ответов (SSE, чанки) - код `011`. Каждый чанк/событие - отдельный [Envelope object](objects/envelope.md). `metadata` последнего чанка может содержать `finished: true`.

### Не-JSON ответы

Файлы, бинарные данные, HTML - отдаются без конверта. Content-Type отличен от `application/json`. Служебные заголовки (`cursed-version` и т.д.) всё равно присутствуют - см. [headers.md](headers.md).
