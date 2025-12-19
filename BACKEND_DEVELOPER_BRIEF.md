# Backend Developer Brief
## Stitch Design - Influencer & Foodie App

**Proje Özeti:** Influencer tavsiyeleri üzerinden restoran keşfi yapılan, güvenilirlik puanı sistemiyle desteklenen sosyal yemek uygulaması.

---

## 1. Veritabanı Şeması

### 1.1 Users (Kullanıcılar)
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    bio TEXT,
    avatar_url VARCHAR(500),
    credibility_score DECIMAL(3,2) DEFAULT 0.00,  -- 0.00 - 5.00
    total_reviews INT DEFAULT 0,
    verified_reviews INT DEFAULT 0,
    followers_count INT DEFAULT 0,
    following_count INT DEFAULT 0,
    is_influencer BOOLEAN DEFAULT FALSE,
    is_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

### 1.2 Restaurants (Restoranlar)
```sql
CREATE TABLE restaurants (
    id UUID PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    category VARCHAR(100) NOT NULL,
    description TEXT,
    address TEXT,
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    price_range VARCHAR(10),  -- $, $$, $$$, $$$$
    image_url VARCHAR(500),
    user_rating DECIMAL(2,1) DEFAULT 0.0,  -- 0.0 - 5.0
    influencer_score DECIMAL(2,1) DEFAULT 0.0,  -- 0.0 - 5.0
    review_count INT DEFAULT 0,
    phone VARCHAR(20),
    website VARCHAR(255),
    opening_hours JSONB,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

### 1.3 Reviews (Yorumlar)
```sql
CREATE TABLE reviews (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    restaurant_id UUID REFERENCES restaurants(id),
    influencer_id UUID REFERENCES users(id) NULL,  -- Kimin tavsiyesiyle geldi
    rating INT CHECK (rating >= 1 AND rating <= 5),
    content TEXT,
    likes_count INT DEFAULT 0,
    dislikes_count INT DEFAULT 0,
    is_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

### 1.4 Posts (Gönderiler)
```sql
CREATE TABLE posts (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    restaurant_id UUID REFERENCES restaurants(id),
    content TEXT NOT NULL,
    image_url VARCHAR(500),
    likes_count INT DEFAULT 0,
    comments_count INT DEFAULT 0,
    shares_count INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

### 1.5 Follows (Takip İlişkileri)
```sql
CREATE TABLE follows (
    id UUID PRIMARY KEY,
    follower_id UUID REFERENCES users(id),
    following_id UUID REFERENCES users(id),
    created_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(follower_id, following_id)
);
```

### 1.6 Likes (Beğeniler)
```sql
CREATE TABLE post_likes (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    post_id UUID REFERENCES posts(id),
    created_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(user_id, post_id)
);
```

### 1.7 Influencer Score History (Puan Geçmişi)
```sql
CREATE TABLE influencer_score_history (
    id UUID PRIMARY KEY,
    influencer_id UUID REFERENCES users(id),
    review_id UUID REFERENCES reviews(id),
    old_score DECIMAL(3,2),
    new_score DECIMAL(3,2),
    score_change DECIMAL(3,2),
    created_at TIMESTAMP DEFAULT NOW()
);
```

---

## 2. API Endpoints

### 2.1 Authentication
```
POST   /api/v1/auth/register          - Yeni kullanıcı kaydı
POST   /api/v1/auth/login             - Giriş yap
POST   /api/v1/auth/logout            - Çıkış yap
POST   /api/v1/auth/forgot-password   - Şifre sıfırlama emaili gönder
POST   /api/v1/auth/reset-password    - Şifreyi sıfırla
POST   /api/v1/auth/refresh-token     - Token yenile
```

### 2.2 Users
```
GET    /api/v1/users/:id              - Kullanıcı profili getir
PUT    /api/v1/users/:id              - Kullanıcı güncelle
GET    /api/v1/users/:id/followers    - Takipçileri getir
GET    /api/v1/users/:id/following    - Takip edilenleri getir
POST   /api/v1/users/:id/follow       - Takip et
DELETE /api/v1/users/:id/follow       - Takipten çık
GET    /api/v1/users/:id/posts        - Kullanıcının gönderilerini getir
GET    /api/v1/users/:id/reviews      - Kullanıcının yorumlarını getir
```

### 2.3 Restaurants
```
GET    /api/v1/restaurants            - Restoranları listele (pagination, filtering)
GET    /api/v1/restaurants/:id        - Restoran detayı
GET    /api/v1/restaurants/:id/reviews          - Restoran yorumları
GET    /api/v1/restaurants/:id/influencers      - Öneren influencer'lar
GET    /api/v1/restaurants/search     - Restoran ara (query, category, location)
GET    /api/v1/restaurants/nearby     - Yakındaki restoranlar (lat, lng, radius)
```

### 2.4 Reviews
```
POST   /api/v1/reviews                - Yorum ekle
GET    /api/v1/reviews/:id            - Yorum detayı
PUT    /api/v1/reviews/:id            - Yorum güncelle
DELETE /api/v1/reviews/:id            - Yorum sil
POST   /api/v1/reviews/:id/like       - Yorum beğen
POST   /api/v1/reviews/:id/dislike    - Yorum beğenme
```

### 2.5 Posts (Feed)
```
GET    /api/v1/feed                   - Ana feed (takip edilenlerden)
GET    /api/v1/feed/discover          - Keşfet feed'i
POST   /api/v1/posts                  - Gönderi oluştur
GET    /api/v1/posts/:id              - Gönderi detayı
DELETE /api/v1/posts/:id              - Gönderi sil
POST   /api/v1/posts/:id/like         - Beğen
DELETE /api/v1/posts/:id/like         - Beğeniyi kaldır
GET    /api/v1/posts/:id/comments     - Yorumları getir
POST   /api/v1/posts/:id/comments     - Yorum ekle
```

### 2.6 Notifications
```
GET    /api/v1/notifications          - Bildirimleri getir
PUT    /api/v1/notifications/:id/read - Okundu işaretle
PUT    /api/v1/notifications/read-all - Tümünü okundu işaretle
```

---

## 3. Kritik İş Mantığı

### 3.1 Influencer Güvenilirlik Puanı Hesaplama

```python
def calculate_score_change(rating: int) -> float:
    """
    Kullanıcının verdiği puana göre influencer'ın güvenilirlik puanı değişimi
    """
    score_map = {
        5: +0.05,   # Mükemmel - tavsiye tuttu
        4: +0.02,   # İyi - tavsiye tuttu
        3: 0.00,    # Nötr - etkilemez
        2: -0.02,   # Kötü - tavsiye tutmadı
        1: -0.05    # Çok kötü - tavsiye tutmadı
    }
    return score_map.get(rating, 0.00)

def update_influencer_score(influencer_id: UUID, rating: int):
    """
    Influencer puanını güncelle
    """
    influencer = get_user(influencer_id)
    score_change = calculate_score_change(rating)

    new_score = influencer.credibility_score + score_change
    new_score = max(0.00, min(5.00, new_score))  # 0-5 arasında tut
    new_score = round(new_score, 2)

    # Geçmişi kaydet
    save_score_history(influencer_id, influencer.credibility_score, new_score, score_change)

    # Güncelle
    influencer.credibility_score = new_score
    influencer.verified_reviews += 1
    influencer.total_reviews += 1

    return {
        "old_score": influencer.credibility_score,
        "new_score": new_score,
        "change": score_change
    }
```

### 3.2 Restoran Puanı Hesaplama

```python
def update_restaurant_rating(restaurant_id: UUID):
    """
    Yeni yorum sonrası restoran puanını güncelle
    """
    reviews = get_reviews_by_restaurant(restaurant_id)

    if not reviews:
        return

    # Kullanıcı puanı - tüm yorumların ortalaması
    user_rating = sum(r.rating for r in reviews) / len(reviews)

    # Influencer puanı - sadece influencer tavsiyesiyle gelen yorumlar
    influencer_reviews = [r for r in reviews if r.influencer_id is not None]
    if influencer_reviews:
        influencer_score = sum(r.rating for r in influencer_reviews) / len(influencer_reviews)
    else:
        influencer_score = user_rating

    restaurant.user_rating = round(user_rating, 1)
    restaurant.influencer_score = round(influencer_score, 1)
    restaurant.review_count = len(reviews)
```

### 3.3 Feed Algoritması

```python
def get_feed(user_id: UUID, page: int, limit: int = 20):
    """
    Kullanıcının feed'ini getir
    - Takip edilen influencer'ların gönderileri
    - Zaman bazlı sıralama
    - Engagement bazlı boost
    """
    following_ids = get_following_ids(user_id)

    posts = Post.query.filter(
        Post.user_id.in_(following_ids)
    ).order_by(
        Post.created_at.desc()
    ).paginate(page=page, per_page=limit)

    return posts
```

---

## 4. Real-time Özellikler (WebSocket)

### 4.1 Events
```
- new_post          : Yeni gönderi bildirimi
- new_follower      : Yeni takipçi bildirimi
- post_liked        : Gönderi beğenildi
- review_added      : Yeni yorum eklendi
- score_updated     : Influencer puanı güncellendi
```

### 4.2 WebSocket Channels
```
- user:{user_id}              : Kullanıcıya özel bildirimler
- feed:{user_id}              : Feed güncellemeleri
- restaurant:{restaurant_id}  : Restoran güncellemeleri
```

---

## 5. Güvenlik Gereksinimleri

### 5.1 Authentication
- JWT Token based authentication
- Access Token: 15 dakika
- Refresh Token: 7 gün
- Password hashing: bcrypt (min 12 rounds)

### 5.2 Rate Limiting
```
- Auth endpoints: 5 req/dakika
- API endpoints: 100 req/dakika
- File upload: 10 req/dakika
```

### 5.3 Validasyonlar
- Email format kontrolü
- Password minimum 8 karakter, 1 büyük harf, 1 rakam
- Username: 3-30 karakter, alfanümerik + underscore
- Review content: max 2000 karakter
- Bio: max 500 karakter

---

## 6. Dosya Yükleme

### 6.1 Avatar
- Max boyut: 5MB
- Formatlar: JPG, PNG, WebP
- Resize: 200x200, 400x400, 800x800
- Storage: AWS S3 / Cloudinary

### 6.2 Post Görselleri
- Max boyut: 10MB
- Formatlar: JPG, PNG, WebP
- Resize: 600x600, 1200x1200
- Storage: AWS S3 / Cloudinary

---

## 7. Önbellek Stratejisi (Redis)

```
- user:{id}                     : 5 dakika
- restaurant:{id}               : 10 dakika
- feed:{user_id}:{page}         : 2 dakika
- search:{query}                : 5 dakika
- influencer_leaderboard        : 1 saat
```

---

## 8. Önerilen Tech Stack

- **Framework:** Node.js (NestJS) veya Python (FastAPI)
- **Database:** PostgreSQL
- **Cache:** Redis
- **Search:** Elasticsearch (restoran arama için)
- **File Storage:** AWS S3 + CloudFront CDN
- **Real-time:** Socket.io veya WebSocket
- **Queue:** Bull (Node) veya Celery (Python)
- **API Docs:** Swagger/OpenAPI

---

## 9. MVP için Öncelik Sırası

### Faz 1 (2-3 hafta)
1. User authentication (register, login, forgot password)
2. User profile CRUD
3. Restaurant CRUD + listing
4. Basic search

### Faz 2 (2-3 hafta)
1. Review system + influencer attribution
2. Influencer scoring algorithm
3. Follow/unfollow system
4. Basic feed

### Faz 3 (2-3 hafta)
1. Posts/Feed system
2. Like/comment functionality
3. Real-time notifications
4. Search improvements

### Faz 4 (1-2 hafta)
1. Performance optimization
2. Caching implementation
3. Analytics/logging
4. Security audit

---

## 10. Örnek API Response'ları

### Login Response
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "username": "hakan_guntulun",
      "name": "Hakan Güntulun",
      "email": "hakan@email.com",
      "avatar_url": "https://...",
      "credibility_score": 4.9,
      "followers_count": 2100000,
      "is_influencer": true
    },
    "tokens": {
      "access_token": "eyJ...",
      "refresh_token": "eyJ...",
      "expires_in": 900
    }
  }
}
```

### Review Create Request
```json
{
  "restaurant_id": "uuid",
  "rating": 5,
  "content": "Harika bir deneyimdi!",
  "influencer_id": "uuid"  // nullable - kimin tavsiyesiyle geldi
}
```

### Review Create Response
```json
{
  "success": true,
  "data": {
    "review": {
      "id": "uuid",
      "rating": 5,
      "content": "Harika bir deneyimdi!",
      "created_at": "2024-01-15T10:30:00Z"
    },
    "influencer_impact": {
      "influencer_id": "uuid",
      "influencer_name": "Hakan Güntulun",
      "old_score": 4.85,
      "new_score": 4.90,
      "change": 0.05
    }
  }
}
```

---

**Hazırlayan:** Claude AI
**Tarih:** 2024
**Versiyon:** 1.0
