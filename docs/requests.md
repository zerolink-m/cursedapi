# Приём данных (запросы) для cursedapi v1.1

Тело запроса оборачивается в конверт - [Request object](objects/request.md): `data` (обязательный) + `options` (опциональный).

## Авторизация

Два способа передать учётные данные (см. [headers.md](headers.md), раздел Authorization):

| Способ | Пример |
|--------|--------|
| Заголовок (предпочтительно) | `Authorization: Bearer eyh...` |
| Query-параметр | `GET /v1/users?token=eyh...` |

Спецификация не ограничивает тип авторизации: `TYPE` в заголовке - любое
название схемы (`Bearer`, `Basic`, `ApiKey` и т.д.), формат данных - на
усмотрение сервиса. Ошибки авторизации - коды `042`-`046` (401).

## Content-Type

| Тип тела | Content-Type | Когда |
|----------|--------------|-------|
| JSON | `application/json` | Все POST/PATCH и опционально DELETE |
| Файлы | `multipart/form-data` | Загрузка файлов (например кастомный `upload-avatar`) |
| Прочее | по содержимому | Кастомные и virtual-эндпоинты (см. [paths.md](paths.md)) |

Неподдерживаемый формат тела - код `107` (Unsupported body format), битый JSON - `117` (Invalid JSON body), пустое обязательное тело - `121` (The request body is empty).

## Тело запроса

### Одинарная операция (POST / PATCH)

```json
{
    "data": {
        "user": {
            "name": "john",
            "added_at": 123456789
        }
    }
}
```

Ключ объекта - в единственном числе, как в одинарном ответе (см. [Data object](objects/data.md)).

### Bulk-операция (POST /v1/users/bulk, PATCH /v1/users/bulk/{ids})

```json
{
    "data": {
        "users": [
            { "name": "john" },
            { "name": "jane" }
        ]
    }
}
```

Ключ - во множественном числе, значение - массив объектов.

### Вложенные объекты

Вложения создаются/изменяются в составе родителя, если сервер это поддерживает (см. `expand` в [query.md](query.md)):

```json
{
    "data": {
        "user": {
            "name": "john",
            "avatar": {
                "type": "png"
            }
        }
    }
}
```

## Options object

[Options object](objects/options.md) - управляющие флаги операции, не данные:

```json
{
    "data": { "users": [ { "name": "john" } ] },
    "options": {
        "partial_allowed": true
    }
}
```

Схема открыта: любой сервис может добавлять свои опции. Неизвестные опции игнорируются.

## partial_allowed

Управляет поведением bulk-операций при ошибках.

| Значение | Поведение |
|----------|-----------|
| `false` (default) | **Атомарность**: если хотя бы один объект не прошёл - вся операция отклоняется (код ошибки, например `109`), **ничего не применяется** |
| `true` | **Частичное выполнение**: успешные применяются, упавшие пропускаются. Ответ - коды `002`/`004`/`008` (partially) и [Bulk object](objects/bulk.md) с `succeeded`/`failed` |

### Передача флага

Два способа - query-параметр или `options` в теле. **`options` имеет преимущество над query.**

Query (необязательный, default `false`):

```
PATCH /v1/users/bulk/1,2,5?partial_allowed=true
```

Body:

```json
{
    "data": { "users": [ { "name": "john" } ] },
    "options": { "partial_allowed": true }
}
```

Если переданы оба - применяется значение из `options`.

## Валидация входа

Сервер валидирует `data` по схеме объекта. При ошибке возвращается код `109`
(Validation error) с [Error object](objects/error.md) и [Field object](objects/field.md)
по каждому некорректному полю (см. [responses.md](responses.md), раздел Ошибки).

Код при отсутствии обязательного поля: как правило `109` (422), но кастомный
эндпоинт вправе вернуть `119` (400, A required field is missing). Оба кода
валидны: выбор за сервером, фронтенд обязан обрабатывать оба.

## Правила

1. Тело обязательно для POST и PATCH; для DELETE - опционально.
2. `data` обязателен в теле; запрос без `data` - код `121` или `109`.
3. `options` не содержит данных - только флаги выполнения.
4. Кастомные и virtual-эндпоинты могут принимать любое тело, но JSON - только в формате Request object.
