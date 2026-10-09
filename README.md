
| Code | HTTP Code | Detail | Is main code |
|-----|-----|-----|-----|
| 000 | 200 | Success. | Yes, for success
| 001 | 201 | Created succesfully. | No
| 002 | 201 | Partially created succesfully. | No
| 003 | 200 | Edited succesfully. | No
| 004 | 200 | Partially edited succesfully. | No
| 005 | 200 | Not changed. | No
| 006 | 202 | Accepted for work. | No
| 007 | 200 | Deleted succesfully. | No
| 008 | 200 | Partially deleted succesfully. | No
| 010 | 200 | Authorized succesfully. | No
| 011 | 200 | Streaming response. | No
| 012-029 | 2xx | Your any success codes. | -
| 030 | 301 | Moved permanently. | No
| 031 | 302 | Moved temporarily. | No
| 032 | 303 | See other. | No
| 033 | 304 | File not modified. | No
| 034 | 307 | Temporarily relocated while retaining the method. | No
| 035 | 308 | Permanently relocated while retaining the method. | No
| 036-039 | 3xx | Your any redirect codes. | -
| 040 | 400 | Invalid request. | Yes
| 041 | 400 | This fields cannot edit for one or more objects. | No
| 042 | 401 | Token error. | Yes
| 043 | 401 | The token has expired. | No
| 044 | 401 | The token is signed with an incorrect key. | No
| 045 | 401 | The token has an invalid algorithm. | No
| 046 | 401 | No header or query containing the token was found. Authorization is not possible. | No
| 047 | 402 | Payment required. | Yes
| 048 | 403 | Forbidden. | Yes
| 049 | 403 | This resource is not available for retrieval. Forbidden. | No
| 050 | 403 | Forbidden due to an incorrect role. | No
| 051 | 403 | Access to this resource is denied for this object. | No
| 052 | 403 | Actor is blocked. | No
| 053 | 403 | You are blocked. | No
| 054-080 | 403 | Your any 403 code. | -
| 081 | 404 | Not found. | Yes
| 082 | 404 | Object not found. | No
| 083-095 | 404 | Your any 404 code. | No
| 096 | 405 | The method is not permitted for one or more objects. | Yes
| 097 | 406 | This response format cannot be returned. | Yes
| 098 | 408 | The request from the user timed out. | Yes
| 099 | 409 | Conflict. | Yes
| 100 | 409 | The field already exists for this object or one of the objects. | No
| 101 | 409 | The object has child objects. | No
| 102 | 410 | The object is unavailable or has been permanently deleted. | Yes
| 103 | 411 | Length Required. | Yes
| 104 | 412 | Precondition Failed. | Yes
| 105 | 413 | The request field contains extra elements, or the total request size has been exceeded. | Yes
| 106 | 414 | URI Too Long. | Yes
| 107 | 415 | Unsupported body format. | Yes
| 108 | 416 | Range not satisfiable. | Yes
| 109 | 422 | Validation error. | Yes
| 110 | 423 | Locked. | Yes
| 111 | 423 | Object locked to edit. | No
| 112 | 423 | Object locked to delete. | No
| 113 | 428 | Precondition required. | Yes
| 114 | 429 | Too many requests. | Yes
| 115 | 429 | Too many requests for object. | No
| 116 | 451 | Unavailable for legal reasons. | Yes
| 117-200 | 4xx | Your any 4xx code. | -
| 201 | 500 | Internal server error. | Yes
| 202 | 500 | Database error. | No
| 203-220 | 500 | Your any 500 code. | -
| 221 | 501 | Function not implemented. | Yes
| 222 | 502 | Bad gateway. | Yes
| 223 | 502 | The external API is unavailable. | No
| 224 | 502 | The external service returned an unexpected response format. | No
| 225 | 503 | The service is unavailable. | Yes
| 226 | 503 | The service is under load. | No
| 227 | 504 | Gateway timeout | Yes
| 228 | 504 | The external API request timed out. | No
| 229-255 | any | Your any code. | -