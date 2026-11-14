# Коды для версии cursedapi v1.2

Код — строка из трёх цифр (`"XXX"`), от `"000"` до `"511"`. Это **не** HTTP-статус: каждый код сопоставлен отдельному HTTP-статусу. Клиент определяет сценарий по коду, а не по тексту ошибки.

**Main code** — основной код для своего HTTP-статуса: если нет подходящего узкого кода, используется main. Номера стандартных кодов упорядочены сначала по HTTP-классу, затем по точному HTTP-статусу. Свободные номера между ними предназначены для будущих кодов стандарта.

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

| Code | HTTP Code | Detail | Is main code |
|------|-----------|--------|--------------|
| 000 | 200 | Success. | Yes, for success |
| 002 | 200 | Edited successfully. | No |
| 004 | 200 | Partially edited successfully. Returned only with `partial_allowed=true`. | No |
| 007 | 200 | Not changed. | No |
| 009 | 200 | Deleted successfully. | No |
| 012 | 200 | Partially deleted successfully. Returned only with `partial_allowed=true`. | No |
| 014 | 200 | Authorized successfully. | No |
| 017 | 200 | Streaming response. | No |
| 019 | 200 | Empty result. The request is valid, but no records were found. | No |
| 022 | 200 | Cached response. | No |
| 024 | 200 | The object is already in the requested state. | No |
| 026 | 200 | File uploaded successfully. | No |
| 029 | 200 | Email sent successfully. | No |
| 031 | 200 | Password changed successfully. | No |
| 034 | 200 | Logged out successfully. | No |
| 036 | 200 | Token refreshed successfully. | No |
| 039 | 201 | Created successfully. | No |
| 041 | 201 | Partially created successfully. Returned only with `partial_allowed=true`. | No |
| 044 | 202 | Accepted for work. | No |

## 3xx — стандартные коды (`045–058`)

| Code | HTTP Code | Detail | Is main code |
|------|-----------|--------|--------------|
| 045 | 301 | Moved permanently. | No |
| 047 | 302 | Moved temporarily. | No |
| 050 | 303 | See other. | No |
| 052 | 304 | File not modified. | No |
| 055 | 307 | Temporarily relocated while retaining the method. | No |
| 058 | 308 | Permanently relocated while retaining the method. | No |

## 4xx — стандартные коды (`059–331`)

| Code | HTTP Code | Detail | Is main code |
|------|-----------|--------|--------------|
| 059 | 400 | Invalid request. | Yes |
| 061 | 400 | This fields cannot edit for one or more objects. | No |
| 063 | 400 | Invalid JSON body. | No |
| 066 | 400 | Invalid query parameters, including malformed or disallowed `filter` expressions (see [Filter](filter.md)). | No |
| 068 | 400 | A required field is missing. | No |
| 070 | 400 | Field type mismatch. | No |
| 073 | 400 | The request body is empty. | No |
| 075 | 400 | The file is too large. | No |
| 077 | 400 | Unsupported file type. | No |
| 080 | 400 | Invalid file content. | No |
| 082 | 400 | Invalid email format. | No |
| 085 | 400 | The password is too weak. | No |
| 087 | 400 | Passwords do not match. | No |
| 089 | 400 | Invalid date format. | No |
| 092 | 400 | Invalid phone number format. | No |
| 094 | 400 | Invalid URL format. | No |
| 096 | 400 | The field value is out of range. | No |
| 099 | 400 | Too many fields in the request. | No |
| 101 | 400 | Duplicate values in the request. | No |
| 103 | 400 | Captcha verification failed. | No |
| 106 | 400 | Invalid one-time code (OTP). | No |
| 108 | 400 | The one-time code (OTP) has expired. | No |
| 111 | 400 | Too many one-time code (OTP) attempts. | No |
| 113 | 400 | Invalid credentials. | No |
| 115 | 400 | An account with this email already exists. | No |
| 118 | 400 | An account with this username already exists. | No |
| 120 | 400 | The old password is invalid. | No |
| 122 | 400 | You cannot perform this action on yourself. | No |
| 125 | 400 | Invalid sort field. | No |
| 127 | 400 | Invalid search column. | No |
| 129 | 400 | Invalid limit or offset value. | No |
| 132 | 400 | The bulk operation size limit has been exceeded. | No |
| 134 | 400 | There is nothing to update. | No |
| 137 | 400 | There is nothing to delete. | No |
| 139 | 400 | Invalid token format. | No |
| 141 | 400 | The token has been revoked. | No |
| 144 | 400 | The refresh token is missing. | No |
| 146 | 400 | Invalid locale or language. | No |
| 148 | 400 | Unsupported media type for this endpoint. | No |
| 151 | 400 | The request was cancelled by the client. | No |
| 153 | 400 | The requested function is not supported by this API server. See the OPTIONS response (`cursed-supported-functions`). | No |
| 155 | 400 | The requested function is disabled on this API server. | No |
| 158 | 400 | Limit value is above the maximum allowed by this API server. | No |
| 160 | 400 | Expansion is not supported for one or more requested relations. | No |
| 163 | 400 | The stream was interrupted before completion. | No |
| 165 | 400 | The request combines functions that cannot be used together. | No |
| 167 | 401 | Token error. | Yes |
| 170 | 401 | The token has expired. | No |
| 172 | 401 | The token is signed with an incorrect key. | No |
| 174 | 401 | The token has an invalid algorithm. | No |
| 177 | 401 | No header or query containing the token was found. Authorization is not possible. | No |
| 179 | 402 | Payment required. | Yes |
| 181 | 403 | Forbidden. | Yes |
| 184 | 403 | This resource is not available for retrieval. Forbidden. | No |
| 186 | 403 | Forbidden due to an incorrect role. | No |
| 189 | 403 | Access to this resource is denied for this object. | No |
| 191 | 403 | Actor is blocked. | No |
| 193 | 403 | You are blocked. | No |
| 196 | 403 | Email is not confirmed. | No |
| 198 | 403 | The account is not activated. | No |
| 200 | 403 | A subscription is required to access this resource. | No |
| 203 | 403 | The rate limit for the current plan has been exceeded. | No |
| 205 | 403 | The object is read-only. | No |
| 208 | 403 | Editing your own account this way is forbidden. | No |
| 210 | 403 | Your IP address is blocked. | No |
| 212 | 404 | Not found. | Yes |
| 215 | 404 | Object not found. | No |
| 217 | 404 | Route not found. | No |
| 219 | 404 | File not found. | No |
| 222 | 404 | Field not found. | No |
| 224 | 404 | Endpoint not found. | No |
| 226 | 405 | The method is not permitted for one or more objects. Include the standard `Allow` header listing available methods. | Yes |
| 229 | 406 | This response format cannot be returned. | Yes |
| 231 | 408 | The request from the user timed out. | Yes |
| 234 | 409 | Conflict. | Yes |
| 236 | 409 | The field already exists for this object or one of the objects. | No |
| 238 | 409 | The object has child objects. | No |
| 241 | 409 | The object is being edited by another user. | No |
| 243 | 409 | Stale data: the object has changed since it was last fetched. | No |
| 245 | 409 | The object cannot be deleted because it is referenced by other objects. | No |
| 248 | 409 | The operation is already in progress. | No |
| 250 | 409 | Duplicate request (idempotency conflict). | No |
| 252 | 410 | The object is unavailable or has been permanently deleted. | Yes |
| 255 | 411 | Length Required. | Yes |
| 257 | 412 | Precondition Failed. | Yes |
| 260 | 413 | The request field contains extra elements, or the total request size has been exceeded. | Yes |
| 262 | 414 | URI Too Long. | Yes |
| 264 | 415 | Unsupported body format. | Yes |
| 267 | 416 | Range not satisfiable. | Yes |
| 269 | 422 | Validation error. Yes, for missing required fields in standard endpoints, but a custom endpoint may return code 068 (400) instead. | Yes |
| 271 | 422 | A required query parameter is missing. | No |
| 274 | 422 | The field value is too long. | No |
| 276 | 422 | The field value is too short. | No |
| 278 | 422 | The field contains forbidden characters. | No |
| 281 | 422 | Invalid enum value. | No |
| 283 | 422 | The field is immutable. | No |
| 286 | 422 | Too many decimal places. | No |
| 288 | 422 | Negative values are not allowed. | No |
| 290 | 422 | Empty values are not allowed. | No |
| 293 | 422 | Invalid nested object. | No |
| 295 | 422 | Unknown field in the request. | No |
| 297 | 422 | A multi search list contains an empty position. | No |
| 300 | 422 | The search values, columns and methods lists have different lengths. | No |
| 302 | 423 | Locked. | Yes |
| 304 | 423 | Object locked to edit. | No |
| 307 | 423 | Object locked to delete. | No |
| 309 | 423 | The object is locked by an administrator. | No |
| 312 | 428 | Precondition required. | Yes |
| 314 | 429 | Too many requests. | Yes |
| 316 | 429 | Too many requests for object. | No |
| 319 | 429 | Too many login attempts. | No |
| 321 | 429 | Too many requests to this endpoint. | No |
| 323 | 429 | The upload rate limit has been exceeded. | No |
| 326 | 429 | The bulk request rate limit has been exceeded. | No |
| 328 | 451 | Unavailable for legal reasons. | Yes |
| 331 | 451 | The content is not available in your region. | No |

## 5xx — стандартные коды (`332–383`)

| Code | HTTP Code | Detail | Is main code |
|------|-----------|--------|--------------|
| 332 | 500 | Internal server error. | Yes |
| 334 | 500 | Database error. | No |
| 336 | 500 | File storage error. | No |
| 339 | 500 | Email service error. | No |
| 341 | 500 | Cache error. | No |
| 344 | 500 | A database migration is in progress. | No |
| 346 | 501 | The bulk operation is not implemented. | No |
| 349 | 501 | Search is not implemented. | No |
| 351 | 501 | The expansion feature is not implemented on this server. | No |
| 353 | 501 | Function not implemented. | Yes |
| 356 | 502 | Payment gateway error. | No |
| 358 | 502 | External auth provider error. | No |
| 361 | 502 | Bad gateway. | Yes |
| 363 | 502 | The external API is unavailable. | No |
| 366 | 502 | The external service returned an unexpected response format. | No |
| 368 | 503 | The service is under maintenance. | No |
| 370 | 503 | The database is starting up. | No |
| 373 | 503 | The service is unavailable. | Yes |
| 375 | 503 | The service is under load. | No |
| 378 | 504 | The external file service request timed out. | No |
| 380 | 504 | Gateway timeout. | Yes |
| 383 | 504 | The external API request timed out. | No |

## Коды сервисов (`384–511`)

Все 128 номеров предназначены для сервисов. Сервис назначает код и HTTP-статус совместно, документирует их для клиентов и не использует номера стандартной части. Значение `code` остаётся строкой из трёх цифр.
