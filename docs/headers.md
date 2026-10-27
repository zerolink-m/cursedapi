# Headers

Headers carry specification metadata. Unless stated otherwise, a header is
sent in every response.

- [Main Documentation](../README.md)

## Response Headers

| Header | Example | Description |
|---|---|---|
| `cursed-version` | `1.1` | Specification version. If `off`, the function is not supported or disabled on this server. |
| `cursed-duration` | `0.035` | Request processing time in seconds. If `off`, not supported or disabled. |
| `cursed-request-id` | `f47ac10b-58cc-4372-a567-0e02b2c3d479` | Unique request identifier. If `off`, not supported or disabled. |
| `cursed-deprecation` | `true` | Marks the endpoint as deprecated. Absent when the endpoint is current. |
| `cursed-retry-after` | `30` | Seconds until the client may repeat the request. Sent with codes 114, 172, 173, 174. If `off`, not supported or disabled. |

The exact value format (for example a date instead of seconds in
`cursed-retry-after`) is up to the server implementation.

## Rate Limit Headers

Rate limit headers are sent when the server uses rate limiting. If rate
limiting is disabled or not supported, every rate limit header returns `off`.

| Header | Example | Description |
|---|---|---|
| `cursed-rate-limit-limit` | `100` | Maximum number of requests in the current window. |
| `cursed-rate-limit-remaining` | `87` | Remaining requests in the current window. |
| `cursed-rate-limit-reset` | `1699999999` | Unix timestamp when the window resets. |
| `cursed-rate-limit-policy` | `100;w=60` | Human readable policy description. Optional. |
| `cursed-retry-after` | `30` | Seconds until the client may repeat the request. |

When the limit is exhausted the server returns code 114 with the headers
above.

## Authorization

The client authenticates in one of two ways.

Header method, preferred:

```
Authorization: TYPE DATA
```

Query method, for clients that cannot set headers:

```
GET /v1/users?token=eyh...
```

`TYPE` is the authentication scheme name, for example `Bearer`. `DATA` is the
scheme dependent credential, for example a JWT. The example credential
`eyh...` is illustrative: any scheme and credential format is valid, the
specification does not restrict them. Both methods may be combined; the
header takes precedence.

Examples:

```
Authorization: Bearer eyh...
Authorization: Basic dXNlcjpwYXNz
Authorization: ApiKey 9f2c...
```

## OPTIONS

An OPTIONS request describes what the endpoint supports. Two headers are
mandatory in the response. In their values, elements are separated by
commas without spaces.

| Header | Example | Description |
|---|---|---|
| `cursed-supported-functions` | `limit,offset,search,...` | Comma separated list of supported functions. |
| `cursed-version` | `1.1` | Specification version. |

### cursed-supported-functions

Canonical names: `limit`, `offset`, `sort_by`, `order`, `search`,
`search_method`, `search_column`, `expand`, `bulk`, `multi_search`, `partial`,
`streaming`, `pagination`. Servers may add custom names, which must be
prefixed with `x-` (for example `x-geo-filter`).

### Other OPTIONS Headers

All headers below are optional. The rules of value `off` are given per
header. `off` means the function is disabled or not supported.

| Header | Values | Description |
|---|---|---|
| `cursed-bulk` | `on` / `off` | Bulk operations on the endpoint. |
| `cursed-max-bulk` | `5` / `off` / absent | Maximum items in one bulk request. `off` or absence means the function is disabled. |
| `cursed-paging` | `on` / `off` | Whether offset based paging is available. |
| `cursed-paging-max-limit` | `255` / `off` / absent | Maximum value of `limit` on the endpoint. `off` or absence means not restricted or unknown. |
| `cursed-streaming` | `on` / `off` | Streaming responses. |
| `cursed-search` | `on` / `off` | Search on the endpoint. |
| `cursed-auth` | `bearer` / `token` / `off` | Accepted authentication methods. |
| `cursed-accept` | `application/json` / `off` | Response content types the endpoint can produce. |
| `cursed-cache` | `on` / `off` | Server side caching for the endpoint. |

## Main Documentation

- [Paths](paths.md)
- [Responses](responses.md)
- [Query Parameters](query.md)
- [Codes](codes.md)
- [Requests](requests.md)
- [Objects](../README.md#objects)
