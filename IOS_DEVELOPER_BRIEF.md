# iOS Native Developer Brief
## Stitch Design - Influencer & Foodie App

**Proje Özeti:** Influencer tavsiyeleri üzerinden restoran keşfi yapılan, güvenilirlik puanı sistemiyle desteklenen sosyal yemek uygulaması.

**Platform:** iOS 15.0+
**Dil:** Swift 5.9+
**UI Framework:** SwiftUI (tercihen) veya UIKit

---

## 1. Tasarım Sistemi

### 1.1 Renk Paleti

```swift
extension Color {
    static let appPrimary = Color(hex: "#EC6D13")        // Ana turuncu
    static let appBackground = Color(hex: "#181411")     // Koyu arka plan
    static let appSurface = Color(hex: "#27201C")        // Kart arka planı
    static let appSurfaceInput = Color(hex: "#392F28")   // Input arka planı
    static let appBorder = Color(hex: "#54453B")         // Border rengi
    static let appTextMuted = Color(hex: "#B9A89D")      // İkincil metin
    static let appTextPrimary = Color.white              // Ana metin

    // Feedback renkleri
    static let appSuccess = Color(hex: "#22C55E")        // Yeşil - başarı
    static let appError = Color(hex: "#EF4444")          // Kırmızı - hata
    static let appWarning = Color(hex: "#F59E0B")        // Sarı - uyarı
}
```

### 1.2 Tipografi

```swift
// Font: Plus Jakarta Sans (Google Fonts'tan indirilmeli)
// Fallback: System font

struct AppTypography {
    static let largeTitle = Font.custom("PlusJakartaSans-Bold", size: 28)
    static let title = Font.custom("PlusJakartaSans-Bold", size: 22)
    static let headline = Font.custom("PlusJakartaSans-Bold", size: 18)
    static let body = Font.custom("PlusJakartaSans-Regular", size: 16)
    static let subheadline = Font.custom("PlusJakartaSans-Medium", size: 14)
    static let caption = Font.custom("PlusJakartaSans-Regular", size: 13)
    static let small = Font.custom("PlusJakartaSans-Regular", size: 12)
}
```

### 1.3 Spacing & Sizing

```swift
struct AppSpacing {
    static let xs: CGFloat = 4
    static let sm: CGFloat = 8
    static let md: CGFloat = 12
    static let lg: CGFloat = 16
    static let xl: CGFloat = 24
    static let xxl: CGFloat = 32
}

struct AppCornerRadius {
    static let small: CGFloat = 8
    static let medium: CGFloat = 12
    static let large: CGFloat = 16
    static let extraLarge: CGFloat = 24
    static let full: CGFloat = 9999
}
```

---

## 2. Ekran Listesi & Navigasyon

### 2.1 Authentication Flow
```
┌─────────────────────────────────────────────────────────────┐
│  LandingScreen                                              │
│  ├── LoginScreen                                            │
│  │   └── ForgotPasswordScreen                               │
│  └── SignUpScreen                                           │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 Main Tab Bar
```
┌─────────────────────────────────────────────────────────────┐
│  MainTabView                                                │
│  ├── Tab 1: FeedScreen (Home icon)                          │
│  │   └── RestaurantDetailScreen                             │
│  │   └── ProfileScreen (influencer profili)                 │
│  ├── Tab 2: SearchScreen (Search icon)                      │
│  │   └── RestaurantDetailScreen                             │
│  ├── Tab 3: CreatePostScreen (Plus icon)                    │
│  ├── Tab 4: NotificationsScreen (Bell icon)                 │
│  └── Tab 5: ProfileScreen (User icon - own profile)         │
│       └── EditProfileScreen                                 │
└─────────────────────────────────────────────────────────────┘
```

### 2.3 Restaurant Flow
```
┌─────────────────────────────────────────────────────────────┐
│  RestaurantDetailScreen                                     │
│  ├── WriteReviewScreen                                      │
│  │   └── InfluencerSelectModal (bottom sheet)               │
│  │       └── ScoreUpdateResultView (animasyonlu sonuç)      │
│  └── AllReviewsScreen                                       │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. Ekran Detayları

### 3.1 Landing Screen
**Dosya:** `LandingScreen.swift`

```
┌─────────────────────────────────────┐
│                                     │
│         [Hero Image]                │
│         (aspect ratio: 16:9)        │
│                                     │
├─────────────────────────────────────┤
│                                     │
│   "Güvenilir önerilerle en iyi      │
│    mekanları keşfet"                │
│                                     │
│   Güvendiğiniz influencer'ların     │
│   önerdiği restoranları bulun.      │
│                                     │
│   ┌─────────────────────────────┐   │
│   │        Kayıt Ol             │   │ ← Primary Button (turuncu)
│   └─────────────────────────────┘   │
│   ┌─────────────────────────────┐   │
│   │        Giriş Yap            │   │ ← Secondary Button (koyu)
│   └─────────────────────────────┘   │
│                                     │
│   Kullanım Şartları ve Gizlilik     │
│   Politikasını kabul etmiş...       │
│                                     │
└─────────────────────────────────────┘
```

### 3.2 Login Screen
**Dosya:** `LoginScreen.swift`

```
┌─────────────────────────────────────┐
│                                     │
│         [App Logo]                  │
│         (64x64, rounded)            │
│                                     │
│      "Tekrar Hoş Geldin"            │
│   Influencer'lar ve mekanlarla      │
│        bağlantı kur.                │
│                                     │
│   ┌─────────────────────────────┐   │
│   │  E-posta                    │   │ ← TextField
│   └─────────────────────────────┘   │
│   ┌─────────────────────────────┐   │
│   │  Şifre                      │   │ ← SecureField
│   └─────────────────────────────┘   │
│                                     │
│              Şifremi Unuttum? →     │
│                                     │
│   ┌─────────────────────────────┐   │
│   │        Giriş Yap            │   │
│   └─────────────────────────────┘   │
│                                     │
│   Hesabın yok mu? Kayıt Ol          │
│                                     │
└─────────────────────────────────────┘
```

### 3.3 Feed Screen
**Dosya:** `FeedScreen.swift`

```
┌─────────────────────────────────────┐
│  ←  [Logo]     Feed      [Logout]   │ ← Navigation Bar
├─────────────────────────────────────┤
│                                     │
│  ○ ○ ○ ○ ○ ○ ○                      │ ← Stories (horizontal scroll)
│  Ayşe Mert Zey Ali Eli Bur Hak      │
│                                     │
├─────────────────────────────────────┤
│  ┌─────────────────────────────┐    │
│  │    ★ Yeni Öneri!            │    │ ← Animated badge (pulse)
│  └─────────────────────────────┘    │
│  ┌─────────────────────────────┐    │
│  │ ○ Hakan Güntulun    ★4.9   │    │ ← Influencer info
│  │   @hakan_guntulun · Şimdi   │    │
│  │                             │    │
│  │ ┌─────────────────────────┐ │    │
│  │ │                         │ │    │
│  │ │    [Restaurant Image]   │ │    │ ← Post image
│  │ │                         │ │    │
│  │ ├─────────────────────────┤ │    │
│  │ │ Poyraz Çapari    ★4.9   │ │    │
│  │ │ Balık · Poyrazköy       │ │    │
│  │ └─────────────────────────┘ │    │
│  │                             │    │
│  │ "Bu mekanı keşfettim..."    │    │ ← Post content
│  │                             │    │
│  │  ♡ 0    💬 0    ↗ 0        │    │ ← Action buttons
│  └─────────────────────────────┘    │
│                                     │
│  [Diğer postlar...]                 │
│                                     │
├─────────────────────────────────────┤
│   🏠    🔍    ➕    🔔    👤       │ ← Tab Bar
└─────────────────────────────────────┘
```

**Önemli Animasyonlar:**
1. **Yeni post animasyonu:** Yukarıdan aşağı kayma (0.8s ease-out)
2. **Shimmer loading:** Gradient animasyonu (1.5s infinite)
3. **Pulse glow:** Badge etrafında nabız efekti (2s infinite)

### 3.4 Restaurant Detail Screen
**Dosya:** `RestaurantDetailScreen.swift`

```
┌─────────────────────────────────────┐
│  ←        Poyraz Çapari             │
├─────────────────────────────────────┤
│                                     │
│           [Restaurant              │
│             Image]                  │
│            (144x144)                │
│                                     │
│        "Poyraz Çapari"              │
│     Balık Restoranı · $$            │
│     Poyrazköy Sahil, İstanbul       │
│                                     │
│  ┌───────────────┬───────────────┐  │
│  │ Kullanıcı     │ Influencer    │  │
│  │   Puanı       │   Puanı       │  │
│  │    4.8 ★      │    4.9 ★      │  │ ← Rating cards
│  │  567 yorum    │ Güvenilir     │  │
│  │               │   öneriler    │  │
│  └───────────────┴───────────────┘  │
│                                     │
│  Hakkında                           │
│  Poyraz Çapari Poyrazköy Sahilde... │
│                                     │
│  Öneren Influencer'lar              │
│  ┌────┐ ┌────┐ ┌────┐ ┌────┐       │
│  │ ○  │ │ ○  │ │ ○  │ │ ○  │       │ ← Horizontal scroll
│  │★4.9│ │★4.7│ │★4.8│ │★4.6│       │
│  │Hak │ │Mer │ │Zey │ │Ali │       │
│  └────┘ └────┘ └────┘ └────┘       │
│                                     │
│  Yorumlar (4)                       │
│  ┌─────────────────────────────┐    │
│  │ ○ Ayşe Yılmaz               │    │
│  │   2 hafta önce              │    │
│  │   ★★★★★                      │    │
│  │   "Mikla beklentilerimi..." │    │
│  └─────────────────────────────┘    │
│                                     │
│  ┌─────────────┬─────────────┐      │
│  │  Ziyaret Et │  Yorum Yaz  │      │ ← Action buttons
│  └─────────────┴─────────────┘      │
│                                     │
├─────────────────────────────────────┤
│   🏠    🔍    ➕    🔔    👤       │
└─────────────────────────────────────┘
```

### 3.5 Write Review Screen
**Dosya:** `WriteReviewScreen.swift`

```
┌─────────────────────────────────────┐
│  ✕           Yorum Yaz              │
├─────────────────────────────────────┤
│                                     │
│  Poyraz Çapari hakkında             │
│  deneyimin nasıldı?                 │
│                                     │
│  Puanla                             │
│  ┌───┐ ┌───┐ ┌───┐ ┌───┐ ┌───┐    │
│  │ ★ │ │ ★ │ │ ★ │ │ ☆ │ │ ☆ │    │ ← Star rating
│  └───┘ └───┘ └───┘ └───┘ └───┘    │
│                                     │
│  ⭐ Mükemmel! Bu tavsiyeyi yapan    │ ← Dynamic feedback text
│  influencer puan kazanacak.         │
│                                     │
│  ┌─────────────────────────────┐    │
│  │                             │    │
│  │  Düşüncelerini paylaş...    │    │ ← TextEditor
│  │                             │    │
│  │                             │    │
│  └─────────────────────────────┘    │
│                                     │
│  ┌─────────────────────────────┐    │
│  │        Devam Et             │    │
│  └─────────────────────────────┘    │
│                                     │
└─────────────────────────────────────┘
```

### 3.6 Influencer Select Modal (Bottom Sheet)
**Dosya:** `InfluencerSelectModal.swift`

**State 1: Seçim Ekranı**
```
┌─────────────────────────────────────┐
│                                     │
│  ═══════════════════════════════    │ ← Drag indicator
│                                     │
│  Kimin Tavsiyesiyle Geldiniz?  Atla │
│                                     │
│  Bu restoranı size öneren           │
│  influencer'ı seçin...              │
│                                     │
│  ┌─────────────────────────────┐    │
│  │ ○ Hakan Güntulun     ★4.9  │    │ ← Selected state (orange border)
│  │   @hakan_guntulun     ✓    │    │
│  └─────────────────────────────┘    │
│  ┌─────────────────────────────┐    │
│  │ ○ Mertcan Kızılkaya  ★4.7  │    │
│  │   @mertcan_kizilkaya       │    │
│  └─────────────────────────────┘    │
│  ┌─────────────────────────────┐    │
│  │ ○ Zeynep Demir       ★4.9  │    │
│  │   @zeynep_gurme            │    │
│  └─────────────────────────────┘    │
│  ...                                │
│                                     │
│  ┌─────────────────────────────┐    │
│  │     Onayla ve Gönder        │    │
│  └─────────────────────────────┘    │
│                                     │
└─────────────────────────────────────┘
```

**State 2: Sonuç Animasyonu**
```
┌─────────────────────────────────────┐
│                                     │
│                                     │
│            ┌─────┐                  │
│            │  ○  │ ← Avatar (96x96) │
│            │     │   ring: primary  │
│            └─────┘                  │
│               📈 ← Trend icon       │
│                    (green/red)      │
│                                     │
│         Hakan Güntulun              │
│        @hakan_guntulun              │
│                                     │
│    Eski Puan  →  Yeni Puan          │
│      4.85          4.90             │ ← Animated score
│                                     │
│            +0.05                    │ ← Float up animation
│                                     │
│    "Tavsiyesi doğrulandı!           │
│     Güvenilirliği arttı."           │
│                                     │
│         ○ Yönlendiriliyorsunuz...   │ ← Loading spinner
│                                     │
└─────────────────────────────────────┘
```

### 3.7 Profile Screen
**Dosya:** `ProfileScreen.swift`

```
┌─────────────────────────────────────┐
│  ←       Hakan Güntulun             │
├─────────────────────────────────────┤
│                                     │
│              ┌─────┐                │
│              │  ○  │                │ ← Avatar with ring
│              │     │                │
│              └─────┘                │
│              ★ 4.9                  │ ← Credibility badge
│                                     │
│          Hakan Güntulun             │
│         @hakan_guntulun             │
│   Lezzet Avcısı | İstanbul'un...    │
│                                     │
│    2.1M      89       312           │
│   Takipçi  Takip    Yorum           │
│                                     │
│  ┌─────────────────────────────┐    │
│  │ Güvenilirlik Puanı          │    │
│  │ ★★★★★              4.9/5   │    │
│  │                             │    │
│  │ Doğrulanmış Yorum           │    │
│  │ ████████████░░  305 / 312   │    │ ← Progress bar
│  └─────────────────────────────┘    │
│                                     │
│   ┌─────────────────────────────┐   │
│   │        Takip Et             │   │ ← Follow button
│   └─────────────────────────────┘   │
│                                     │
│  ┌──────────┬──────────┐            │
│  │ Gönderiler│ Önerilen │            │ ← Segmented control
│  └──────────┴──────────┘            │
│                                     │
│  ┌────┐ ┌────┐ ┌────┐              │
│  │    │ │    │ │    │              │ ← Grid posts (3 column)
│  └────┘ └────┘ └────┘              │
│                                     │
├─────────────────────────────────────┤
│   🏠    🔍    ➕    🔔    👤       │
└─────────────────────────────────────┘
```

### 3.8 Search Screen
**Dosya:** `SearchScreen.swift`

```
┌─────────────────────────────────────┐
│  ┌─────────────────────────────┐    │
│  │ 🔍 Restoran veya kategori...│    │ ← Search bar
│  └─────────────────────────────┘    │
├─────────────────────────────────────┤
│                                     │
│  Restoranlar                        │
│                                     │
│  ┌─────────────────────────────┐    │
│  │ ┌────┐  Poyraz Çapari       │    │
│  │ │    │  Balık · $$          │    │
│  │ │    │  ★ 4.8  Inf: 4.9     │    │
│  │ └────┘                      │    │
│  └─────────────────────────────┘    │
│  ┌─────────────────────────────┐    │
│  │ ┌────┐  Antep Sofrası       │    │
│  │ │    │  Türk Mutfağı · $$   │    │
│  │ │    │  ★ 4.7  Inf: 4.6     │    │
│  │ └────┘                      │    │
│  └─────────────────────────────┘    │
│  ...                                │
│                                     │
├─────────────────────────────────────┤
│   🏠    🔍    ➕    🔔    👤       │
└─────────────────────────────────────┘
```

---

## 4. Data Models

```swift
// MARK: - User Model
struct User: Codable, Identifiable {
    let id: UUID
    let username: String
    let name: String
    let email: String
    let bio: String?
    let avatarUrl: String?
    var credibilityScore: Double
    var totalReviews: Int
    var verifiedReviews: Int
    var followersCount: Int
    var followingCount: Int
    let isInfluencer: Bool
    let isVerified: Bool
    let createdAt: Date
}

// MARK: - Restaurant Model
struct Restaurant: Codable, Identifiable {
    let id: UUID
    let name: String
    let category: String
    let description: String?
    let address: String?
    let latitude: Double?
    let longitude: Double?
    let priceRange: String
    let imageUrl: String?
    var userRating: Double
    var influencerScore: Double
    var reviewCount: Int
    let phone: String?
    let website: String?
}

// MARK: - Review Model
struct Review: Codable, Identifiable {
    let id: UUID
    let userId: UUID
    let restaurantId: UUID
    let influencerId: UUID?
    let rating: Int
    let content: String?
    var likesCount: Int
    var dislikesCount: Int
    let isVerified: Bool
    let createdAt: Date

    // Nested
    var user: User?
}

// MARK: - Post Model
struct Post: Codable, Identifiable {
    let id: UUID
    let userId: UUID
    let restaurantId: UUID
    let content: String
    let imageUrl: String?
    var likesCount: Int
    var commentsCount: Int
    var sharesCount: Int
    let createdAt: Date

    // Nested
    var user: User?
    var restaurant: Restaurant?
}

// MARK: - Score Change Result
struct ScoreChangeResult: Codable {
    let influencerId: UUID
    let influencerName: String
    let oldScore: Double
    let newScore: Double
    let change: Double
}
```

---

## 5. Animasyonlar

### 5.1 Yeni Post Animasyonu (Feed)
```swift
struct NewPostAnimation: ViewModifier {
    @State private var offset: CGFloat = -200
    @State private var opacity: Double = 0

    func body(content: Content) -> some View {
        content
            .offset(y: offset)
            .opacity(opacity)
            .onAppear {
                withAnimation(.easeOut(duration: 0.8)) {
                    offset = 0
                    opacity = 1
                }
            }
    }
}
```

### 5.2 Shimmer Loading
```swift
struct ShimmerEffect: ViewModifier {
    @State private var phase: CGFloat = 0

    func body(content: Content) -> some View {
        content
            .overlay(
                LinearGradient(
                    colors: [
                        Color.appSurface,
                        Color.appSurfaceInput,
                        Color.appSurface
                    ],
                    startPoint: .leading,
                    endPoint: .trailing
                )
                .offset(x: phase)
            )
            .onAppear {
                withAnimation(.linear(duration: 1.5).repeatForever(autoreverses: false)) {
                    phase = 400
                }
            }
    }
}
```

### 5.3 Pulse Glow Effect
```swift
struct PulseGlow: ViewModifier {
    @State private var scale: CGFloat = 1
    @State private var opacity: Double = 0.4

    func body(content: Content) -> some View {
        content
            .shadow(color: Color.appPrimary.opacity(opacity), radius: scale * 10)
            .onAppear {
                withAnimation(.easeInOut(duration: 2).repeatForever()) {
                    scale = 1.2
                    opacity = 0.2
                }
            }
    }
}
```

### 5.4 Score Update Animation
```swift
struct ScoreUpdateView: View {
    let oldScore: Double
    let newScore: Double
    let change: Double

    @State private var showChange = false
    @State private var floatOffset: CGFloat = 0
    @State private var floatOpacity: Double = 1

    var body: some View {
        VStack {
            HStack(spacing: 20) {
                VStack {
                    Text("Eski Puan")
                        .foregroundColor(.appTextMuted)
                    Text(String(format: "%.2f", oldScore))
                        .font(.title)
                        .fontWeight(.bold)
                }

                Text("→")
                    .font(.title)

                VStack {
                    Text("Yeni Puan")
                        .foregroundColor(.appTextMuted)
                    Text(String(format: "%.2f", newScore))
                        .font(.title)
                        .fontWeight(.bold)
                        .foregroundColor(change >= 0 ? .appSuccess : .appError)
                        .scaleEffect(showChange ? 1.3 : 1)
                }
            }

            // Floating score change
            Text(String(format: "%+.2f", change))
                .font(.title2)
                .fontWeight(.bold)
                .foregroundColor(change >= 0 ? .appSuccess : .appError)
                .offset(y: floatOffset)
                .opacity(floatOpacity)
        }
        .onAppear {
            withAnimation(.spring(duration: 0.5).delay(0.3)) {
                showChange = true
            }
            withAnimation(.easeOut(duration: 2).delay(0.5)) {
                floatOffset = -50
                floatOpacity = 0
            }
        }
    }
}
```

---

## 6. Network Layer

### 6.1 API Client
```swift
class APIClient {
    static let shared = APIClient()
    private let baseURL = "https://api.stitchdesign.com/v1"

    func request<T: Decodable>(
        endpoint: String,
        method: HTTPMethod = .get,
        body: Encodable? = nil
    ) async throws -> T {
        // Implementation
    }
}
```

### 6.2 Endpoints
```swift
enum Endpoint {
    // Auth
    case login(email: String, password: String)
    case register(name: String, email: String, password: String)
    case forgotPassword(email: String)

    // Users
    case getUser(id: UUID)
    case updateUser(id: UUID, data: UpdateUserRequest)
    case follow(userId: UUID)
    case unfollow(userId: UUID)

    // Restaurants
    case getRestaurants(page: Int, limit: Int)
    case getRestaurant(id: UUID)
    case searchRestaurants(query: String)

    // Reviews
    case createReview(request: CreateReviewRequest)
    case getReviews(restaurantId: UUID)

    // Feed
    case getFeed(page: Int)
    case likePost(postId: UUID)
}
```

---

## 7. State Management

### 7.1 App State
```swift
@MainActor
class AppState: ObservableObject {
    @Published var currentUser: User?
    @Published var isAuthenticated = false
    @Published var isLoading = false
}
```

### 7.2 View Models
```swift
// FeedViewModel
@MainActor
class FeedViewModel: ObservableObject {
    @Published var posts: [Post] = []
    @Published var newPost: Post?
    @Published var showNewPostAnimation = false
    @Published var isLoading = false

    func loadFeed() async { }
    func simulateNewPost() { }
    func likePost(_ postId: UUID) async { }
}

// RestaurantDetailViewModel
@MainActor
class RestaurantDetailViewModel: ObservableObject {
    @Published var restaurant: Restaurant?
    @Published var reviews: [Review] = []
    @Published var recommendingInfluencers: [User] = []

    func loadRestaurant(id: UUID) async { }
    func loadReviews() async { }
}

// WriteReviewViewModel
@MainActor
class WriteReviewViewModel: ObservableObject {
    @Published var rating: Int = 0
    @Published var content: String = ""
    @Published var selectedInfluencer: User?
    @Published var showInfluencerModal = false
    @Published var scoreChangeResult: ScoreChangeResult?

    func submitReview(restaurantId: UUID) async { }
}
```

---

## 8. Gerekli Kütüphaneler

```swift
// Package.swift dependencies

dependencies: [
    // Networking
    .package(url: "https://github.com/Alamofire/Alamofire.git", from: "5.8.0"),

    // Image Loading
    .package(url: "https://github.com/kean/Nuke.git", from: "12.0.0"),

    // Keychain
    .package(url: "https://github.com/kishikawakatsumi/KeychainAccess.git", from: "4.2.0"),

    // Lottie (optional - for complex animations)
    .package(url: "https://github.com/airbnb/lottie-ios.git", from: "4.3.0"),
]
```

---

## 9. Proje Yapısı

```
StitchDesign/
├── App/
│   ├── StitchDesignApp.swift
│   └── AppDelegate.swift
├── Core/
│   ├── Design/
│   │   ├── Colors.swift
│   │   ├── Typography.swift
│   │   └── Spacing.swift
│   ├── Network/
│   │   ├── APIClient.swift
│   │   ├── Endpoints.swift
│   │   └── NetworkError.swift
│   ├── Storage/
│   │   ├── UserDefaults+Extensions.swift
│   │   └── KeychainManager.swift
│   └── Utils/
│       ├── DateFormatter+Extensions.swift
│       └── NumberFormatter+Extensions.swift
├── Features/
│   ├── Auth/
│   │   ├── Views/
│   │   │   ├── LandingScreen.swift
│   │   │   ├── LoginScreen.swift
│   │   │   ├── SignUpScreen.swift
│   │   │   └── ForgotPasswordScreen.swift
│   │   └── ViewModels/
│   │       └── AuthViewModel.swift
│   ├── Feed/
│   │   ├── Views/
│   │   │   ├── FeedScreen.swift
│   │   │   ├── PostCard.swift
│   │   │   ├── NewPostCard.swift
│   │   │   └── StoriesRow.swift
│   │   └── ViewModels/
│   │       └── FeedViewModel.swift
│   ├── Restaurant/
│   │   ├── Views/
│   │   │   ├── RestaurantDetailScreen.swift
│   │   │   ├── WriteReviewScreen.swift
│   │   │   ├── InfluencerSelectModal.swift
│   │   │   └── ScoreUpdateResultView.swift
│   │   └── ViewModels/
│   │       ├── RestaurantDetailViewModel.swift
│   │       └── WriteReviewViewModel.swift
│   ├── Search/
│   │   ├── Views/
│   │   │   └── SearchScreen.swift
│   │   └── ViewModels/
│   │       └── SearchViewModel.swift
│   ├── Profile/
│   │   ├── Views/
│   │   │   ├── ProfileScreen.swift
│   │   │   └── EditProfileScreen.swift
│   │   └── ViewModels/
│   │       └── ProfileViewModel.swift
│   └── Notifications/
│       ├── Views/
│       │   └── NotificationsScreen.swift
│       └── ViewModels/
│           └── NotificationsViewModel.swift
├── Models/
│   ├── User.swift
│   ├── Restaurant.swift
│   ├── Review.swift
│   ├── Post.swift
│   └── ScoreChangeResult.swift
├── Components/
│   ├── Buttons/
│   │   ├── PrimaryButton.swift
│   │   └── SecondaryButton.swift
│   ├── Cards/
│   │   ├── RestaurantCard.swift
│   │   └── ReviewCard.swift
│   ├── Inputs/
│   │   ├── AppTextField.swift
│   │   └── StarRating.swift
│   └── Modifiers/
│       ├── ShimmerEffect.swift
│       ├── PulseGlow.swift
│       └── NewPostAnimation.swift
└── Resources/
    ├── Assets.xcassets/
    ├── Fonts/
    │   └── PlusJakartaSans/
    └── Localizable.strings
```

---

## 10. MVP Geliştirme Önceliği

### Faz 1: Temel Yapı (1-2 hafta)
- [ ] Proje setup & folder structure
- [ ] Design system implementation
- [ ] Network layer
- [ ] Auth flow (Login, Register, Forgot Password)

### Faz 2: Ana Ekranlar (2-3 hafta)
- [ ] Feed screen with post cards
- [ ] Restaurant detail screen
- [ ] Search screen
- [ ] Basic profile screen

### Faz 3: Review Sistemi (1-2 hafta)
- [ ] Write review screen
- [ ] Influencer selection modal
- [ ] Score update animation
- [ ] Review listing

### Faz 4: Polish & Animasyonlar (1 hafta)
- [ ] New post animation
- [ ] Shimmer loading states
- [ ] Pull to refresh
- [ ] Error handling & empty states

### Faz 5: Test & Optimizasyon (1 hafta)
- [ ] Unit tests
- [ ] UI tests
- [ ] Performance optimization
- [ ] Memory leak fixes

---

## 11. Test Bilgileri

### Demo Hesap
```
Email: ayse@email.com
Password: 123456
```

### Prototip URL
```
Prototip HTML dosyası: stitch_design_-_influencer_&_foodie_app/code.html
HTTP Server ile açılmalı (localhost:8000)
```

---

**Hazırlayan:** Claude AI
**Tarih:** 2024
**Versiyon:** 1.0
