# Get Post Detail

Retrieves a specific post by its unique ID.

## Endpoint

**GET** `/api/posts/{id}`

## Authentication

Not required for published posts.

If your application supports private or unpublished posts, authentication and additional authorization checks may be required.

## URL Parameters

| Parameter | Type    | Description                                 |
| :-------- | :------ | :------------------------------------------ |
| `id`      | integer | The unique database identifier of the post. |

### Example Request

```http
GET /api/posts/15
Accept: application/json
```

### Example Request (Authenticated)

```http
GET /api/posts/15
Authorization: Bearer 1|laravel_sanctum_token_string_here
Accept: application/json
```

## Responses

### 1. Success (200 OK)

Returned when the post is successfully found.

```json
{
    "success": true,
    "data": {
        "id": 15,
        "user_id": 1,
        "title": "Understanding Laravel Sanctum",
        "slug": "understanding-laravel-sanctum",
        "content": "Sanctum provides a featherweight authentication system...",
        "image": "posts/1715432100_cover.jpg",
        "is_published": true,
        "created_at": "2026-04-04T09:15:00.000000Z",
        "updated_at": "2026-04-04T09:15:00.000000Z"
    }
}
```

### 2. Not Found (404 Not Found)

Returned when the requested ID does not exist in the database.

```json
{
    "success": false,
    "message": "Post not found"
}
```

> [!Note]
> If you are using Laravel's default `findOrFail()` without custom exception handling, the response format may differ from the example above.

### 3. Unauthorized (401 Unauthorized)

Returned if authentication is required but no valid token is provided.

```json
{
    "message": "Unauthenticated."
}
```

## Technical Details

- **Resource Resolution:** The controller uses Eloquent's `findOrFail($id)` method to retrieve the post by its primary key. If the record does not exist, Laravel throws a `ModelNotFoundException`, which should be handled and formatted into a proper 404 JSON response.
- **Visibility Logic:** In a real-world scenario, only published posts (`is_published = true`) should be publicly accessible. Draft or private posts should require authentication and proper authorization checks.
