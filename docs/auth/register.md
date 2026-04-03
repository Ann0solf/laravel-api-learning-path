# Registration

Creates a new user record in the database and immediately issues an API access token.

## Endpoint

**POST** `/api/register`

## Request Payload

**Content-Type:** `application/json`

Since the `confirmed` rule is applied, the client must include a `password_confirmation` field to avoid validation errors (422).

| Field                   | Type   | Rules                         | Description                                                        |
| :---------------------- | :----- | :---------------------------- | :----------------------------------------------------------------- |
| `name`                  | string | required, max:255             | The user's full name.                                              |
| `email`                 | string | required, valid email, unique | Must be a valid format and not already exist in the `users` table. |
| `password`              | string | required, min:6, confirmed    | The password. Must be at least 6 characters.                       |
| `password_confirmation` | string | required                      | Must exactly match the `password` field.                           |

### Example Request

```json
{
    "name": "John Doe",
    "email": "john.doe@example.com",
    "password": "secretpassword123",
    "password_confirmation": "secretpassword123"
}
```

## Responses

### 1. Success (201 Created)

When the data is valid and the database does its job. The API automatically logs the user in by returning a Sanctum token.

```json
{
    "success": true,
    "message": "Register successful",
    "data": {
        "id": 1,
        "name": "John Doe",
        "email": "john.doe@example.com"
    },
    "access_token": "1|laravel_sanctum_token_string_here",
    "token_type": "Bearer"
}
```

The returned token should be used in subsequent requests:

    Authorization: Bearer {token}

### 2. Validation Failed (422 Unprocessable Entity)

When the request contains invalid or incomplete data.

```json
{
    "success": false,
    "message": "Validation failed",
    "errors": {
        "email": ["The email has already been taken."],
        "password": ["The password confirmation does not match."]
    }
}
```

### 3. Server Error (500 Internal Server Error)

When something blows up on the backend (e.g., the database connection drops).

```json
{
    "success": false,
    "message": "Internal server error"
}
```

> [!Note]:
> If `app.debug` is set to true in the environment, the `errors` field will output the actual exception message instead of the generic string.

## Technical Details

- **Pre-emptive Validation:** The controller intercepts bad data immediately using Laravel's `Validator`. If it fails, it halts execution and returns a standardized 422 JSON response. No bad data touches the database.
- **Data Security:** Passwords are never stored in plain text. They are run through Laravel's `Hash::make()` (which defaults to bcrypt or argon2) before insertion. If the database is compromised, attackers will only obtain hashed passwords.
- **Auto-Login:** Instead of forcing the user to hit the `/login` endpoint right after registering, the function generates a `plainTextToken` (named `api-token`) via Sanctum and returns it immediately.
- **Fail-Safe Execution:** The database insertion and token generation are wrapped in a `try-catch` block. It catches generic exceptions and uses a ternary operator on `config('app.debug')` to ensure sensitive stack traces are never leaked to production environments.

```

```
