# Settings object

Эхо применённых query-параметров + общее количество записей. Содержится в [Data object](data.md) при массовой отдаче.

## Схема

| Поле | Тип | Обязательное | Описание |
|------|-----|--------------|----------|
| total | int | Да | Сколько всего записей в БД подходит под фильтры (без учёта `limit`/`offset`) |
| limit | int | Да | Сколько было отправлено юзером (или default 25) |
| offset | int | Да | Сколько было отправлено юзером (или default 0) |
| order | str | Да | `asc`/`ascending` или `desc`/`descending` |
| sort_by | str or null | Да | Поле (или поля через запятую) сортировки. `null` если не передано |
| search | str or null | Да | Value (или значения через запятую) поиска. `null` если не передано |
| search_method | str or null | Да | Метод (или методы через запятую) поиска. `null` если не передано |
| search_column | str or null | Да | Колонка (или колонки через запятую) поиска. `null` если не передано |

Эхо `search_method` включает и логические связки (`and`/`or`/`and_not`/`or_not`) - как передал клиент (см. [query.md](../query.md), Logical Connectors). `expand` и `partial_allowed` в `settings` не эхо-руются: раскрытие связей видно в `data`, а `partial_allowed` - флаг операции, не выборка.

## Пример

```json
"settings": {
    "total": 42,
    "limit": 10,
    "offset": 0,
    "order": "ascending",
    "sort_by": null,
    "search": "john,doe",
    "search_method": "exactly,or,include",
    "search_column": "name,surname"
}
```

## Правила

1. `settings` - точное эхо того, что **реально применил сервер** (после нормализации и дефолтов), а не слепок запроса.
2. Если query-параметр не передан - его поле равно дефолту (`limit: 25`, `offset: 0`, `order: "ascending"`) или `null` (`sort_by`, `search`, `search_method`, `search_column`).
3. Мультизначения возвращаются как есть, одной строкой с запятыми - в том же виде, в котором прислал клиент (после нормализации кавычек).
4. `total` считается **до** применения `limit`/`offset` - для пагинации на фронтенде.
