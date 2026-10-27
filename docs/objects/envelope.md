# Envelope object

Корневой объект любого JSON-ответа cursedapi. Все остальные объекты вложены в него.

```
Envelope
|- code: str          - Code object (см. codes.md)
|- code_detail: str   - человекочитаемое описание кода
|- error: Error object
|- metadata: Metadata object
|- data: Data object
```

## Схема

| Поле | Тип | Обязательное | Описание |
|------|-----|--------------|----------|
| code | str `"XXX"` | Да | Код из [codes.md](../codes.md) |
| code_detail | str | Да | Человекочитаемое описание кода (может локализоваться) |
| error | [Error object](error.md) | Да | `{}` при успехе, детали при ошибке |
| metadata | [Metadata object](metadata.md) | Да | Служебная информация |
| data | [Data object](data.md) | Да | Полезная нагрузка |

## Пример

```json
{
    "code": "000",
    "code_detail": "Success.",
    "error": {},
    "metadata": {},
    "data": {}
}
```

## Правила

1. Все пять полей присутствуют **всегда**, даже если пустые - фронтенд не должен проверять наличие ключей.
2. `code` - всегда строка из трёх цифр, не число.
3. Порядок полей не регламентирован, но рекомендуется указанный выше.
4. Не-JSON ответы (файлы, бинарные данные, HTML) конвертом не оборачиваются - см. [responses.md](../responses.md).
