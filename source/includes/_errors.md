# Errors

```json
{
  "error": {
    "status": 400,
    "message": "That's not gone well"
  }
}
```

Successful API calls will normally return a 200 status. Failed API calls will return an alternate status as detailed below. In addition For failed API calls, an `error` JSON response will be returned including a descriptive message.

Error Code | Meaning
---------- | -------
401 | Unauthorized -- Check user email and password credentials.
403 | Not allowed -- Check API token.
404 | Not Found -- Check email address or password reset.
422 | Unprocessable -- Validation rules failed.
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- Temporarily offline for maintenance. Try again later.
