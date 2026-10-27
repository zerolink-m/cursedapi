# Request object

Конверт тела запроса. Зеркален [Envelope object](envelope.md) из ответа, но содержит только входную часть.

```
Request object
|- data: Data object (обязательный)
\- options: Options object (опциональный)
```

## Схема

| Поле | Тип | Обязательное | Описание |
|------|-----|--------------|----------|
| data | [Data object](data.md) | Да | Полезная нагрузка: ключ объекта (см. правила именования) |
| options | [Options object](options.md) | Нет | Опции выполнения операции. Отсутствует, если опций нет |

## Примеры

Создание/изменение одного объекта:

```json
{
    "data": {
        "user": {
            "field": "value"
        }
    }
}
```

Bulk-операция:

```json
{
    "data": {
        "users": [
            { "field": "value" },
            { "field": "value" }
        ]
    }
}
```

С опциями:

```json
{
    "data": {
        "users": [
            { "field": "value" }
        ]
    },
    "options": {
        "partial_allowed": true
    }
}
```

## Правила

1. В запросе нет `code`, `code_detail`, `error`, `metadata` - это часть ответного [Envelope object](envelope.md).
2. Именование ключей в `data` зеркально ответу: единственное число для одинарных операций (`user`), множественное для bulk (`users`).
3. `options` перекрывает одноимённые query-параметры (см. [requests.md](../requests.md)).
4. Тело обязательно для POST и PATCH. Для DELETE - опционально.
5. Content-Type: `application/json` обязателен для JSON-тел. Иначе - код `107` (Unsupported body format).
