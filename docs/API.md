# API Documentation

## Base URL

```
http://localhost:3001/api
```

## Authentication

All authenticated routes require a JWT token sent via HTTP-only cookies.

### Authentication Flow

1. User registers or logs in
2. Server returns JWT token as HTTP-only cookie
3. Client includes cookie in subsequent requests
4. Server validates token via AuthMiddleware

## Authentication Routes

### POST /auth/signup

Register a new user account.

**Request Body:**

```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response (201):**

```json
{
  "user": {
    "id": "user_id",
    "email": "user@example.com",
    "profileSetup": false
  }
}
```

**Error Response (400):**

```json
{
  "message": "User with this email already exists"
}
```

### POST /auth/login

Authenticate existing user.

**Request Body:**

```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response (200):**

```json
{
  "user": {
    "id": "user_id",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "image": "profile_image_url",
    "color": 1,
    "profileSetup": true
  }
}
```

### GET /auth/user-info

Get current user information (authenticated).

**Headers:**

```
Cookie: jwt=<token>
```

**Response (200):**

```json
{
  "id": "user_id",
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "image": "profile_image_url",
  "color": 1,
  "profileSetup": true
}
```

### POST /auth/update-profile

Update user profile information (authenticated).

**Request Body:**

```json
{
  "firstName": "John",
  "lastName": "Doe",
  "color": 1
}
```

**Response (200):**

```json
{
  "id": "user_id",
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "color": 1,
  "profileSetup": true
}
```

### POST /auth/add-profile-image

Upload profile image (authenticated).

**Content-Type:** `multipart/form-data`

**Request Body:**

```
profile-image: <file>
```

**Response (200):**

```json
{
  "image": "uploads/profiles/filename.jpg"
}
```

### DELETE /auth/remove-profile-image

Remove user's profile image (authenticated).

**Response (200):**

```json
{
  "message": "Profile image removed successfully"
}
```

### POST /auth/logout

Logout user and clear JWT cookie.

**Response (200):**

```json
{
  "message": "Logout successful"
}
```

## Contact Routes

### POST /contacts/search

Search for users to add as contacts (authenticated).

**Request Body:**

```json
{
  "searchTerm": "john"
}
```

**Response (200):**

```json
{
  "contacts": [
    {
      "id": "user_id",
      "email": "john@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "image": "profile_image_url",
      "color": 1
    }
  ]
}
```

### GET /contacts/get-contacts-for-dm

Get list of users that have had conversations with current user (authenticated).

**Response (200):**

```json
{
  "contacts": [
    {
      "id": "user_id",
      "email": "contact@example.com",
      "firstName": "Contact",
      "lastName": "User",
      "image": "profile_image_url",
      "color": 2,
      "lastMessageTime": "2023-12-01T10:00:00Z"
    }
  ]
}
```

## Message Routes

### POST /messages/get-messages

Get conversation messages between current user and another user (authenticated).

**Request Body:**

```json
{
  "id": "other_user_id"
}
```

**Response (200):**

```json
{
  "messages": [
    {
      "_id": "message_id",
      "sender": {
        "id": "sender_id",
        "email": "sender@example.com",
        "firstName": "Sender",
        "lastName": "Name",
        "image": "profile_image_url",
        "color": 1
      },
      "recipient": {
        "id": "recipient_id",
        "email": "recipient@example.com",
        "firstName": "Recipient",
        "lastName": "Name",
        "image": "profile_image_url",
        "color": 2
      },
      "messageType": "text",
      "content": "Hello, how are you?",
      "timestamp": "2023-12-01T10:00:00Z"
    }
  ]
}
```

### POST /messages/upload-file

Upload a file for sharing in chat (authenticated).

**Content-Type:** `multipart/form-data`

**Request Body:**

```
file: <file>
```

**Response (200):**

```json
{
  "filePath": "uploads/files/filename.ext"
}
```

## Error Responses

### 400 Bad Request

```json
{
  "message": "Validation error message"
}
```

### 401 Unauthorized

```json
{
  "message": "Token is required"
}
```

### 403 Forbidden

```json
{
  "message": "Invalid token"
}
```

### 404 Not Found

```json
{
  "message": "Resource not found"
}
```

### 500 Internal Server Error

```json
{
  "message": "Internal server error"
}
```

## Rate Limiting

Currently, no rate limiting is implemented. Consider adding rate limiting middleware for production use.

## File Upload Specifications

### Supported File Types

- Images: jpg, jpeg, png, gif, webp
- Documents: pdf, doc, docx, txt
- Archives: zip, rar

### File Size Limits

- Profile images: 5MB maximum
- Shared files: 10MB maximum

### File Storage

Files are stored in the `uploads/` directory with organized subdirectories:

- `uploads/profiles/` - Profile images
- `uploads/files/` - Shared files

Files are served as static content through Express.js static middleware.
