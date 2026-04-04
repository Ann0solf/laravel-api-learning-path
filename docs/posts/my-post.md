# My Posts

Retrieves a paginated list of posts created by the authenticated user.

## Endpoint

**GET** `/api/my-posts`

## Authentication

**Required.** You must pass a valid Sanctum token in the request headers.

```http
Authorization: Bearer {your_access_token_here}
```

## Query Parameters (Optional)

| Parameter | Type    | Description                                               |
| :-------- | :------ | :-------------------------------------------------------- |
| `page`    | integer | Specifies the page number for pagination. Default is `1`. |

## Example Request

```http
GET /api/my-posts?page=1
Authorization: Bearer 1|laravel_sanctum_token_string_here
Accept: application/json
```

## Responses

### 1. Success (200 OK)

Returned when the request is successful. The response includes paginated data.

```json
{
    "success": true,
    "data": {
        "current_page": 1,
        "data": [
            {
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
        ],
        "first_page_url": "http://localhost:8000/api/my-posts?page=1",
        "from": 1,
        "last_page": 1,
        "last_page_url": "http://localhost:8000/api/my-posts?page=1",
        "links": [
            {
                "url": null,
                "label": "&laquo; Previous",
                "active": false
            },
            {
                "url": "http://localhost:8000/api/my-posts?page=1",
                "label": "1",
                "active": true
            },
            {
                "url": null,
                "label": "Next &raquo;",
                "active": false
            }
        ],
        "next_page_url": null,
        "path": "http://localhost:8000/api/my-posts",
        "per_page": 15,
        "prev_page_url": null,
        "to": 1,
        "total": 1
    }
}
```

### 2. Unauthorized (401 Unauthorized)

Returned if no valid authentication token is provided.

```json
{
    "message": "Unauthenticated."
}
```

## Technical Details

- **User-Scoped Data:** This endpoint strictly returns posts that belong to the currently authenticated user (`user_id = Auth::id()`), ensuring data isolation between users.

- **Pagination:** Results are paginated using Laravel's built-in `paginate(15)` method. This prevents large data loads and improves performance.

- **Sorting:** Posts are returned in descending order (`latest()`), meaning the newest posts appear first.

- **Security Context:** Unlike the public `/api/posts` endpoint, this route is fully protected and intended for personal dashboards or user-specific views.
