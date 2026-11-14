# Коды для версии cursedapi v1.2

Код — строка из трёх цифр (`"XXX"`), от `"000"` до `"511"`. Это **не** HTTP-статус: каждый код сопоставлен отдельному HTTP-статусу. Клиент определяет сценарий по коду, а не по тексту ошибки.

`code_detail` — типовая человекочитаемая строка для одноимённого поля JSON-конверта; сервер может её локализовать, но смысл определяет `code`. Колонка «Описание» поясняет ограничения и условия, `—` означает отсутствие дополнительных условий. **Main code** показывает основной код того же HTTP-статуса: `Yes` с кратким смыслом у общего кода, `No → XXX` у узкого. Сервер может вернуть основной код вместо узкого, даже если узкий подходит, чтобы сообщить более общую информацию. HTTP-статус при этом не меняется. Для каждого представленного HTTP-статуса есть один основной код; при отсутствии подходящего узкого кода выбирают его. Номера стандартных кодов упорядочены сначала по HTTP-классу, затем по точному HTTP-статусу. Свободные номера между ними предназначены для будущих кодов стандарта.

## Распределение пространства

| HTTP-класс | Диапазон стандартных кодов | Определено | Свободно для стандарта |
|------------|----------------------------|------------|------------------------|
| 2xx | `000–044` | 19 | 26 |
| 3xx | `045–058` | 6 | 8 |
| 4xx | `059–331` | 116 | 157 |
| 5xx | `332–383` | 22 | 30 |
| Любой допустимый HTTP-статус | `384–511` | 0 | 128 кодов сервисов |

Всего определено 163 стандартных кода; внутри `000–383` свободен 221 номер. Свободные номера **не** являются кодами сервисов: их может занять будущая версия стандарта. Для пользовательских кодов сервис использует только `384–511`, сам объявляет соответствие HTTP-статусам и смысл кодов в своей документации/схеме. Неопределённый стандартный код нельзя возвращать до его назначения стандартом.

Примечание: порядок кодов внутри HTTP-класса следует числовому порядку HTTP-статусов. Для будущего кода выбирается свободный номер внутри соответствующего класса с сохранением этого порядка, пока в нужном месте есть место; если промежуток исчерпан, дальнейшее перераспределение потребует новой версии спецификации.

## 2xx — стандартные коды (`000–044`)

| Code | HTTP Code | code_detail | Описание | Main code |
|------|-----------|-------------|----------|-----------|
| 000 | 200 | Success. | — | Yes — успешный ответ |
| 002 | 200 | Edited successfully. | — | No → `000` |
| 004 | 200 | Partially edited successfully. | Только при `partial_allowed=true`. | No → `000` |
| 007 | 200 | Not changed. | — | No → `000` |
| 009 | 200 | Deleted successfully. | — | No → `000` |
| 012 | 200 | Partially deleted successfully. | Только при `partial_allowed=true`. | No → `000` |
| 014 | 200 | Authorized successfully. | — | No → `000` |
| 017 | 200 | Streaming response. | — | No → `000` |
| 019 | 200 | Empty result. | Запрос корректен, но записи не найдены. | No → `000` |
| 022 | 200 | Cached response. | — | No → `000` |
| 024 | 200 | The object is already in the requested state. | — | No → `000` |
| 026 | 200 | File uploaded successfully. | — | No → `000` |
| 029 | 200 | Email sent successfully. | — | No → `000` |
| 031 | 200 | Password changed successfully. | — | No → `000` |
| 034 | 200 | Logged out successfully. | — | No → `000` |
| 036 | 200 | Token refreshed successfully. | — | No → `000` |
| 039 | 201 | Created successfully. | — | Yes — создание |
| 041 | 201 | Partially created successfully. | Только при `partial_allowed=true`. | No → `039` |
| 044 | 202 | Accepted for work. | — | Yes — принято к обработке |

## 3xx — стандартные коды (`045–058`)

| Code | HTTP Code | code_detail | Описание | Main code |
|------|-----------|-------------|----------|-----------|
| 045 | 301 | Moved permanently. | — | Yes — постоянное перенаправление |
| 047 | 302 | Moved temporarily. | — | Yes — временное перенаправление |
| 050 | 303 | See other. | — | Yes — перенаправление к другому ресурсу |
| 052 | 304 | File not modified. | Тела нет; код передаётся в `cursed-code` (см. [Headers](headers.md)). | Yes — ресурс не изменился |
| 055 | 307 | Temporarily relocated while retaining the method. | — | Yes — временное перенаправление с сохранением метода |
| 058 | 308 | Permanently relocated while retaining the method. | — | Yes — постоянное перенаправление с сохранением метода |

## 4xx — стандартные коды (`059–331`)

| Code | HTTP Code | code_detail | Описание | Main code |
|------|-----------|-------------|----------|-----------|
| 059 | 400 | Invalid request. | — | Yes — некорректный запрос |
| 061 | 400 | These fields cannot be edited for one or more objects. | — | No → `059` |
| 063 | 400 | Invalid JSON body. | — | No → `059` |
| 066 | 400 | Invalid query parameters. | Включает некорректные или недопустимые выражения `filter` (см. [Filter](filter.md)). | No → `059` |
| 068 | 400 | A required field is missing. | — | No → `059` |
| 070 | 400 | Field type mismatch. | — | No → `059` |
| 073 | 400 | The request body is empty. | — | No → `059` |
| 075 | 400 | The file is too large. | — | No → `059` |
| 077 | 400 | Unsupported file type. | — | No → `059` |
| 080 | 400 | Invalid file content. | — | No → `059` |
| 082 | 400 | Invalid email format. | — | No → `059` |
| 085 | 400 | The password is too weak. | — | No → `059` |
| 087 | 400 | Passwords do not match. | — | No → `059` |
| 089 | 400 | Invalid date format. | — | No → `059` |
| 092 | 400 | Invalid phone number format. | — | No → `059` |
| 094 | 400 | Invalid URL format. | — | No → `059` |
| 096 | 400 | The field value is out of range. | — | No → `059` |
| 099 | 400 | Too many fields in the request. | — | No → `059` |
| 101 | 400 | Duplicate values in the request. | — | No → `059` |
| 103 | 400 | Captcha verification failed. | — | No → `059` |
| 106 | 400 | Invalid one-time code (OTP). | — | No → `059` |
| 108 | 400 | The one-time code (OTP) has expired. | — | No → `059` |
| 111 | 400 | Too many one-time code (OTP) attempts. | — | No → `059` |
| 113 | 400 | Invalid credentials. | — | No → `059` |
| 115 | 400 | An account with this email already exists. | — | No → `059` |
| 118 | 400 | An account with this username already exists. | — | No → `059` |
| 120 | 400 | The old password is invalid. | — | No → `059` |
| 122 | 400 | You cannot perform this action on yourself. | — | No → `059` |
| 125 | 400 | Invalid sort field. | — | No → `059` |
| 127 | 400 | Invalid search column. | — | No → `059` |
| 129 | 400 | Invalid limit or offset value. | — | No → `059` |
| 132 | 400 | The bulk operation size limit has been exceeded. | — | No → `059` |
| 134 | 400 | There is nothing to update. | — | No → `059` |
| 137 | 400 | There is nothing to delete. | — | No → `059` |
| 139 | 400 | Invalid token format. | — | No → `059` |
| 141 | 400 | The token has been revoked. | — | No → `059` |
| 144 | 400 | The refresh token is missing. | — | No → `059` |
| 146 | 400 | Invalid locale or language. | — | No → `059` |
| 148 | 400 | Unsupported media type for this endpoint. | — | No → `059` |
| 151 | 400 | The request was cancelled by the client. | — | No → `059` |
| 153 | 400 | The requested function is not supported by this API server. | Проверяйте `cursed-supported-functions` в OPTIONS. | No → `059` |
| 155 | 400 | The requested function is disabled on this API server. | — | No → `059` |
| 158 | 400 | Limit value is above the maximum allowed by this API server. | — | No → `059` |
| 160 | 400 | Expansion is not supported for one or more requested relations. | — | No → `059` |
| 163 | 400 | The stream was interrupted before completion. | — | No → `059` |
| 165 | 400 | The request combines functions that cannot be used together. | — | No → `059` |
| 167 | 401 | Token error. | — | Yes — ошибка авторизации |
| 170 | 401 | The token has expired. | — | No → `167` |
| 172 | 401 | The token is signed with an incorrect key. | — | No → `167` |
| 174 | 401 | The token has an invalid algorithm. | — | No → `167` |
| 177 | 401 | No header or query containing the token was found. | Авторизация невозможна без токена. | No → `167` |
| 179 | 402 | Payment required. | — | Yes — требуется оплата |
| 181 | 403 | Forbidden. | — | Yes — доступ запрещён |
| 184 | 403 | This resource is not available for retrieval. Forbidden. | — | No → `181` |
| 186 | 403 | Forbidden due to an incorrect role. | — | No → `181` |
| 189 | 403 | Access to this resource is denied for this object. | — | No → `181` |
| 191 | 403 | Actor is blocked. | — | No → `181` |
| 193 | 403 | You are blocked. | — | No → `181` |
| 196 | 403 | Email is not confirmed. | — | No → `181` |
| 198 | 403 | The account is not activated. | — | No → `181` |
| 200 | 403 | A subscription is required to access this resource. | — | No → `181` |
| 203 | 403 | The rate limit for the current plan has been exceeded. | — | No → `181` |
| 205 | 403 | The object is read-only. | — | No → `181` |
| 208 | 403 | Editing your own account this way is forbidden. | — | No → `181` |
| 210 | 403 | Your IP address is blocked. | — | No → `181` |
| 212 | 404 | Not found. | — | Yes — не найдено |
| 215 | 404 | Object not found. | — | No → `212` |
| 217 | 404 | Route not found. | — | No → `212` |
| 219 | 404 | File not found. | — | No → `212` |
| 222 | 404 | Field not found. | — | No → `212` |
| 224 | 404 | Endpoint not found. | — | No → `212` |
| 226 | 405 | The method is not permitted for one or more objects. | Ответ содержит стандартный `Allow` с разрешёнными методами. | Yes — метод не разрешён |
| 229 | 406 | This response format cannot be returned. | — | Yes — формат ответа недоступен |
| 231 | 408 | The request from the user timed out. | — | Yes — таймаут запроса |
| 234 | 409 | Conflict. | — | Yes — конфликт |
| 236 | 409 | The field already exists for this object or one of the objects. | — | No → `234` |
| 238 | 409 | The object has child objects. | — | No → `234` |
| 241 | 409 | The object is being edited by another user. | — | No → `234` |
| 243 | 409 | Stale data: the object has changed since it was last fetched. | — | No → `234` |
| 245 | 409 | The object cannot be deleted because it is referenced by other objects. | — | No → `234` |
| 248 | 409 | The operation is already in progress. | — | No → `234` |
| 250 | 409 | Duplicate request (idempotency conflict). | — | No → `234` |
| 252 | 410 | The object is unavailable or has been permanently deleted. | — | Yes — ресурс недоступен |
| 255 | 411 | Length Required. | — | Yes — требуется длина |
| 257 | 412 | Precondition Failed. | — | Yes — предусловие не выполнено |
| 260 | 413 | The request field contains extra elements, or the total request size has been exceeded. | — | Yes — слишком большой запрос |
| 262 | 414 | URI Too Long. | — | Yes — слишком длинный URI |
| 264 | 415 | Unsupported body format. | — | Yes — формат тела не поддерживается |
| 267 | 416 | Range not satisfiable. | — | Yes — диапазон недоступен |
| 269 | 422 | Validation error. | При отсутствии обязательного поля стандартный эндпоинт использует этот код; кастомный может вернуть `068` (HTTP 400). | Yes — ошибка валидации |
| 271 | 422 | A required query parameter is missing. | — | No → `269` |
| 274 | 422 | The field value is too long. | — | No → `269` |
| 276 | 422 | The field value is too short. | — | No → `269` |
| 278 | 422 | The field contains forbidden characters. | — | No → `269` |
| 281 | 422 | Invalid enum value. | — | No → `269` |
| 283 | 422 | The field is immutable. | — | No → `269` |
| 286 | 422 | Too many decimal places. | — | No → `269` |
| 288 | 422 | Negative values are not allowed. | — | No → `269` |
| 290 | 422 | Empty values are not allowed. | — | No → `269` |
| 293 | 422 | Invalid nested object. | — | No → `269` |
| 295 | 422 | Unknown field in the request. | — | No → `269` |
| 297 | 422 | A multi search list contains an empty position. | — | No → `269` |
| 300 | 422 | The search values, columns and methods lists have different lengths. | — | No → `269` |
| 302 | 423 | Locked. | — | Yes — ресурс заблокирован |
| 304 | 423 | Object locked to edit. | — | No → `302` |
| 307 | 423 | Object locked to delete. | — | No → `302` |
| 309 | 423 | The object is locked by an administrator. | — | No → `302` |
| 312 | 428 | Precondition required. | — | Yes — требуется предусловие |
| 314 | 429 | Too many requests. | — | Yes — слишком много запросов |
| 316 | 429 | Too many requests for object. | — | No → `314` |
| 319 | 429 | Too many login attempts. | — | No → `314` |
| 321 | 429 | Too many requests to this endpoint. | — | No → `314` |
| 323 | 429 | The upload rate limit has been exceeded. | — | No → `314` |
| 326 | 429 | The bulk request rate limit has been exceeded. | — | No → `314` |
| 328 | 451 | Unavailable for legal reasons. | — | Yes — недоступно по юридическим причинам |
| 331 | 451 | The content is not available in your region. | — | No → `328` |

## 5xx — стандартные коды (`332–383`)

| Code | HTTP Code | code_detail | Описание | Main code |
|------|-----------|-------------|----------|-----------|
| 332 | 500 | Internal server error. | — | Yes — внутренняя ошибка |
| 334 | 500 | Database error. | — | No → `332` |
| 336 | 500 | File storage error. | — | No → `332` |
| 339 | 500 | Email service error. | — | No → `332` |
| 341 | 500 | Cache error. | — | No → `332` |
| 344 | 500 | A database migration is in progress. | — | No → `332` |
| 346 | 501 | The bulk operation is not implemented. | — | No → `353` |
| 349 | 501 | Search is not implemented. | — | No → `353` |
| 351 | 501 | The expansion feature is not implemented on this server. | — | No → `353` |
| 353 | 501 | Function not implemented. | — | Yes — функция не реализована |
| 356 | 502 | Payment gateway error. | — | No → `361` |
| 358 | 502 | External auth provider error. | — | No → `361` |
| 361 | 502 | Bad gateway. | — | Yes — ошибка шлюза |
| 363 | 502 | The external API is unavailable. | — | No → `361` |
| 366 | 502 | The external service returned an unexpected response format. | — | No → `361` |
| 368 | 503 | The service is under maintenance. | — | No → `373` |
| 370 | 503 | The database is starting up. | — | No → `373` |
| 373 | 503 | The service is unavailable. | — | Yes — сервис недоступен |
| 375 | 503 | The service is under load. | — | No → `373` |
| 378 | 504 | The external file service request timed out. | — | No → `380` |
| 380 | 504 | Gateway timeout. | — | Yes — таймаут шлюза |
| 383 | 504 | The external API request timed out. | — | No → `380` |

## Коды сервисов (`384–511`)

Все 128 номеров предназначены для сервисов. Сервис назначает код и HTTP-статус совместно, документирует их для клиентов и не использует номера стандартной части. Значение `code` остаётся строкой из трёх цифр.
