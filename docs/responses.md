# Формат ответов (CursedJSON) для cursedapi v1.2

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

Тело запроса (приём данных) описано в [requests.md](requests.md): [Request object](objects/request.md) (`data` + `options`) и [Options object](objects/options.md) (`partial_allowed` и кастомные флаги).

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
            "order": "asc",
            "sort_by": null,
            "search": null,
            "search_method": null,
            "search_column": null,
            "filter": null
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

HTTP 200, коды `000`/`002`/`007`/`009`; создание - HTTP 201, код `039`

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

Атомарны по умолчанию. Частичное выполнение - только с флагом `partial_allowed` (см. [requests.md](requests.md)).

Полный успех - коды `039`/`002`/`009`, `data` содержит массив объектов. Bulk GET успешен тем же кодом, что и обычный список (`000`).
Частичный успех - коды `041`/`004`/`012` и [Bulk object](objects/bulk.md):

```json
{
    "code": "041",
    "code_detail": "Partially created successfully.",
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
    "code": "269",
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

При ошибке валидации тела сервер может дополнительно указать `error.request_schema` и `error.request_example` — пример см. в [Error object](objects/error.md). Для ошибки `filter` детали помещаются в `error.query.filter` (код `066`); правила языка — в [filter.md](filter.md).

### Стриминг

Для потоковых ответов (SSE, чанки) - код `017`. Каждый чанк/событие - отдельный [Envelope object](objects/envelope.md). `metadata` последнего чанка может содержать `finished: true`.

Спецификация не регулирует заголовок `Accept` и не диктует коды чанков:
их определяет программист на основе логики своего приложения.

### Не-JSON ответы

Файлы, бинарные данные, HTML - отдаются без конверта. Content-Type отличен от `application/json`. Служебные заголовки (`cursed-version` и т.д.) всё равно присутствуют - см. [headers.md](headers.md).
