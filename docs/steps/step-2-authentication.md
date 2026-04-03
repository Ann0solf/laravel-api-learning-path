# Step 2: Authentication

## The Concept of Auth

Before writing any code, let's understand the purpose of authentication.

Authentication is the process of verifying _who_ is making a request. Without it, anyone could access or modify your data, which is a terrible idea.

Instead of sending email and password on every request, the API issues a token after successful login. This token acts as a secure identifier for subsequent requests.

---

## The Flow: Login & Register

### 1. Registration (Creating a new account)

- The client sends user data (name, email, password)
- The API validates the input and ensures the email is unique
- The password is hashed before storing in the database
- The API saves the user and returns a success response

### 2. Login (Obtaining an access token)

- The client sends email and password
- The API verifies the credentials against stored data
- If valid, the API generates an **Access Token**
- If invalid, the API returns a `401 Unauthorized` response

### 3. Authenticated Requests (Using the token)

- The client includes the token in the `Authorization` header:

    Authorization: Bearer {token}

- The API validates the token before processing the request

---

## Why Laravel Sanctum?

Laravel provides multiple authentication options, including Passport.

Laravel Passport is designed for full OAuth2 implementations, which can be complex and unnecessary for simple APIs.

**Sanctum**, on the other hand, is lightweight and designed specifically for:

- Single Page Applications (SPA)
- Mobile applications
- Simple token-based APIs

It allows you to issue and manage API tokens without dealing with complex OAuth configurations, making it a practical choice for most projects.
