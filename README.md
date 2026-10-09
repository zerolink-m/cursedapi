
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
| 012-029 | Any | Your any success codes. | -
| 030 | 301 | Moved permanently. | No
| 031 | 302 | Moved temporarily. | No
| 032 | 303 | See other. | No
| 033 | 304 | File not modified. | No
| 034 | 307 | Temporarily relocated while retaining the method. | No
| 035 | 308 | Permanently relocated while retaining the method. | No
| 036-039 | Any | Your any redirect codes. | -
| 040 | 400 | Invalid request. | Yes
| 041 | 401 | Token error. | Yes
| 042 | 401 | The token has expired. | No
| 043 | 401 | The token is signed with an incorrect key. | No
| 044 | 401 | The token has an invalid algorithm. | No
| 045 | 401 | No header or query containing the token was found. Authorization is not possible. | No
| 046 | 402 | Payment required. | Yes
| 047 | 403 | Forbidden. | Yes
| 048 | 403 | This resource is not available for retrieval. Forbidden. | No
| 049 | 403 | Forbidden due to an incorrect role. | No
| 050 | 403 | Access to this resource is denied for this object. | No
| 051-055 | 403 | Your any 403 code. | -
| 056 | 404 | Not found. | Yes
| 057 | 404 | Object not found. | No
| 058-070 | 404 | Your any 404 code. | No
| 071 | 405 | The method is not permitted for one or more objects. | Yes
| 072 | 406 | This response format cannot be returned. | Yes
| 073 | 408 | The request from the user timed out. | Yes
| 074 | 409 | Conflict. | Yes
| 255 | 500 | Server error. | Yes, for general server error

    VALIDATION_ERROR: "Validation error.",
    BAD_REQUEST: "Invalid request.",
    OBJECT_FIELD_EXISTS: "The field already exists for this object or one of the objects.",
    TOO_LARGE: "The request field contains extra elements, or the total request size has been exceeded.",
    BAD_GATEWAY: "The external API is unavailable for one reason or another.",
    DB_ERROR: "The external database is unavailable for one reason or another.",
    RATE_LIMITED: "Request limit exceeded for one or more objects.",
    SERVICE_UNAVAILABLE: "The service is unavailable for one reason or another.",
    SERVICE_OVERLOADED: "The service is under heavy load.",
    FIELD_NOT_EDITABLE: "This fields cannot edit for one or more objects.",
    FILE_NOT_FOUND: "File not found.",
    FILE_BAD_FORMAT: "The file format is not suitable.",
    S3_DISABLED: "File storage is temporarily unavailable.",
    YOU_ARE_BLOCKED: "You are blocked.",
    S3_ERROR: "External object storage error.",
    DATACENTER_NOT_FOUND: "Datacenter not found.",
    REMNAWAVE_ERROR: "The remnawave API is unavailable for one reason or another.",
    WGDASHBOARD_ERROR: "The wgdashboard API is unavailable for one reason or another.",
    FUNCTION_NOT_IMPLEMENTED: "This function is not currently implemented.",
    OBJECT_HAS_CHILDS: "The object has child objects.",
    TOO_SMALL: "The request field is missing elements, or the total request size is below the minimum allowed limit.",