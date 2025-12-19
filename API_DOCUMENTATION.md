# Spotera API Documentation
## iOS Developer Guide

**Version:** 1.0
**Base URL:** `https://api.spotera.com/api/v1`
**Last Updated:** December 2024

---

## Table of Contents
1. [Authentication](#1-authentication)
2. [Users](#2-users)
3. [Restaurants](#3-restaurants)
4. [Reviews](#4-reviews)
5. [Posts & Feed](#5-posts--feed)
6. [Notifications](#6-notifications)
7. [WebSocket Events](#7-websocket-events)
8. [Data Models](#8-data-models)
9. [Error Handling](#9-error-handling)

---

## General Information

### Base URL
```
Production: https://api.spotera.com/api/v1
Development: http://localhost:3000/api/v1
```

### Authentication
API uses JWT Bearer Token authentication. Include the token in the Authorization header:
```
Authorization: Bearer <access_token>
```

### Response Format
All responses follow this structure:
```json
{
  "success": true,
  "data": { ... },
  "message": "Optional message"
}
```

### Pagination
Paginated endpoints accept these query parameters:
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | integer | 1 | Page number |
| limit | integer | 20 | Items per page (max: 100) |

Paginated response includes:
```json
{
  "success": true,
  "data": {
    "data": [...],
    "meta": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "totalPages": 5
    }
  }
}
```

---

## 1. Authentication

### 1.1 Register
Create a new user account.

**Endpoint:** `POST /auth/register`
**Auth Required:** No

**Request Body:**
```json
{
  "username": "ayse_yilmaz",
  "name": "Ayşe Yılmaz",
  "email": "ayse@email.com",
  "password": "Password123",
  "bio": "Yemek severim 🍴"
}
```

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| username | string | Yes | 3-30 chars, alphanumeric + underscore |
| name | string | Yes | 2-100 chars |
| email | string | Yes | Valid email format |
| password | string | Yes | Min 8 chars, 1 uppercase, 1 number |
| bio | string | No | Max 500 chars |

**Response (201):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "username": "ayse_yilmaz",
      "name": "Ayşe Yılmaz",
      "email": "ayse@email.com",
      "bio": "Yemek severim 🍴",
      "avatarUrl": null,
      "credibilityScore": 0,
      "totalReviews": 0,
      "verifiedReviews": 0,
      "followersCount": 0,
      "followingCount": 0,
      "isInfluencer": false,
      "isVerified": false,
      "createdAt": "2024-01-15T10:30:00Z"
    },
    "tokens": {
      "accessToken": "eyJhbGciOiJIUzI1NiIs...",
      "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
      "expiresIn": 900
    }
  }
}
```

---

### 1.2 Login
Authenticate user and get tokens.

**Endpoint:** `POST /auth/login`
**Auth Required:** No

**Request Body:**
```json
{
  "email": "ayse@email.com",
  "password": "Password123"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "username": "ayse_yilmaz",
      "name": "Ayşe Yılmaz",
      "email": "ayse@email.com",
      "bio": "Yemek severim 🍴",
      "avatarUrl": "https://...",
      "credibilityScore": 0,
      "totalReviews": 5,
      "verifiedReviews": 3,
      "followersCount": 45,
      "followingCount": 120,
      "isInfluencer": false,
      "isVerified": false,
      "createdAt": "2024-01-15T10:30:00Z"
    },
    "tokens": {
      "accessToken": "eyJhbGciOiJIUzI1NiIs...",
      "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
      "expiresIn": 900
    }
  }
}
```

**Error (401):**
```json
{
  "success": false,
  "message": "Invalid credentials"
}
```

---

### 1.3 Logout
Invalidate refresh token.

**Endpoint:** `POST /auth/logout`
**Auth Required:** Yes

**Request Body:**
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Logged out successfully"
  }
}
```

---

### 1.4 Refresh Token
Get new access token using refresh token.

**Endpoint:** `POST /auth/refresh-token`
**Auth Required:** No

**Request Body:**
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "user": { ... },
    "tokens": {
      "accessToken": "eyJhbGciOiJIUzI1NiIs...",
      "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
      "expiresIn": 900
    }
  }
}
```

---

### 1.5 Forgot Password
Request password reset email.

**Endpoint:** `POST /auth/forgot-password`
**Auth Required:** No

**Request Body:**
```json
{
  "email": "ayse@email.com"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "If the email exists, a password reset link will be sent"
  }
}
```

---

## 2. Users

### 2.1 Get User Profile
Get user profile by ID.

**Endpoint:** `GET /users/:id`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "username": "hakan_guntulun",
    "name": "Hakan Güntulun",
    "email": "hakan@email.com",
    "bio": "Lezzet Avcısı | İstanbul'un en iyi restoranlarını keşfediyorum 🍽️",
    "avatarUrl": "https://...",
    "credibilityScore": 4.9,
    "totalReviews": 312,
    "verifiedReviews": 305,
    "followersCount": 2100000,
    "followingCount": 89,
    "isInfluencer": true,
    "isVerified": true,
    "createdAt": "2024-01-15T10:30:00Z",
    "isFollowing": true,
    "isOwnProfile": false
  }
}
```

---

### 2.2 Update User Profile
Update current user's profile.

**Endpoint:** `PUT /users/:id`
**Auth Required:** Yes (own profile only)

**Request Body:**
```json
{
  "name": "Ayşe Yılmaz Updated",
  "username": "ayse_new",
  "bio": "Updated bio",
  "avatarUrl": "https://..."
}
```

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| name | string | No | 2-100 chars |
| username | string | No | 3-30 chars, unique |
| bio | string | No | Max 500 chars |
| avatarUrl | string | No | Max 500 chars |

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "username": "ayse_new",
    "name": "Ayşe Yılmaz Updated",
    ...
  }
}
```

---

### 2.3 Get Followers
Get list of user's followers.

**Endpoint:** `GET /users/:id/followers`
**Auth Required:** Yes
**Pagination:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": "uuid",
        "username": "user1",
        "name": "User One",
        "avatarUrl": "https://...",
        "credibilityScore": 4.5,
        "isInfluencer": false,
        "isVerified": false
      }
    ],
    "meta": {
      "page": 1,
      "limit": 20,
      "total": 45,
      "totalPages": 3
    }
  }
}
```

---

### 2.4 Get Following
Get list of users being followed.

**Endpoint:** `GET /users/:id/following`
**Auth Required:** Yes
**Pagination:** Yes

**Response:** Same structure as Get Followers

---

### 2.5 Follow User
Follow another user.

**Endpoint:** `POST /users/:id/follow`
**Auth Required:** Yes

**Response (201):**
```json
{
  "success": true,
  "data": {
    "message": "Followed successfully"
  }
}
```

**Error (409):**
```json
{
  "success": false,
  "message": "Already following this user"
}
```

---

### 2.6 Unfollow User
Unfollow a user.

**Endpoint:** `DELETE /users/:id/follow`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Unfollowed successfully"
  }
}
```

---

### 2.7 Get User Posts
Get posts by a specific user.

**Endpoint:** `GET /users/:id/posts`
**Auth Required:** Yes
**Pagination:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": "uuid",
        "content": "Bu mekanı keşfettim!",
        "imageUrl": "https://...",
        "likesCount": 45200,
        "commentsCount": 892,
        "sharesCount": 234,
        "createdAt": "2024-01-15T10:30:00Z",
        "user": { ... },
        "restaurant": { ... }
      }
    ],
    "meta": { ... }
  }
}
```

---

### 2.8 Get User Reviews
Get reviews by a specific user.

**Endpoint:** `GET /users/:id/reviews`
**Auth Required:** Yes
**Pagination:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": "uuid",
        "rating": 5,
        "content": "Harika bir deneyimdi!",
        "likesCount": 23,
        "dislikesCount": 1,
        "isVerified": true,
        "createdAt": "2024-01-15T10:30:00Z",
        "user": { ... },
        "restaurant": { ... },
        "influencer": { ... }
      }
    ],
    "meta": { ... }
  }
}
```

---

## 3. Restaurants

### 3.1 List Restaurants
Get all restaurants with pagination.

**Endpoint:** `GET /restaurants`
**Auth Required:** Yes
**Pagination:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": "uuid",
        "name": "Poyraz Çapari",
        "category": "Balık",
        "description": "Deniz kenarında harika bir balık restoranı",
        "address": "Poyrazköy Sahil, İstanbul",
        "latitude": 41.1802,
        "longitude": 29.1232,
        "priceRange": "$$",
        "imageUrl": "https://...",
        "userRating": 4.8,
        "influencerScore": 4.9,
        "reviewCount": 567,
        "phone": "+90 212 123 45 67",
        "website": "https://poyrazcapari.com",
        "openingHours": null,
        "createdAt": "2024-01-15T10:30:00Z"
      }
    ],
    "meta": { ... }
  }
}
```

---

### 3.2 Get Restaurant Detail
Get detailed information about a restaurant.

**Endpoint:** `GET /restaurants/:id`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Poyraz Çapari",
    "category": "Balık",
    "description": "Poyrazköy Sahil'de yer alan, deniz ürünleri ve balık konusunda uzmanlaşmış şık bir restoran.",
    "address": "Poyrazköy Sahil, İstanbul",
    "latitude": 41.1802,
    "longitude": 29.1232,
    "priceRange": "$$",
    "imageUrl": "https://...",
    "userRating": 4.8,
    "influencerScore": 4.9,
    "reviewCount": 567,
    "phone": "+90 212 123 45 67",
    "website": "https://poyrazcapari.com",
    "openingHours": {
      "monday": "12:00-23:00",
      "tuesday": "12:00-23:00",
      "wednesday": "12:00-23:00",
      "thursday": "12:00-23:00",
      "friday": "12:00-24:00",
      "saturday": "12:00-24:00",
      "sunday": "12:00-23:00"
    },
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

---

### 3.3 Search Restaurants
Search restaurants by various criteria.

**Endpoint:** `GET /restaurants/search`
**Auth Required:** Yes
**Pagination:** Yes

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| query | string | No | Search in name, description, category |
| category | string | No | Filter by category |
| priceRange | string | No | Filter by price range ($, $$, $$$, $$$$) |
| minRating | number | No | Minimum user rating (0-5) |

**Example:** `GET /restaurants/search?query=balık&category=Balık&minRating=4`

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [ ... ],
    "meta": { ... }
  }
}
```

---

### 3.4 Get Nearby Restaurants
Get restaurants near a location.

**Endpoint:** `GET /restaurants/nearby`
**Auth Required:** Yes
**Pagination:** Yes

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| latitude | number | Yes | User's latitude |
| longitude | number | Yes | User's longitude |
| radius | number | No | Search radius in km (default: 5, max: 50) |

**Example:** `GET /restaurants/nearby?latitude=41.0082&longitude=28.9784&radius=5`

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": "uuid",
        "name": "Poyraz Çapari",
        "category": "Balık",
        "userRating": 4.8,
        "influencerScore": 4.9,
        "distance": 2.3,
        ...
      }
    ],
    "meta": { ... }
  }
}
```

---

### 3.5 Get Restaurant Reviews
Get reviews for a specific restaurant.

**Endpoint:** `GET /restaurants/:id/reviews`
**Auth Required:** Yes
**Pagination:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": "uuid",
        "rating": 5,
        "content": "Hakan'ın önerisiyle geldim, beklentilerimi aştı!",
        "likesCount": 23,
        "dislikesCount": 1,
        "isVerified": true,
        "createdAt": "2024-01-15T10:30:00Z",
        "user": {
          "id": "uuid",
          "username": "ayse_yilmaz",
          "name": "Ayşe Yılmaz",
          "avatarUrl": "https://..."
        },
        "influencer": {
          "id": "uuid",
          "username": "hakan_guntulun",
          "name": "Hakan Güntulun",
          "avatarUrl": "https://...",
          "credibilityScore": 4.9
        }
      }
    ],
    "meta": { ... }
  }
}
```

---

### 3.6 Get Recommending Influencers
Get influencers who have recommended this restaurant.

**Endpoint:** `GET /restaurants/:id/influencers`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "username": "hakan_guntulun",
      "name": "Hakan Güntulun",
      "avatarUrl": "https://...",
      "credibilityScore": 4.9,
      "isVerified": true
    },
    {
      "id": "uuid",
      "username": "mertcan_kizilkaya",
      "name": "Mertcan Kızılkaya",
      "avatarUrl": "https://...",
      "credibilityScore": 4.7,
      "isVerified": true
    }
  ]
}
```

---

## 4. Reviews

### 4.1 Create Review
Create a new review for a restaurant.

**Endpoint:** `POST /reviews`
**Auth Required:** Yes

**Request Body:**
```json
{
  "restaurantId": "uuid",
  "rating": 5,
  "content": "Harika bir deneyimdi! Yemekler çok lezzetliydi.",
  "influencerId": "uuid"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| restaurantId | uuid | Yes | Restaurant ID |
| rating | integer | Yes | 1-5 stars |
| content | string | No | Review text (max 2000 chars) |
| influencerId | uuid | No | ID of influencer whose recommendation led to visit |

**Response (201):**
```json
{
  "success": true,
  "data": {
    "review": {
      "id": "uuid",
      "rating": 5,
      "content": "Harika bir deneyimdi!",
      "likesCount": 0,
      "dislikesCount": 0,
      "isVerified": true,
      "createdAt": "2024-01-15T10:30:00Z",
      "user": { ... },
      "restaurant": { ... },
      "influencer": { ... }
    },
    "influencerImpact": {
      "influencerId": "uuid",
      "influencerName": "Hakan Güntulun",
      "influencerUsername": "hakan_guntulun",
      "oldScore": 4.85,
      "newScore": 4.90,
      "change": 0.05
    }
  }
}
```

**Influencer Score Impact:**
| Rating | Score Change |
|--------|-------------|
| 5 ⭐ | +0.05 |
| 4 ⭐ | +0.02 |
| 3 ⭐ | 0.00 |
| 2 ⭐ | -0.02 |
| 1 ⭐ | -0.05 |

---

### 4.2 Get Review
Get review by ID.

**Endpoint:** `GET /reviews/:id`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "rating": 5,
    "content": "Harika bir deneyimdi!",
    "likesCount": 23,
    "dislikesCount": 1,
    "isVerified": true,
    "createdAt": "2024-01-15T10:30:00Z",
    "updatedAt": "2024-01-15T10:30:00Z",
    "user": { ... },
    "restaurant": { ... },
    "influencer": { ... }
  }
}
```

---

### 4.3 Update Review
Update own review.

**Endpoint:** `PUT /reviews/:id`
**Auth Required:** Yes (own review only)

**Request Body:**
```json
{
  "rating": 4,
  "content": "Updated review content"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "review": { ... },
    "influencerImpact": {
      "influencerId": "uuid",
      "influencerName": "Hakan Güntulun",
      "influencerUsername": "hakan_guntulun",
      "oldScore": 4.90,
      "newScore": 4.87,
      "change": -0.03
    }
  }
}
```

---

### 4.4 Delete Review
Delete own review.

**Endpoint:** `DELETE /reviews/:id`
**Auth Required:** Yes (own review only)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Review deleted successfully"
  }
}
```

---

### 4.5 Like Review
Like a review.

**Endpoint:** `POST /reviews/:id/like`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Review liked"
  }
}
```

---

### 4.6 Dislike Review
Dislike a review.

**Endpoint:** `POST /reviews/:id/dislike`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Review disliked"
  }
}
```

---

## 5. Posts & Feed

### 5.1 Get Feed
Get personalized feed from followed users.

**Endpoint:** `GET /feed`
**Auth Required:** Yes
**Pagination:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": "uuid",
        "content": "Bu mekanı keşfettim! Muhteşem bir atmosfer 🌊",
        "imageUrl": "https://...",
        "likesCount": 45200,
        "commentsCount": 892,
        "sharesCount": 234,
        "createdAt": "2024-01-15T10:30:00Z",
        "isLiked": false,
        "user": {
          "id": "uuid",
          "username": "hakan_guntulun",
          "name": "Hakan Güntulun",
          "avatarUrl": "https://...",
          "credibilityScore": 4.9,
          "isInfluencer": true,
          "isVerified": true
        },
        "restaurant": {
          "id": "uuid",
          "name": "Poyraz Çapari",
          "category": "Balık",
          "imageUrl": "https://...",
          "userRating": 4.8,
          "influencerScore": 4.9
        }
      }
    ],
    "meta": { ... }
  }
}
```

---

### 5.2 Get Discover Feed
Get trending posts from influencers.

**Endpoint:** `GET /feed/discover`
**Auth Required:** Yes
**Pagination:** Yes

**Response:** Same structure as Get Feed

---

### 5.3 Create Post
Create a new post about a restaurant.

**Endpoint:** `POST /posts`
**Auth Required:** Yes

**Request Body:**
```json
{
  "restaurantId": "uuid",
  "content": "Bu mekanı keşfettim! Muhteşem bir atmosfer ve lezzetli yemekler.",
  "imageUrl": "https://..."
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| restaurantId | uuid | Yes | Restaurant ID |
| content | string | Yes | Post content (max 2000 chars) |
| imageUrl | string | No | Post image URL (max 500 chars) |

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "content": "Bu mekanı keşfettim!",
    "imageUrl": "https://...",
    "likesCount": 0,
    "commentsCount": 0,
    "sharesCount": 0,
    "createdAt": "2024-01-15T10:30:00Z",
    "user": { ... },
    "restaurant": { ... }
  }
}
```

---

### 5.4 Get Post
Get post by ID.

**Endpoint:** `GET /posts/:id`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "content": "Bu mekanı keşfettim!",
    "imageUrl": "https://...",
    "likesCount": 45200,
    "commentsCount": 892,
    "sharesCount": 234,
    "createdAt": "2024-01-15T10:30:00Z",
    "isLiked": true,
    "user": { ... },
    "restaurant": { ... }
  }
}
```

---

### 5.5 Delete Post
Delete own post.

**Endpoint:** `DELETE /posts/:id`
**Auth Required:** Yes (own post only)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Post deleted successfully"
  }
}
```

---

### 5.6 Like Post
Like a post.

**Endpoint:** `POST /posts/:id/like`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Post liked"
  }
}
```

---

### 5.7 Unlike Post
Remove like from a post.

**Endpoint:** `DELETE /posts/:id/like`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Like removed"
  }
}
```

---

### 5.8 Get Post Comments
Get comments on a post.

**Endpoint:** `GET /posts/:id/comments`
**Auth Required:** Yes
**Pagination:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": "uuid",
        "content": "Harika bir öneri, teşekkürler!",
        "createdAt": "2024-01-15T10:30:00Z",
        "user": {
          "id": "uuid",
          "username": "user1",
          "name": "User One",
          "avatarUrl": "https://..."
        }
      }
    ],
    "meta": { ... }
  }
}
```

---

### 5.9 Add Comment
Add a comment to a post.

**Endpoint:** `POST /posts/:id/comments`
**Auth Required:** Yes

**Request Body:**
```json
{
  "content": "Harika bir öneri, teşekkürler!"
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "content": "Harika bir öneri, teşekkürler!",
    "createdAt": "2024-01-15T10:30:00Z",
    "user": {
      "id": "uuid",
      "username": "ayse_yilmaz",
      "name": "Ayşe Yılmaz",
      "avatarUrl": "https://..."
    }
  }
}
```

---

## 6. Notifications

### 6.1 Get Notifications
Get user's notifications.

**Endpoint:** `GET /notifications`
**Auth Required:** Yes
**Pagination:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": "uuid",
        "type": "new_follower",
        "title": "Yeni Takipçi",
        "message": "Hakan Güntulun seni takip etmeye başladı",
        "data": {
          "followerId": "uuid"
        },
        "isRead": false,
        "createdAt": "2024-01-15T10:30:00Z"
      },
      {
        "id": "uuid",
        "type": "score_updated",
        "title": "Puan Güncellemesi",
        "message": "Güvenilirlik puanınız +0.05 arttı! Yeni puanınız: 4.90",
        "data": {
          "oldScore": 4.85,
          "newScore": 4.90,
          "change": 0.05
        },
        "isRead": true,
        "createdAt": "2024-01-15T09:00:00Z"
      }
    ],
    "unreadCount": 5,
    "meta": { ... }
  }
}
```

**Notification Types:**
| Type | Description |
|------|-------------|
| new_follower | Someone followed you |
| post_liked | Someone liked your post |
| post_commented | Someone commented on your post |
| review_added | New review on a restaurant you posted about |
| score_updated | Your credibility score changed |

---

### 6.2 Mark Notification as Read
Mark a single notification as read.

**Endpoint:** `PUT /notifications/:id/read`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Notification marked as read"
  }
}
```

---

### 6.3 Mark All as Read
Mark all notifications as read.

**Endpoint:** `PUT /notifications/read-all`
**Auth Required:** Yes

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "All notifications marked as read"
  }
}
```

---

## 7. WebSocket Events

### Connection
Connect to WebSocket server with authentication:

```swift
// iOS Example
let socket = SocketManager(
    socketURL: URL(string: "wss://api.spotera.com")!,
    config: [
        .connectParams(["token": accessToken])
    ]
)
```

### Client → Server Events

#### Join Feed
Subscribe to feed updates.
```json
{
  "event": "join_feed"
}
```

#### Join Restaurant
Subscribe to restaurant updates.
```json
{
  "event": "join_restaurant",
  "data": {
    "restaurantId": "uuid"
  }
}
```

#### Leave Restaurant
Unsubscribe from restaurant updates.
```json
{
  "event": "leave_restaurant",
  "data": {
    "restaurantId": "uuid"
  }
}
```

### Server → Client Events

#### new_post
New post from followed user.
```json
{
  "event": "new_post",
  "data": {
    "id": "uuid",
    "content": "...",
    "user": { ... },
    "restaurant": { ... }
  }
}
```

#### new_follower
Someone started following you.
```json
{
  "event": "new_follower",
  "data": {
    "id": "uuid",
    "username": "user1",
    "name": "User One",
    "avatarUrl": "https://..."
  }
}
```

#### post_liked
Your post was liked.
```json
{
  "event": "post_liked",
  "data": {
    "postId": "uuid",
    "likesCount": 45201
  }
}
```

#### review_added
New review added to a restaurant you're watching.
```json
{
  "event": "review_added",
  "data": {
    "id": "uuid",
    "rating": 5,
    "content": "...",
    "user": { ... }
  }
}
```

#### score_updated
Your credibility score changed.
```json
{
  "event": "score_updated",
  "data": {
    "oldScore": 4.85,
    "newScore": 4.90,
    "change": 0.05
  }
}
```

---

## 8. Data Models

### User
```swift
struct User: Codable {
    let id: String
    let username: String
    let name: String
    let email: String
    let bio: String?
    let avatarUrl: String?
    let credibilityScore: Double
    let totalReviews: Int
    let verifiedReviews: Int
    let followersCount: Int
    let followingCount: Int
    let isInfluencer: Bool
    let isVerified: Bool
    let createdAt: Date
}
```

### Restaurant
```swift
struct Restaurant: Codable {
    let id: String
    let name: String
    let category: String
    let description: String?
    let address: String?
    let latitude: Double?
    let longitude: Double?
    let priceRange: String?
    let imageUrl: String?
    let userRating: Double
    let influencerScore: Double
    let reviewCount: Int
    let phone: String?
    let website: String?
    let openingHours: [String: String]?
    let createdAt: Date
}
```

### Review
```swift
struct Review: Codable {
    let id: String
    let rating: Int
    let content: String?
    let likesCount: Int
    let dislikesCount: Int
    let isVerified: Bool
    let createdAt: Date
    let user: UserSummary?
    let restaurant: RestaurantSummary?
    let influencer: UserSummary?
}
```

### Post
```swift
struct Post: Codable {
    let id: String
    let content: String
    let imageUrl: String?
    let likesCount: Int
    let commentsCount: Int
    let sharesCount: Int
    let createdAt: Date
    let isLiked: Bool?
    let user: UserSummary?
    let restaurant: RestaurantSummary?
}
```

### ScoreChangeResult
```swift
struct ScoreChangeResult: Codable {
    let influencerId: String
    let influencerName: String
    let influencerUsername: String
    let oldScore: Double
    let newScore: Double
    let change: Double
}
```

---

## 9. Error Handling

### HTTP Status Codes
| Code | Description |
|------|-------------|
| 200 | Success |
| 201 | Created |
| 400 | Bad Request - Invalid input |
| 401 | Unauthorized - Invalid or missing token |
| 403 | Forbidden - Not allowed |
| 404 | Not Found |
| 409 | Conflict - Duplicate resource |
| 429 | Too Many Requests - Rate limited |
| 500 | Internal Server Error |

### Error Response Format
```json
{
  "success": false,
  "message": "Error description",
  "statusCode": 400
}
```

### Common Errors

#### Validation Error (400)
```json
{
  "success": false,
  "message": [
    "email must be an email",
    "password must be longer than or equal to 8 characters"
  ],
  "statusCode": 400
}
```

#### Unauthorized (401)
```json
{
  "success": false,
  "message": "Unauthorized",
  "statusCode": 401
}
```

#### Forbidden (403)
```json
{
  "success": false,
  "message": "You can only update your own profile",
  "statusCode": 403
}
```

#### Not Found (404)
```json
{
  "success": false,
  "message": "Restaurant not found",
  "statusCode": 404
}
```

#### Conflict (409)
```json
{
  "success": false,
  "message": "You have already reviewed this restaurant",
  "statusCode": 409
}
```

---

## Rate Limiting

| Endpoint Type | Limit |
|--------------|-------|
| Auth endpoints | 5 requests/minute |
| API endpoints | 100 requests/minute |
| File upload | 10 requests/minute |

When rate limited, you'll receive:
```json
{
  "success": false,
  "message": "Too many requests",
  "statusCode": 429
}
```

---

## Test Credentials

| Role | Email | Password |
|------|-------|----------|
| Regular User | ayse@email.com | 123456 |
| Influencer | hakan@email.com | Password123 |
| Influencer | mertcan@email.com | Password123 |
| Influencer | zeynep@email.com | Password123 |

---

## Swagger Documentation

Interactive API documentation is available at:
```
https://api.spotera.com/api/docs
```

---

**Questions?** Contact the backend team.
