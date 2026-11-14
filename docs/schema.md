# Схема эндпоинта — cursedapi v1.2

`cursed-schema-url` в ответе `OPTIONS` указывает на описание **шаблона пути** с отдельными правилами для каждого HTTP-метода. Публикация схемы необязательна для минимального сервера; если включён продвинутый [`filter`](filter.md), схема обязательна. URL выбирает сервер, он не обязан совпадать с путём ресурса.

Например, `OPTIONS /v1/users/100/avatars` может вернуть:

```http
Allow: GET, POST, OPTIONS
cursed-supported-functions: GET.limit,GET.offset,GET.filter
cursed-schema-url: https://api.example.com/v1/schemas/users/avatars
```

Один и тот же URL может описывать `/v1/users/100/avatars` и `/v1/users/101/avatars`: в схеме указан шаблон `/v1/users/{user_id}/avatars`, а не конкретный ID.

## Ответ по URL схемы

GET по `cursed-schema-url` использует обычную авторизацию сервиса и возвращает CursedJSON (с `code`, `code_detail`, `error`, `metadata`, `data`). В `data.schema`:

| Поле | Тип | Назначение |
|------|-----|------------|
| `path_template` | string | Путь с именованными параметрами, например `/v1/users/{user_id}/avatars` |
| `path_parameters` | object | Ожидаемые параметры пути: тип, обязательность и описание |
| `methods` | object | Ключ — HTTP-метод; значение — его отдельная схема |

Внутри каждого метода `query` описывает поддерживаемые query-параметры, `request` — принимаемый Content-Type, JSON Schema тела и пример, `responses` — возможные HTTP-статусы и варианты ответа. Не относящиеся к методу разделы отсутствуют: например, у GET без тела нет `request.body_schema`. `body_schema` — JSON Schema Draft 2020-12; она описывает **полное** тело, а не только `data`. `example` — иллюстрация структуры, а не обещание успеха бизнес-операции. Каждому варианту ответа соответствует HTTP-статус, строковый код cursedapi, фактический Content-Type и схема полного ответа. Несколько вариантов при одном статусе допускаются.

Упрощённый пример схемы вложенной коллекции (внутренние JSON Schema сокращены только там, где показано `{}`):

```json
{
  "code": "000",
  "code_detail": "Success.",
  "error": {},
  "metadata": {},
  "data": {
    "schema": {
      "path_template": "/v1/users/{user_id}/avatars",
      "path_parameters": {
        "user_id": { "type": "int", "required": true, "description": "ID владельца аватаров" }
      },
      "methods": {
        "GET": {
          "query": {
            "limit": { "type": "int", "default": 25 },
            "offset": { "type": "int", "default": 0 },
            "filter": {
              "encoding": "base64url",
              "fields": {
                "type": { "type": "string", "operators": ["==", "IN"] }
              },
              "limits": {
                "max_expression_bytes": 2048,
                "max_depth": 8,
                "max_conditions": 20,
                "max_in_items": 50
              }
            }
          },
          "responses": {
            "200": [
              { "code": "000", "content_type": "application/json", "body_schema": {} }
            ],
            "404": [
              { "code": "215", "content_type": "application/json", "body_schema": {} }
            ]
          }
        },
        "POST": {
          "request": {
            "content_type": "application/json",
            "body_schema": {
              "$schema": "https://json-schema.org/draft/2020-12/schema",
              "type": "object",
              "required": ["data"],
              "properties": {
                "data": {
                  "type": "object",
                  "required": ["avatar"],
                  "properties": {
                    "avatar": {
                      "type": "object",
                      "required": ["type"],
                      "properties": { "type": { "type": "string" } }
                    }
                  }
                }
              }
            },
            "example": { "data": { "avatar": { "type": "png" } } }
          },
          "responses": {
            "201": [
              { "code": "039", "content_type": "application/json", "body_schema": {} }
            ],
            "422": [
              { "code": "269", "content_type": "application/json", "body_schema": {} }
            ]
          }
        }
      }
    }
  }
}
```

`{}` в `responses.*[].body_schema` выше — **заглушка примера**, не достаточная опубликованная схема: реальный сервер описывает полный конверт и данные/ошибку для каждого варианта. Сервер перечисляет свои возможные варианты, а не обязан реализовывать все коды таблицы.

Схема конкретного аватара относится к **другому шаблону** `/v1/users/{user_id}/avatars/{avatar_id}`: там два параметра пути и свой набор методов. `avatar_id` ищется в пределах указанного `user_id`; нельзя использовать схему коллекции как схему конкретного элемента. Результат для чужого объекта определяется политикой доступа сервиса и не должен раскрывать сведения о недоступных объектах.

## Обнаружение и безопасность

- `OPTIONS` показывает реально доступные методы (`Allow`) и функции по методам (`cursed-supported-functions`); `methods` в схеме согласован с ними для текущего клиента. Сервер может описывать разные права для разных клиентов.
- Если фильтр включён хотя бы для одного метода, `cursed-schema-url` обязателен, и схема этого метода содержит `query.filter.fields` и `query.filter.limits` в формате [filter.md](filter.md). Для остальных методов фильтр не предполагается автоматически.
- Не публикуйте недоступные поля, секретные значения и чужие реальные данные. Если схема зависит от авторизации, не разрешайте общим кешам переиспользовать её между клиентами; ответ должен использовать соответствующую политику кеширования (например, `Cache-Control: private, no-store`).
- Для ошибки валидации сервер может вернуть `error.request_schema` и `error.request_example` вместо ссылки на схему или вместе с ней (см. [Error object](objects/error.md)).
