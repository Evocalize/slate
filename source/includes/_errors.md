# Errors

We return the appropriate HTTP Status code when we return an error. Additionally we do our best to provide a human
readable error message to help you figure out what went wrong.

| Error Code                            | HTTP Status Code | Meaning                                                               |
|---------------------------------------|------------------|-----------------------------------------------------------------------|
| EV_BAD_REQUEST_INVALID_FIELDS         | 400              | Request is missing required fields or has failed validation check.    |
| EV_BAD_REQUEST_INVALID_USER_IDENTITY  | 400              | Request contains an invalid user or group, e.g., user does not exist. |
| EV_BAD_REQUEST_TOO_MANY_ITEMS         | 400              | Batch request contains too many items.                                |
| EV_BAD_REQUEST_MALFORMED              | 400              | Malformed request, e.g. a type mismatch.                              |
| EV_BAD_REQUEST_DUPLICATE              | 400              | An object with a different id already exists.                         |
| EV_BAD_REQUEST_INVALID_BUDGET         | 400              | The requested budget change is invalid.                               |
| EV_BAD_REQUEST_INVALID_SCHEDULE       | 400              | The requested schedule change is invalid.                             |
| EV_BAD_REQUEST_INVALID_VARIABLE_VALUE | 400              | The requested variable value change is invalid.                       |
| EV_OBJECT_NOT_EDITABLE_TEMPORARILY    | 400              | The object cannot be edited at the moment. Please try again later.    |
| EV_OBJECT_NOT_FOUND                   | 404              | Unable to locate requested resource.                                  |
| EV_UNAUTHORIZED_INVALID_KEY           | 401              | Provided Client Key Id is invalid.                                    |
| EV_UNAUTHORIZED_MISSING_HEADERS       | 401              | Request is missing one of the required headers.                       |
| EV_UNAUTHORIZED_INVALID_SIGNATURE     | 401              | Signature provided in `X-Evocalize-Signature` is not valid.           |
| EV_UNAUTHORIZED_INVALID_SECRET        | 401              | Secret provided in `X-Evocalize-Client-Key` is not valid.             |
| EV_UNAUTHORIZED_EXPIRED_REQUEST       | 401              | Request timestamp is over 1 minute old.                               |
| EV_INTERNAL_SERVER_ERROR              | 500              | We had a problem with our server. Try again later.                    |
