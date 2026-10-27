# Коды для версии cursedapi v1.1

Код - строка из трёх цифр `"XXX"`. Фронтенд определяет сценарий по коду, а не по тексту ошибки.

**Main code** - основной код для своего HTTP-кода: если нет подходящего узкого кода, используется main.

## Успех (2xx)

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 000 | 200 | Success. | Yes, for success |
| 001 | 201 | Created successfully. | No |
| 002 | 201 | Partially created successfully. Returned only with `partial_allowed=true`. | No |
| 003 | 200 | Edited successfully. | No |
| 004 | 200 | Partially edited successfully. Returned only with `partial_allowed=true`. | No |
| 005 | 200 | Not changed. | No |
| 006 | 202 | Accepted for work. | No |
| 007 | 200 | Deleted successfully. | No |
| 008 | 200 | Partially deleted successfully. Returned only with `partial_allowed=true`. | No |
| 010 | 200 | Authorized successfully. | No |
| 011 | 200 | Streaming response. | No |
| 012 | 200 | Empty result. The request is valid, but no records were found. | No |
| 013 | 200 | Cached response. | No |
| 014 | 200 | The object is already in the requested state. | No |
| 015 | 200 | File uploaded successfully. | No |
| 016 | 200 | Email sent successfully. | No |
| 017 | 200 | Password changed successfully. | No |
| 018 | 200 | Logged out successfully. | No |
| 019 | 200 | Token refreshed successfully. | No |
| 020-029 | 2xx | Your any success codes. | - |

## Редиректы (3xx)

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 030 | 301 | Moved permanently. | No |
| 031 | 302 | Moved temporarily. | No |
| 032 | 303 | See other. | No |
| 033 | 304 | File not modified. | No |
| 034 | 307 | Temporarily relocated while retaining the method. | No |
| 035 | 308 | Permanently relocated while retaining the method. | No |
| 036-039 | 3xx | Your any redirect codes. | - |

## Ошибки клиента (4xx)

### Общие

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 040 | 400 | Invalid request. | Yes |
| 041 | 400 | This fields cannot edit for one or more objects. | No |
| 042 | 401 | Token error. | Yes |
| 043 | 401 | The token has expired. | No |
| 044 | 401 | The token is signed with an incorrect key. | No |
| 045 | 401 | The token has an invalid algorithm. | No |
| 046 | 401 | No header or query containing the token was found. Authorization is not possible. | No |
| 047 | 402 | Payment required. | Yes |
| 048 | 403 | Forbidden. | Yes |
| 049 | 403 | This resource is not available for retrieval. Forbidden. | No |
| 050 | 403 | Forbidden due to an incorrect role. | No |
| 051 | 403 | Access to this resource is denied for this object. | No |
| 052 | 403 | Actor is blocked. | No |
| 053 | 403 | You are blocked. | No |
| 081 | 404 | Not found. | Yes |
| 082 | 404 | Object not found. | No |
| 096 | 405 | The method is not permitted for one or more objects. | Yes |
| 097 | 406 | This response format cannot be returned. | Yes |
| 098 | 408 | The request from the user timed out. | Yes |
| 099 | 409 | Conflict. | Yes |
| 100 | 409 | The field already exists for this object or one of the objects. | No |
| 101 | 409 | The object has child objects. | No |
| 102 | 410 | The object is unavailable or has been permanently deleted. | Yes |
| 103 | 411 | Length Required. | Yes |
| 104 | 412 | Precondition Failed. | Yes |
| 105 | 413 | The request field contains extra elements, or the total request size has been exceeded. | Yes |
| 106 | 414 | URI Too Long. | Yes |
| 107 | 415 | Unsupported body format. | Yes |
| 108 | 416 | Range not satisfiable. | Yes |
| 109 | 422 | Validation error. Yes, for missing required fields in standard endpoints, but a custom endpoint may return code 119 (400) instead. | Yes |
| 110 | 423 | Locked. | Yes |
| 111 | 423 | Object locked to edit. | No |
| 112 | 423 | Object locked to delete. | No |
| 113 | 428 | Precondition required. | Yes |
| 114 | 429 | Too many requests. | Yes |
| 115 | 429 | Too many requests for object. | No |
| 116 | 451 | Unavailable for legal reasons. | Yes |

### Доступ и аккаунт (403)

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 054 | 403 | Email is not confirmed. | No |
| 055 | 403 | The account is not activated. | No |
| 056 | 403 | A subscription is required to access this resource. | No |
| 057 | 403 | The rate limit for the current plan has been exceeded. | No |
| 058 | 403 | The object is read-only. | No |
| 059 | 403 | Editing your own account this way is forbidden. | No |
| 060 | 403 | Your IP address is blocked. | No |
| 061-080 | 403 | Your any 403 code. | - |

### Не найдено (404)

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 083 | 404 | Route not found. | No |
| 084 | 404 | File not found. | No |
| 085 | 404 | Field not found. | No |
| 086 | 404 | Endpoint not found. | No |
| 087-095 | 404 | Your any 404 code. | - |

### Формат запроса и файлов (400)

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 117 | 400 | Invalid JSON body. | No |
| 118 | 400 | Invalid query parameters. For example an empty position in a multi search list (code 107 section in [Query](query.md)). | No |
| 119 | 400 | A required field is missing. | No |
| 120 | 400 | Field type mismatch. | No |
| 121 | 400 | The request body is empty. | No |
| 122 | 400 | The file is too large. | No |
| 123 | 400 | Unsupported file type. | No |
| 124 | 400 | Invalid file content. | No |
| 125 | 400 | Invalid email format. | No |
| 126 | 400 | The password is too weak. | No |
| 127 | 400 | Passwords do not match. | No |
| 128 | 400 | Invalid date format. | No |
| 129 | 400 | Invalid phone number format. | No |
| 130 | 400 | Invalid URL format. | No |
| 131 | 400 | The field value is out of range. | No |
| 132 | 400 | Too many fields in the request. | No |
| 133 | 400 | Duplicate values in the request. | No |
| 134 | 400 | Captcha verification failed. | No |
| 135 | 400 | Invalid one-time code (OTP). | No |
| 136 | 400 | The one-time code (OTP) has expired. | No |
| 137 | 400 | Too many one-time code (OTP) attempts. | No |
| 138 | 400 | Invalid credentials. | No |
| 139 | 400 | An account with this email already exists. | No |
| 140 | 400 | An account with this username already exists. | No |
| 141 | 400 | The old password is invalid. | No |
| 142 | 400 | You cannot perform this action on yourself. | No |
| 143 | 400 | Invalid sort field. | No |
| 144 | 400 | Invalid search column. | No |
| 145 | 400 | Invalid limit or offset value. | No |
| 146 | 400 | The bulk operation size limit has been exceeded. | No |
| 147 | 400 | There is nothing to update. | No |
| 148 | 400 | There is nothing to delete. | No |
| 149 | 400 | Invalid token format. | No |
| 150 | 400 | The token has been revoked. | No |
| 151 | 400 | The refresh token is missing. | No |
| 181 | 400 | The requested function is not supported by this API server. See the OPTIONS response (`cursed-supported-functions`). | No |
| 182 | 400 | The requested function is disabled on this API server. | No |
| 183 | 400 | Limit value is above the maximum allowed by this API server. | No |
| 184 | 400 | Expansion is not supported for one or more requested relations. | No |
| 185 | 400 | The stream was interrupted before completion. | No |
| 186 | 400 | The request combines functions that cannot be used together. | No |
| 152 | 400 | Invalid locale or language. | No |
| 153 | 400 | Unsupported media type for this endpoint. | No |
| 154 | 400 | The request was cancelled by the client. | No |

### Конфликты (409)

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 155 | 409 | The object is being edited by another user. | No |
| 156 | 409 | Stale data: the object has changed since it was last fetched. | No |
| 157 | 409 | The object cannot be deleted because it is referenced by other objects. | No |
| 158 | 409 | The operation is already in progress. | No |
| 159 | 409 | Duplicate request (idempotency conflict). | No |

### Валидация полей (422)

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 160 | 422 | A required query parameter is missing. | No |
| 161 | 422 | The field value is too long. | No |
| 162 | 422 | The field value is too short. | No |
| 163 | 422 | The field contains forbidden characters. | No |
| 164 | 422 | Invalid enum value. | No |
| 165 | 422 | The field is immutable. | No |
| 166 | 422 | Too many decimal places. | No |
| 167 | 422 | Negative values are not allowed. | No |
| 168 | 422 | Empty values are not allowed. | No |
| 169 | 422 | Invalid nested object. | No |
| 170 | 422 | Unknown field in the request. | No |
| 188 | 422 | A multi search list contains an empty position. | No |
| 189 | 422 | The search values, columns and methods lists have different lengths. | No |

### Лимиты и блокировки (423 / 429 / 451)

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 171 | 423 | The object is locked by an administrator. | No |
| 172 | 429 | Too many login attempts. | No |
| 173 | 429 | Too many requests to this endpoint. | No |
| 174 | 429 | The upload rate limit has been exceeded. | No |
| 187 | 429 | The bulk request rate limit has been exceeded. | No |
| 175 | 451 | The content is not available in your region. | No |
| 176-200 | 4xx | Your any 4xx code. | - |

## Ошибки сервера (5xx)

| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 201 | 500 | Internal server error. | Yes |
| 202 | 500 | Database error. | No |
| 203 | 500 | File storage error. | No |
| 204 | 500 | Email service error. | No |
| 205 | 500 | Cache error. | No |
| 206 | 500 | A database migration is in progress. | No |
| 207 | 501 | The bulk operation is not implemented. | No |
| 208 | 501 | Search is not implemented. | No |
| 209 | 502 | Payment gateway error. | No |
| 210 | 502 | External auth provider error. | No |
| 211 | 503 | The service is under maintenance. | No |
| 212 | 503 | The database is starting up. | No |
| 213 | 504 | The external file service request timed out. | No |
| 215 | 501 | The expansion feature is not implemented on this server. | No |
| 214-220 | 500 | Your any 500 code. | - |
| 221 | 501 | Function not implemented. | Yes |
| 222 | 502 | Bad gateway. | Yes |
| 223 | 502 | The external API is unavailable. | No |
| 224 | 502 | The external service returned an unexpected response format. | No |
| 225 | 503 | The service is unavailable. | Yes |
| 226 | 503 | The service is under load. | No |
| 227 | 504 | Gateway timeout. | Yes |
| 228 | 504 | The external API request timed out. | No |
| 229-255 | any | Your any code. | - |

## Зарезервированные диапазоны

Свободные диапазоны оставлены в конце каждого блока, чтобы при добавлении новых кодов не приходилось их смещать:

| Диапазон | HTTP | Назначение |
|-----|-----|-----|
| 020-029 | 2xx | Кастомные коды успеха |
| 036-039 | 3xx | Кастомные редиректы |
| 061-080 | 403 | Кастомные запреты |
| 087-095 | 404 | Кастомные «не найдено» |
| 176-200 | 4xx | Кастомные ошибки клиента |
| 214-220 | 500 | Кастомные ошибки сервера (215 занят expansion) |
| 229-255 | any | Полностью кастомные коды |

Примечание: код 215 взят из диапазона 214-220 под расширяющую функцию спеки
(expansion). Диапазон кастомных кодов сервера при этом сужен до 216-220.

Примечание: код 187 (429) зарезервирован под bulk rate limit.
