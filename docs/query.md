# Query Parameters

Query parameters control how the server processes a request. All parameter
names are lowercase snake_case. Unless stated otherwise, every parameter is
optional and defaults to disabled.

- [Main Documentation](../README.md)

## Parameters

| Parameter | Type | Default | Description |
|---|---|---|---|
| `sort_by` | string | none | Field name to sort by. See [Sorting](#sorting). |
| `order` | string | `asc` | Sort direction: `asc` or `desc`. See [Sorting](#sorting). |
| `limit` | integer | 25 | Maximum number of items in a list response. Range: 1 to 255. |
| `offset` | integer | 0 | Number of items to skip before the first returned item. Range: 0 and above. |
| `search` | string | none | Search value or list of values. See [Multi Search](#multi-search). |
| `search_column` | string | none | Field name or list of names the search applies to. See [Multi Search](#multi-search). |
| `search_method` | string | none | Comparison mode or logical connector. See [Multi Search](#multi-search). |
| `expand` | string | none | Comma separated list of relation names to embed as nested objects. See [Expansion](#expansion). |
| `partial_allowed` | boolean | `false` | Allows partial success in bulk operations. See [Request Options](requests.md#partial_allowed). |

Limit out of range: `limit > 255` returns code 256. `limit = 0` is treated as
invalid input and returns code 107. Unknown query parameters are ignored.

## Multi Search

The search parameters work as parallel lists. The i-th value of `search`
matches the i-th name of `search_column` and the i-th token of
`search_method`. Each triple describes one condition.

### Lists and Positional Mapping

Values are separated by commas without spaces:

```
search=john,doe
search_column=name,surname
search_method=equals,include
```

This selects rows where `name` equals `john` OR `surname` contains `doe`.

If `search_column` or `search_method` has a single value while the others
have several, the single value applies to every condition:

```
search=alice,bob
search_column=email
search_method=include,include
```

Both conditions apply to `email`.

### Search Methods

| Method | SQL equivalent |
|---|---|
| `equals` | `column = 'value'` |
| `include` | `column LIKE '%value%'` |
| `starts` | `column LIKE 'value%'` |
| `ends` | `column LIKE '%value'` |
| `exclude` | `column NOT LIKE '%value%'` |
| `exactly` | `column = 'value'` (same as `equals`, kept for compatibility) |

### Logical Connectors

A connector is not a comparison. It glues the condition built from the
current triple to the condition built before it. Available connectors:

| Connector | Meaning |
|---|---|
| `and` | the previous condition is joined with `AND` |
| `or` | the previous condition is joined with `OR` |
| `and_not` | the previous condition is joined with `AND NOT` |
| `or_not` | the previous condition is joined with `OR NOT` |

The first condition never has a connector: it is the root of the expression.
A connector is written as the `search_method` token of the condition it
joins, and that triple contributes no comparison of its own.

Example with several fields:

```
search=john,doe,jane
search_column=name,surname,age
search_method=equals,or,include
```

SQL equivalent:

```sql
SELECT * FROM users
WHERE name = 'john'
   OR surname LIKE '%doe%'
  AND age LIKE '%jane%'
```

Parentheses are not supported: conditions are evaluated left to right.

### Escaping

- To search for a value that contains a comma, wrap it in quotes:
  `search='Smith, John'` or `search="1,2"`.
- Quoted values are never split on commas.
- To search for a literal value with quotes, double the quotes inside:
  `search='it''s'`.
- An unquoted value never contains quotes.

### Literal Quotes

Quotes are syntax, not data. The value `search='test'` is parsed by the
server as the string `test`, not as `'test'`. To search for the literal
string `'test'` including the quotes, the client must quote the quotes:

```
search="'test'"
```

### Empty Positions Are Forbidden

Every position in a list must be filled. Empty positions are a client error
(code 107):

```
WRONG:  search_column=name,,surname
RIGHT:  search_column=name,surname,age
```

If the client does not know a value for a position, it must not include that
position in the request at all.

## Sorting

One `order` value applies to every `sort_by` column:

```
sort_by=created_at,updated_at
order=desc
```

Several `order` values (comma separated) apply positionally, one per column:

```
sort_by=created_at,updated_at
order=asc,desc
```

An invalid direction returns code 107.

## Expansion

`expand` accepts a comma separated list of relation names:

```
GET /v1/users/1?expand=orders,profile
```

Expanded relations are embedded as nested objects under the relation name.
Nested expansion (`expand=orders.items`) is not part of this version.
An unknown relation returns code 107.

## partial_allowed

`partial_allowed=true` in the query string is equivalent to the same field in
the `options` object of the request body. The `options` value wins when both
are present. Details: [Request Options](requests.md#partial_allowed).

## Main Documentation

- [Paths](paths.md)
- [Responses](responses.md)
- [Headers](headers.md)
- [Codes](codes.md)
- [Requests](requests.md)
