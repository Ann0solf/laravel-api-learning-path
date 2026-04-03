# Login

Authenticates an existing user and issues a new API access token for making authenticated requests.

## Endpoint

**POST** `/api/login`

## Request Payload

**Content-Type:** `application/json`

| Field      | Type   | Rules                 | Description                                         |
| :--------- | :----- | :-------------------- | :-------------------------------------------------- |
| `email`    | string | required, valid email | The email address associated with the user account. |
| `password` | string | required, min:6       | The user's password.                                |

### Example Request

```json
{
    "email": "john.doe@example.com",
    "password": "secretpassword123"
}
```

## Responses

### 1. Success (200 OK)

Returned when the provided credentials are valid.

```json
{
    "success": true,
    "data": {
        "id": 1,
        "name": "John Doe",
        "email": "john.doe@example.com"
    },
    "message": "Login successful",
    "access_token": "2|laravel_sanctum_token_string_here",
    "token_type": "Bearer"
}
```

The returned token should be used in subsequent requests:

```http
Authorization: Bearer {token}
```

### 2. Validation Failed (422 Unprocessable Entity)

Returned when the request payload is missing required fields or uses an invalid data format.

```json
{
    "success": false,
    "message": "Validation failed",
    "errors": {
        "email": ["The email field is required."]
    }
}
```

### 3. Invalid Credentials (401 Unauthorized)

Returned when the provided email and password combination does not match any existing records in the database.

```json
{
    "success": false,
    "message": "Invalid credentials"
}
```

## Technical Details

- **Input Validation:** The controller utilizes Laravel's `Validator` to ensure required fields are present before attempting authentication, returning a standardized 422 response if it fails.
- **Authentication Attempt:** The `Auth::attempt()` method evaluates the credentials. It automatically retrieves the user by the provided email and securely compares the provided password against the hashed password stored in the database.
- **Token Generation:** Upon successful authentication, Laravel Sanctum generates a new plain text token (`api-token`). The client application must store this token and include it in the `Authorization` header as a `Bearer` token for all subsequent protected API calls.
- **Multiple Tokens:** Each successful login generates a new token. Previous tokens remain valid unless explicitly revoked.
