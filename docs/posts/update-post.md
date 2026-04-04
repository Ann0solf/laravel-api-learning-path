# Update Post

Updates an existing post owned by the authenticated user.

## Endpoint

**PUT / PATCH** `/api/posts/{id}`

## Authentication

**Required.** You must pass a valid Sanctum token in the request headers.

```http
Authorization: Bearer {your_access_token_here}
```

## URL Parameters

| Parameter | Type    | Description                          |
| :-------- | :------ | :----------------------------------- |
| `id`      | integer | The unique ID of the post to update. |

## Request Payload

**Content-Type:** `multipart/form-data`

> [!Note]
> Use `multipart/form-data` when sending an image file. If no image is included, you may still use form-data for consistency.

| Field          | Type    | Rules                                           | Description                                    |
| :------------- | :------ | :---------------------------------------------- | :--------------------------------------------- |
| `title`        | string  | required, max:255                               | Updated title of the post.                     |
| `content`      | string  | required                                        | Updated content of the post.                   |
| `image`        | file    | optional, image (jpeg, png, jpg, gif), max: 2MB | New image file (replaces old one if provided). |
| `is_published` | boolean | optional                                        | Set publish status (`1` or `0`).               |

### Example Request

```text
title: "Updated Laravel Sanctum Guide"
content: "Updated content here..."
image: cover-new.jpg
is_published: 1
```

## Responses

### 1. Success (200 OK)

Returned when the post is successfully updated.

```json
{
    "success": true,
    "message": "Article updated",
    "data": {
        "id": 15,
        "user_id": 1,
        "title": "Updated Laravel Sanctum Guide",
        "slug": "updated-laravel-sanctum-guide",
        "content": "Updated content here...",
        "image": "posts/1715439999_cover-new.jpg",
        "is_published": true,
        "created_at": "2026-04-04T09:15:00.000000Z",
        "updated_at": "2026-04-04T10:00:00.000000Z"
    }
}
```

### 2. Validation Failed (422 Unprocessable Entity)

Returned when the request payload is invalid.

```json
{
    "success": false,
    "message": "Validation failed",
    "errors": {
        "title": ["The title field is required."]
    }
}
```

### 3. Unauthorized (401 Unauthorized)

Returned if no valid authentication token is provided.

```json
{
    "message": "Unauthenticated."
}
```

### 4. Forbidden (403 Forbidden)

Returned when the authenticated user does not own the post.

```json
{
    "success": false,
    "message": "Forbidden"
}
```

### 5. Not Found (404 Not Found)

Returned when the specified post ID does not exist.

```json
{
    "success": false,
    "message": "Post not found"
}
```

> [!Note]
> If using Laravel's default `findOrFail()` without custom handling, the response format may differ.

### 6. Server Error (500 Internal Server Error)

Returned when an unexpected error occurs during update.

```json
{
    "success": false,
    "message": "Internal server error"
}
```

## Technical Details

- **Ownership Enforcement:** Before updating, the API verifies that the authenticated user owns the post (`user_id === Auth::id()`). If not, the request is blocked with a 403 response.

- **Slug Regeneration:** If the `title` is changed, the API automatically regenerates the slug using `Str::slug()`. If the generated slug already exists, a numeric suffix is appended to ensure uniqueness.

- **Image Replacement:** If a new image is uploaded, it replaces the previous one. If no image is provided, the existing image is retained.

- **Safe Update Execution:** The update operation is wrapped in a `try-catch` block to prevent application crashes and ensure controlled error responses.
