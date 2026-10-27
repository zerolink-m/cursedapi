# Query-параметры для cursedapi v1.1

Применяются к эндпоинтам получения списка объектов (`GET /v1/users`).

| key | value | is_required | default | description |
|-----|-------|-------------|---------|-------------|
| sort_by | Any from database | No | No | Поле (или поля через запятую), по которому будут сортировать |
| order | (asc or ascending) or (desc or descending) | No | ascending | Тип сортировки записей |
| limit | 1-255 | No | 25 | Лимит записей, которые вернутся из БД |
| offset | 0+ | No | 0 | Сколько записей пропустить. Например, если 10 - пропустит первые 10 и вернёт следующие |
| search | str | No | No | Значение (или значения через запятую) поиска в БД |
| search_method | include or exclude or exactly or or | No | include | Метод (или методы через запятую) поиска |
| search_column | str | if search -> Yes, else -> No | No | Колонка (или колонки через запятую) поиска |

## Мультизначения через запятую

`search`, `search_column`, `search_method` и `sort_by` поддерживают несколько значений, разделённых запятой. Значения сопоставляются **позиционно**: первое значение `search` соответствует первому `search_column` и первому `search_method`, и т.д.

Пример:

```
GET /v1/users?search=john,doe&search_column=name,surname&search_method=exactly,or,include
```

Разбор:

| Позиция | search | search_column | search_method |
|---------|--------|---------------|---------------|
| 1 | `john` | `name` | `exactly` |
| 2 | `doe` | `surname` | `or` |
| 3 | - | - | `include` |

Эквивалент в SQL:

```sql
SELECT * FROM users WHERE name == 'john' OR surname LIKE '%doe%'
```

### Правила

1. **Количество значений** `search`, `search_column` и `search_method` должно совпадать, либо `search_method` может быть один (тогда применяется ко всем парам), либо отсутствовать (тогда `include` для всех).
2. **`or`** - не метод сравнения, а **связка**: условие на этой позиции соединяется с предыдущим через `OR` вместо `AND`. Без `or` все условия соединяются через `AND`.
3. **Экранирование запятых**: если в искомом значении нужна запятая, значение оборачивается в кавычки `'...'` или `"..."`:
   - `search='Smith, John',doe` -> значения `Smith, John` и `doe`
   - `search="1,2"` -> одно значение `1,2`
4. **Кавычки внутри значения** удваиваются для экранирования: `search='it''s'` -> значение `it's`.

### Примеры

Поиск по одной колонке с исключением:

```
GET /v1/users?search=john&search_column=name&search_method=exclude
-- WHERE name NOT LIKE '%john%'
```

Несколько условий через AND:

```
GET /v1/users?search=john,doe&search_column=name,surname
-- WHERE name LIKE '%john%' AND surname LIKE '%doe%'
```

Смешанные связки:

```
GET /v1/users?search=john,doe,jane&search_column=name,surname,name&search_method=exactly,or,exclude
-- WHERE name == 'john' OR surname LIKE '%doe%' OR name NOT LIKE '%jane%'
```

Сортировка по нескольким полям:

```
GET /v1/users?sort_by=name,added_at&order=desc
-- ORDER BY name DESC, added_at DESC
```

## Методы поиска

| Метод | SQL-эквивалент | Описание |
|-------|---------------|----------|
| `include` | `LIKE '%value%'` | Содержит |
| `exclude` | `NOT LIKE '%value%'` | Не содержит |
| `exactly` | `== value` | Точное совпадение |
| `or` | (связка) | Соединяет условие с предыдущим через `OR` |
