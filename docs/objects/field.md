# Field object

Детали ошибки валидации одного поля. Значение в [Error object](error.md) -> `fields` по ключу-имени поля.

Базовый пример:

```json
"fields": {
    "name": {
        "required": true,
        "type": "string"
    }
}
```

## Схема

Все ключи опциональны; сервер указывает только те, что относятся к ошибке.

| Ключ | Тип | Когда присутствует | Описание |
|------|-----|---------------------|----------|
| error | str | всегда | Короткое человекочитаемое описание ошибки этого поля |
| required | bool | поле обязательное | Поле обязательно, но отсутствует или пустое |
| type | str | ошибка типа | Ожидаемый тип: `string`, `int`, `float`, `bool`, `date`, `datetime`, `array`, `object`, `file`, `null` |
| actual_type | str | ошибка типа | Фактический тип присланных данных |
| value | any | по возможности | Присланное значение (для отладки; сервер может скрыть чувствительные поля) |
| min_length / max_length | int | ошибка длины строки | Допустимая длина |
| actual_length | int | ошибка длины строки | Фактическая длина |
| min / max | int/float | число вне диапазона | Допустимый диапазон |
| enum | array of str | значение не из списка | Список допустимых значений |
| pattern | str | не совпала регулярка | Ожидаемый regex-паттерн |
| format | str | ошибка формата | Ожидаемый формат: `email`, `phone`, `url`, `uuid`, `date`, `datetime`, `slug`, `json` |
| immutable | bool | попытка изменить неизменяемое | Поле нельзя изменять (создаётся только сервером) |
| unknown | bool | лишнее поле | Поля нет в схеме объекта |
| nested | object | ошибка во вложенном объекте | Ключ - имя вложенного поля, значение - Field object |
| items | object | ошибка в элементе массива | `index` - индекс элемента, `fields` - Field object для него |

## Примеры

```json
"fields": {
    "email": {
        "error": "invalid format",
        "type": "string",
        "format": "email",
        "value": "not-an-email"
    },
    "age": {
        "error": "out of range",
        "type": "int",
        "min": 0,
        "max": 150,
        "value": 999
    },
    "profile": {
        "error": "nested validation failed",
        "nested": {
            "bio": {
                "error": "too long",
                "max_length": 500,
                "actual_length": 742
            }
        }
    },
    "tags": {
        "error": "invalid item at index 3",
        "type": "array",
        "items": {
            "index": 3,
            "fields": {
                "error": "forbidden characters",
                "pattern": "^[a-z0-9_]+$"
            }
        }
    }
}
```

## Правила

1. `error` внутри field object - всегда присутствует, даже если остальные ключи описывают ошибку полностью.
2. `nested` и `items` рекурсивны - вложенность любой глубины.
3. `value` может отсутствовать из соображений безопасности (пароли, токены).
4. Ключи полей - имена полей из схемы объекта, в snake_case.
