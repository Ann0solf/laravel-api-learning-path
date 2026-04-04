# Step 3: CRUD Operations & Resource Ownership

Before diving into the specific endpoints, you need to understand the rules of engagement for this API. We are building a blog/article system. This means handling data creation, retrieval, modification, and deletion—commonly known as CRUD.

However, a production API is not a public sandbox. We have rules.

## The Core Concept: CRUD

CRUD stands for **Create, Read, Update, Delete**. These four operations map directly to HTTP methods:

- **POST** (Create) - Adding a new article.
- **GET** (Read) - Fetching a list of articles or a specific one.
- **PUT/PATCH** (Update) - Modifying an existing article.
- **DELETE** (Delete) - Removing an article entirely.

If an API can handle these four things reliably, it's functional. If it can handle them securely, it's ready for production.

## Why We Enforce Authentication

In Step 2, we built an authentication system. Now, we actually use it.

Except for reading public data (which might be open depending on your business logic), modifying the database requires a verified identity. Every request to Create, Update, or Delete a post **must** include the Sanctum access token we generated during Login.

The client must attach this to the headers of the request:

```http
Authorization: Bearer {your_access_token_here}
```

If a client attempts to hit a protected route without this token, the API will reject it with a `401 Unauthorized` status. There are no exceptions.

## The Data Relationship: Users & Posts

Data doesn't exist in a vacuum. In this system, a **User** has many **Posts**, and every **Post** belongs to a specific **User**. This relationship is strictly defined at the database level and mapped via Laravel's Eloquent ORM (`HasMany` in the User model and `BelongsTo` in the Post model).

When a client creates a new post, the API does _not_ ask the client to send a `user_id` in the request payload. Trusting the client to dictate who owns the data is a massive security flaw. Instead, the API extracts the identity from the `Bearer` token and automatically binds the authenticated user's ID to the new post during creation (`'user_id' => $user`).

## The Concept of Ownership (Authorization)

Authentication proves _who_ you are. Authorization proves _what_ you are allowed to touch.

This is the most critical concept in this step. Just because a user is logged in (Authenticated), does not mean they have the right to modify any record in the database.

We enforce **Resource Ownership**.

- User A can create a post. User A owns that post.
- User A can edit or delete their own post.
- If User B attempts to send an UPDATE or DELETE request to User A's post, the API intercepts the request, checks the ownership, and blocks it.

If you look at the backend logic for updating or deleting, you will see a strict check:

```php
if ($post->user_id !== Auth::id()) {
    // Blocked.
}
```

When this happens, the API returns a `403 Forbidden` status.

`401 Unauthorized` means "I don't know who you are."
`403 Forbidden` means "I know exactly who you are, and you aren't allowed to do this."

Understand these constraints before interacting with the specific endpoints in the following documentation.
