---
title: YourFeed API Reference

language_tabs:
  - http

toc_footers:
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the API documentation for YourFeed. This is a work in progress so please check back for regular updates.


# Authentication

```http
POST /users/authenticate HTTPS/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Authorization: 51edfaa56d5c4e727792
Host: api.yourfeed.com
```

> Contact YourFeed to obtain an API token

All API clients will be issued with an API token (contact YourFeed directly to arrange this). The API token should be sent as an HTTP header with all requests. Requests to the API are only supported over HTTPS to protect the security of this token; it should not be disclosed to any third parties or committed to source control.

The header should look like this:
`Authorization: 51edfaa56d5c4e727792`

<aside class="notice">
You must replace the example above with your personal API key.
</aside>


# User API

## Create new user

```http
POST /users HTTPS/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Authorization: <YOUR API ACCESS TOKEN>
Host: api.yourfeed.com
```

> Returns a 200 success JSON response:

```json
  {
    "id": "00ec95b8-ee36-40d5-9bf8-fee74d22ed1a",
    "email": "someone@somewhere.com",
    "firstName": "Bobby",
    "lastName": "Tables",
    "token": "$2a$11$VdSCQdulx0qaiNJzhM/SiOjNe3RBWHwZ0vObD3eC6XiDAk3d0YBmi"
  }
```

This endpoint may be used to create a new user using supplied credentials and additional user details. If these are valid a new user is created (allocating a new unique id) and a JSON Web Token is returned as part of the response; this may then be used for further API calls with that user.

If any of the user details are invalid an error response is returned - this will include a list of error messages that may be presented to the user.

### HTTP Request

`POST https://api.yourfeed.com/users`

### Parameters

Parameter | Description
--------- | -----------
email | Email address provided by user
password | Password provided by user
firstName | First name provided by user
lastName | Last name provided by user

All parameters are required.

### Errors

Scenario | Status | Message
-------- | ------ | -------
Invalid user | 422 | `["Password cannot be blank", "Password is too short"]`
Email already registered | 422 | `["Email address has already been taken"]`

<aside class="notice">
Passwords are case sensitive, must be at least 6 characters long (maximum 128 characters), and contain at least one letter and one number.
</aside>

<aside class="warning">
Password confirmation is not supported in the API; if required this should be implemented outside of the API, checking that password and password confirmation fields match. Pass only a single password parameter in the API request.
</aside>


## Authenticate user

```http
POST /users/authenticate HTTPS/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Authorization: <YOUR API ACCESS TOKEN>
Host: api.yourfeed.com
```

> Returns a 200 success JSON response:

```json
  {
    "id": "00ec95b8-ee36-40d5-9bf8-fee74d22ed1a",
    "email": "someone@somewhere.com",
    "firstName": "Bobby",
    "lastName": "Tables",
    "token": "$2a$11$VdSCQdulx0qaiNJzhM/SiOjNe3RBWHwZ0vObD3eC6XiDAk3d0YBmi"
  }
```

This endpoint may be used to check user credentials (email address and password). If these are valid a JSON Web Token is returned as part of the response; this may then be used for further API calls with that user. If the credentials are invalid an error response is returned - this is deliberately vague and does not distinguish between unknown email address and invalid password for a known email.

### HTTP Request

`POST https://api.yourfeed.com/users/authenticate`

### Parameters

Parameter | Description
--------- | -----------
email | Users email address
password | Password provided by user

### Errors

Scenario | Status | Message
-------- | ------ | -------
Invalid credentials | 401 | `"Invalid email/password combination"`


## Reset password

```http
POST /users/password HTTPS/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Authorization: <YOUR API ACCESS TOKEN>
Host: api.yourfeed.com
```

> Returns a 200 success JSON response:

```json
  {
    "email": "someone@somewhere.com"
  }
```

This endpoint may be used to request a password reset email for the specified user. The email will include a link for the user to reset their password; this should link to a page hosted outside the API where users may provide a new password. The link will include a query string ending with the `token` parameter e.g.

`<a href="https://partner.domain.com/password/reset?token=2abfea1648dce807802ea5351d2baa5d9b1d0adf0e62a717e183edfaa61a44829216b3361cad6c5f9aba1a6aa3a20b545122bcc64123cdd5b51ba619a2cc063e">Change my password</a>`

If the email address could not be found an error response is returned instead and no email is sent.

### HTTP Request

`POST https://api.yourfeed.com/users/password`

### Parameters

Parameter | Description
--------- | -----------
email | Email address provided by user

### Errors

Scenario | Status | Message
-------- | ------ | -------
Invalid credentials | 404 | `"Unknown email address"`

<aside class="notice">
The password reset landing page must be hosted at a URL supporting https protocol. Please contact YourFeed to configure this URL.
</aside>

<aside class="warning">
Although the API response may be used to indicate that an unknown email address was provided by the user, it is strongly recommended to provide a generic response to the user regardless of this to prevent fishing for known email accounts.
</aside>


## Update password

```http
PUT /users/password HTTPS/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Authorization: <YOUR API ACCESS TOKEN>
Host: api.yourfeed.com
```

> Returns a 200 success JSON response:

```json
  {
    "id": "00ec95b8-ee36-40d5-9bf8-fee74d22ed1a",
    "email": "someone@somewhere.com",
    "firstName": "Bobby",
    "lastName": "Tables"
  }
```

This endpoint may be used to complete a forgotten password request by passing a new password entered by the user and the password reset token provided in the email link. If the new password and token are valid the user will be updated and they may then authenticate in a subsequent request using their new credentials.

If the token is invalid, has expired or the new password is not valid an error response is returned instead.

### HTTP Request

`PUT https://api.yourfeed.com/users/password`

### Parameters

Parameter | Description
--------- | -----------
token | Password reset request token provided in previous email to user
password | New password provided by user

### Errors

Scenario | Status | Message
-------- | ------ | -------
Invalid token or password | 422 | `"Failed to change password"`

<aside class="notice">
Password reset tokens may only be used once and expire after 6 hours.
</aside>

<aside class="warning">
Password confirmation is not supported in the API; if required this should be implemented outside of the API, checking that password and password confirmation fields match. Pass only a single password parameter in the API request.
</aside>
