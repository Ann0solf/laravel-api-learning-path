# Create Post

Creates a new article and automatically associates it with the currently authenticated user.

## Endpoint

**POST** `/api/posts`

## Authentication

**Required.** You must pass a valid Sanctum token in the request headers.

```http
Authorization: Bearer {your_access_token_here}
```

## Request Payload

**Content-Type:** `multipart/form-data`

> \[!NOTE]
> Do not send `application/json`. Because this endpoint accepts image files, you must use `multipart/form-data`. If you use JSON, the image upload will fail.

| Field          | Type    | Rules                                           | Description                                                                       |
| :------------- | :------ | :---------------------------------------------- | :-------------------------------------------------------------------------------- |
| `title`        | string  | required, max:255                               | The title of the article.                                                         |
| `content`      | string  | required                                        | The main body text of the article.                                                |
| `image`        | file    | optional, image (jpeg, png, jpg, gif), max: 2MB | A cover image for the post.                                                       |
| `is_published` | boolean | optional                                        | Set to `1` (true) or `0` (false) to determine if the post is immediately visible. |

### Example Request (Form Data Representation)

```text
title: "Understanding Laravel Sanctum"
content: "Sanctum provides a featherweight authentication system..."
image: [file]
is_published: 1
```

## Responses

### 1. Success (201 Created)

Returned when the validation passes, the image (if provided) is successfully saved to the server, and the database record is created.

```json
{
    "success": true,
    "message": "Post created successfully",
    "data": {
        "user_id": 1,
        "title": "Understanding Laravel Sanctum",
        "slug": "understanding-laravel-sanctum",
        "content": "Sanctum provides a featherweight authentication system...",
        "image": "posts/1715432100_cover.jpg",
        "is_published": true,
        "updated_at": "2026-04-04T09:15:00.000000Z",
        "created_at": "2026-04-04T09:15:00.000000Z",
        "id": 15
    }
}
```

### 2. Validation Failed (422 Unprocessable Entity)

Returned when required fields are missing, the title is too long, or the uploaded file is not a supported image format or exceeds the 2MB size limit.

```json
{
    "success": false,
    "message": "Validation failed",
    "errors": {
        "image": ["The image must not be greater than 2048 kilobytes."]
    }
}
```

### 3. Server Error (500 Internal Server Error)

Returned if the server fails to write the image to the disk or encounters a database execution error.

```json
{
    "success": false,
    "message": "Internal server error"
}
```

### 4. Unauthorized (401 Unauthorized)

Returned if authentication is required but no valid token is provided.

```json
{
    "message": "Unauthenticated."
}
```

## Technical Details

- **Auto-generated Slug:** The client does not need to provide a URL slug. The API automatically generates a URL-friendly slug based on the `title` using Laravel's `Str::slug()`. If a post with that slug already exists, the API will automatically append a numeric counter (e.g., `my-title-1`, `my-title-2`) to ensure database uniqueness before insertion.
- **Image Handling:** If an image is included, the API renames the file using the current Unix timestamp concatenated with the original filename to prevent naming collisions. It is then securely stored in the `public/posts` directory.
- **Automatic Ownership Assignment:** The `user_id` is never accepted from the request payload. It is securely extracted from the authenticated user's session (`Auth::id()`) and forcefully applied to the new record.
