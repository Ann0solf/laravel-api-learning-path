# Delete Post

Deletes an existing post owned by the authenticated user.

## Endpoint

**DELETE** `/api/posts/{id}`

## Authentication

**Required.** You must pass a valid Sanctum token in the request headers.

```http
Authorization: Bearer {your_access_token_here}
```

## URL Parameters

| Parameter | Type    | Description                          |
| :-------- | :------ | :----------------------------------- |
| `id`      | integer | The unique ID of the post to delete. |

## Responses

### 1. Success (200 OK)

Returned when the post is successfully deleted.

```json
{
    "success": true,
    "message": "Article deleted"
}
```

### 2. Unauthorized (401 Unauthorized)

Returned if no valid authentication token is provided.

```json
{
    "message": "Unauthenticated."
}
```

### 3. Forbidden (403 Forbidden)

Returned when the authenticated user does not own the post.

```json
{
    "success": false,
    "message": "Forbidden"
}
```

### 4. Not Found (404 Not Found)

Returned when the specified post ID does not exist.

```json
{
    "success": false,
    "message": "Post not found"
}
```

> [!Note]
> If using Laravel's default `findOrFail()` without custom handling, the response format may differ.

### 5. Server Error (500 Internal Server Error)

Returned when the server fails to delete the record.

```json
{
    "success": false,
    "message": "Internal server error"
}
```

## Technical Details

- **Ownership Enforcement:** The API verifies that the authenticated user owns the post before allowing deletion. If the `user_id` does not match `Auth::id()`, the request is blocked with a 403 response.

- **Safe Deletion:** The deletion process is wrapped in a `try-catch` block to handle unexpected failures (e.g., database issues) and prevent application crashes.

- **Permanent Removal:** This endpoint permanently deletes the record from the database. There is no soft delete implemented by default. If you require recovery functionality, consider using Laravel's `SoftDeletes` feature.
