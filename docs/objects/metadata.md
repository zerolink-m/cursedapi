# Metadata object

Служебная информация о запросе/ответе. Содержится в [Envelope object](envelope.md) -> `metadata`. Может быть `{}`.

## Схема

Все поля опциональны - сервер отдаёт то, что считает нужным.

| Поле | Тип | Описание |
|------|-----|----------|
| request_id | str | UUID запроса (дублирует заголовок `cursed-request-id`) |
| generated_at | int | Unix timestamp генерации ответа |
| duration | float | Время обработки запроса в секундах (дублирует `cursed-duration`) |
| cursed_version | str | Версия API (например `1.1`) |
| deprecated | bool | Эндпоинт устарел (дублирует `cursed-deprecation`) |
| page | object | Пагинация: `{ "current": 1, "pages": 5 }` - вычисляется из `total`/`limit`/`offset` |
| expand | array of str | Какие связи были раскрыты |
| warnings | array of str | Неблокирующие предупреждения (например: «параметр X устарел») |

## Пример

```json
"metadata": {
    "request_id": "550e8400-e29b-41d4-a716-446655440000",
    "generated_at": 1790123456,
    "duration": 0.042,
    "cursed_version": "1.1",
    "page": {
        "current": 1,
        "pages": 5
    },
    "expand": ["avatar"],
    "warnings": ["query parameter 'filter' is deprecated, use 'search' instead"]
}
```

## Правила

1. `metadata` никогда не содержит полезную нагрузку - только служебные данные.
2. Фронтенд не должен полагаться на наличие конкретных полей в `metadata`.
3. Ключи, начинающиеся с `x-` - кастомные расширения конкретной реализации.
