# 📱 SPECIFICATION - ỨNG DỤNG SÀN ĐẤU GIÁ TRỰC TUYẾN (ANDROID)

## 📋 Thông tin dự án

| Thông tin | Chi tiết |
|-----------|----------|
| **Tên ứng dụng** | AuctionHub - Sàn Đấu Giá Trực Tuyến |
| **Nền tảng** | Android |
| **Ngôn ngữ** | Java + XML |
| **Min SDK** | API 24 (Android 7.0) |
| **Target SDK** | API 34 (Android 14) |
| **Architecture** | MVVM (Model-View-ViewModel) |

---

## 🎯 Tổng quan nhóm tính năng

### Bảng phân bổ nhóm tính năng (5 thành viên = 20 nhóm tính năng)

| STT | Nhóm tính năng | Số tính năng con | Độ ưu tiên | Phụ trách |
|-----|----------------|------------------|------------|-----------|
| 1 | Xác thực & Bảo mật | 6 | Cao | TV1 |
| 2 | Quản lý hồ sơ người dùng | 5 | Cao | TV1 |
| 3 | Duyệt & Tìm kiếm sản phẩm | 6 | Cao | TV2 |
| 4 | Chi tiết sản phẩm | 5 | Cao | TV2 |
| 5 | Đấu giá (Bidding) | 6 | Cao | TV3 |
| 6 | Quản lý sản phẩm (Seller) | 5 | Cao | TV3 |
| 7 | Hệ thống thông báo | 4 | Cao | TV4 |
| 8 | Chat & Trao đổi | 4 | Cao | TV4 |
| 9 | Thanh toán & Giao dịch | 5 | Cao | TV5 |
| 10 | Đánh giá & Phản hồi | 4 | Trung bình | TV5 |
| 11 | **Tính năng AI** | 4 | Cao | TV1 |
| 12 | Quản trị (Admin) | 5 | Trung bình | TV2 |
| 13 | Ví điện tử & Nạp tiền | 5 | Cao | TV3 |
| 14 | Quản lý địa chỉ giao hàng | 4 | Trung bình | TV4 |
| 15 | Báo cáo & Thống kê (User) | 4 | Trung bình | TV5 |
| 16 | Hệ thống khuyến mãi & Voucher | 5 | Trung bình | TV1 |
| 17 | Báo cáo vi phạm & Hỗ trợ | 4 | Trung bình | TV2 |
| 18 | Đa ngôn ngữ & Accessibility | 4 | Trung bình | TV3 |
| 19 | Offline Mode & Đồng bộ | 4 | Trung bình | TV4 |
| 20 | Widget & Quick Actions | 4 | Thấp | TV5 |

**Tổng cộng: 92 tính năng con**

---

## 📦 NHÓM TÍNH NĂNG CHI TIẾT

---

### 🔐 NHÓM 1: XÁC THỰC & BẢO MẬT

#### 1.1 Đăng ký tài khoản
**Mô tả:** Người dùng tạo tài khoản mới

**UI Components (XML):**
- `activity_register.xml`
- `EditText`: Họ tên, Email, Mật khẩu, Xác nhận mật khẩu, Số điện thoại
- `Button`: Đăng ký
- `CheckBox`: Đồng ý điều khoản
- `ImageView`: Upload avatar

**Logic (Java):**
```
RegisterActivity.java
├── validateInput()           // Kiểm tra dữ liệu nhập
├── checkEmailExists()        // Kiểm tra email trùng (API call)
├── hashPassword()            // Mã hóa mật khẩu (BCrypt)
├── sendOTP()                 // Gửi OTP xác nhận email
└── createAccount()           // Tạo tài khoản
```

**Validation Rules:**
- Email: Format hợp lệ, không trùng
- Mật khẩu: Tối thiểu 8 ký tự, có chữ hoa, chữ thường, số
- Họ tên: Không rỗng, 2-50 ký tự

**API Endpoint:** `POST /api/auth/register`

---

#### 1.2 Xác nhận OTP
**Mô tả:** Xác thực email qua mã OTP

**UI Components (XML):**
- `activity_otp_verification.xml`
- `EditText` x 6: Nhập từng số OTP
- `TextView`: Countdown timer (60s)
- `Button`: Xác nhận, Gửi lại OTP

**Logic (Java):**
```
OTPVerificationActivity.java
├── startCountdown()          // Đếm ngược 60s
├── verifyOTP()               // Xác thực OTP
├── resendOTP()               // Gửi lại OTP
└── autoFillOTP()             // Tự động điền từ SMS (SMS Retriever API)
```

**API Endpoint:** `POST /api/auth/verify-otp`

---

#### 1.3 Đăng nhập Email/Password
**Mô tả:** Đăng nhập bằng email và mật khẩu

**UI Components (XML):**
- `activity_login.xml`
- `TextInputLayout` + `EditText`: Email, Mật khẩu
- `CheckBox`: Ghi nhớ đăng nhập
- `Button`: Đăng nhập
- `TextView`: Quên mật khẩu, Đăng ký

**Logic (Java):**
```
LoginActivity.java
├── validateCredentials()     // Kiểm tra input
├── authenticateUser()        // Gọi API đăng nhập
├── saveToken()               // Lưu JWT token (SharedPreferences/EncryptedSharedPreferences)
├── navigateToHome()          // Chuyển màn hình chính
└── handleBiometricLogin()    // Đăng nhập vân tay/Face ID
```

**Security:**
- Sử dụng `EncryptedSharedPreferences` để lưu token
- JWT Token với refresh token mechanism

**API Endpoint:** `POST /api/auth/login`

---

#### 1.4 Đăng nhập Social (Google/Facebook)
**Mô tả:** Đăng nhập nhanh qua tài khoản mạng xã hội

**UI Components (XML):**
- `SignInButton` (Google)
- `LoginButton` (Facebook)

**Dependencies:**
```gradle
implementation 'com.google.android.gms:play-services-auth:20.7.0'
implementation 'com.facebook.android:facebook-login:16.2.0'
```

**Logic (Java):**
```
SocialLoginManager.java
├── initGoogleSignIn()        // Khởi tạo Google Sign-In
├── initFacebookLogin()       // Khởi tạo Facebook Login
├── handleGoogleSignInResult()
├── handleFacebookCallback()
└── linkSocialAccount()       // Liên kết với tài khoản hệ thống
```

**API Endpoint:** `POST /api/auth/social-login`

---

#### 1.5 Quên mật khẩu
**Mô tả:** Khôi phục mật khẩu qua email

**UI Components (XML):**
- `activity_forgot_password.xml`
- `EditText`: Email
- `Button`: Gửi yêu cầu

**Flow:**
1. Nhập email → Gửi OTP
2. Xác nhận OTP
3. Nhập mật khẩu mới
4. Đăng nhập lại

**API Endpoints:**
- `POST /api/auth/forgot-password`
- `POST /api/auth/reset-password`

---

#### 1.6 Đăng xuất
**Mô tả:** Thoát khỏi tài khoản

**Logic (Java):**
```
LogoutManager.java
├── clearToken()              // Xóa token local
├── clearUserData()           // Xóa dữ liệu người dùng
├── revokeToken()             // Hủy token trên server
└── navigateToLogin()         // Chuyển về màn hình đăng nhập
```

**API Endpoint:** `POST /api/auth/logout`

---

### 👤 NHÓM 2: QUẢN LÝ HỒ SƠ NGƯỜI DÙNG

#### 2.1 Xem hồ sơ cá nhân
**Mô tả:** Hiển thị thông tin người dùng

**UI Components (XML):**
- `fragment_profile.xml`
- `CircleImageView`: Avatar
- `TextView`: Họ tên, Email, Điểm đánh giá
- `RatingBar`: Hiển thị điểm
- `RecyclerView`: Danh sách đánh giá nhận được

**Layout Structure:**
```xml
<CoordinatorLayout>
    <AppBarLayout>
        <CollapsingToolbarLayout>
            <!-- Avatar + Cover image -->
        </CollapsingToolbarLayout>
    </AppBarLayout>
    <NestedScrollView>
        <!-- Profile details -->
    </NestedScrollView>
</CoordinatorLayout>
```

---

#### 2.2 Chỉnh sửa hồ sơ
**Mô tả:** Cập nhật thông tin cá nhân

**UI Components (XML):**
- `activity_edit_profile.xml`
- `EditText`: Họ tên, Địa chỉ, Ngày sinh
- `ImageView` + `FloatingActionButton`: Đổi avatar
- `Button`: Lưu thay đổi

**Features:**
- Image Picker (Gallery/Camera)
- Image Cropping
- Date Picker cho ngày sinh

**API Endpoint:** `PUT /api/users/profile`

---

#### 2.3 Đổi mật khẩu
**Mô tả:** Thay đổi mật khẩu (yêu cầu mật khẩu cũ)

**UI Components (XML):**
- `activity_change_password.xml`
- `TextInputLayout`: Mật khẩu cũ, Mật khẩu mới, Xác nhận

**Validation:**
- Mật khẩu cũ phải đúng
- Mật khẩu mới khác mật khẩu cũ
- Độ mạnh mật khẩu (PasswordStrengthMeter)

**API Endpoint:** `PUT /api/users/change-password`

---

#### 2.4 Xem điểm đánh giá chi tiết
**Mô tả:** Xem lịch sử đánh giá và nhận xét

**UI Components (XML):**
- `activity_rating_history.xml`
- `TabLayout`: Đánh giá từ người mua / Đánh giá từ người bán
- `RecyclerView` + `item_rating.xml`

**Item Rating Layout:**
```xml
<CardView>
    <LinearLayout>
        <ImageView android:id="@+id/ivRaterAvatar"/>
        <TextView android:id="@+id/tvRaterName"/>
        <ImageView android:id="@+id/ivRatingIcon"/> <!-- +1 or -1 -->
        <TextView android:id="@+id/tvComment"/>
        <TextView android:id="@+id/tvDate"/>
    </LinearLayout>
</CardView>
```

**API Endpoint:** `GET /api/users/ratings`

---

#### 2.5 Yêu cầu nâng cấp Seller
**Mô tả:** Bidder xin nâng cấp thành Seller

**UI Components (XML):**
- `activity_upgrade_request.xml`
- `TextView`: Thông tin yêu cầu, Thời hạn 7 ngày
- `EditText`: Lý do muốn trở thành seller
- `Button`: Gửi yêu cầu

**Status Tracking:**
- Pending: Đang chờ duyệt
- Approved: Đã được duyệt
- Rejected: Bị từ chối

**API Endpoint:** `POST /api/users/upgrade-request`

---

### 🔍 NHÓM 3: DUYỆT & TÌM KIẾM SẢN PHẨM

#### 3.1 Trang chủ
**Mô tả:** Màn hình chính hiển thị các sản phẩm nổi bật

**UI Components (XML):**
- `fragment_home.xml`
- `ViewPager2`: Banner quảng cáo
- `RecyclerView` (Horizontal): Top 5 sắp kết thúc
- `RecyclerView` (Horizontal): Top 5 nhiều lượt bid nhất
- `RecyclerView` (Horizontal): Top 5 giá cao nhất
- `SwipeRefreshLayout`: Pull to refresh

**Layout Structure:**
```xml
<SwipeRefreshLayout>
    <NestedScrollView>
        <LinearLayout orientation="vertical">
            <!-- Banner Slider -->
            <ViewPager2 android:id="@+id/vpBanner"/>
            
            <!-- Section: Sắp kết thúc -->
            <include layout="@layout/section_products"/>
            
            <!-- Section: Nhiều lượt bid -->
            <include layout="@layout/section_products"/>
            
            <!-- Section: Giá cao nhất -->
            <include layout="@layout/section_products"/>
        </LinearLayout>
    </NestedScrollView>
</SwipeRefreshLayout>
```

**API Endpoints:**
- `GET /api/products/ending-soon?limit=5`
- `GET /api/products/most-bids?limit=5`
- `GET /api/products/highest-price?limit=5`

---

#### 3.2 Menu danh mục 2 cấp
**Mô tả:** Hiển thị danh mục sản phẩm theo cấp bậc

**UI Components (XML):**
- `fragment_categories.xml`
- `ExpandableListView` hoặc `RecyclerView` với expandable items

**Data Structure:**
```java
public class Category {
    private int id;
    private String name;
    private String icon;
    private List<Category> subCategories;
}
```

**Example Categories:**
```
Điện tử
├── Điện thoại di động
├── Máy tính xách tay
└── Máy tính bảng

Thời trang
├── Giày
├── Đồng hồ
└── Túi xách
```

**API Endpoint:** `GET /api/categories`

---

#### 3.3 Danh sách sản phẩm theo danh mục
**Mô tả:** Hiển thị sản phẩm thuộc một danh mục với phân trang

**UI Components (XML):**
- `activity_product_list.xml`
- `Toolbar`: Tên danh mục
- `RecyclerView` + `GridLayoutManager` (2 columns)
- `item_product_grid.xml`

**Product Item Layout:**
```xml
<CardView>
    <LinearLayout orientation="vertical">
        <ImageView android:id="@+id/ivProductImage"/>
        <TextView android:id="@+id/tvProductName"/>
        <TextView android:id="@+id/tvCurrentPrice"/>
        <TextView android:id="@+id/tvBuyNowPrice"/>
        <TextView android:id="@+id/tvTimeRemaining"/>
        <TextView android:id="@+id/tvBidCount"/>
        <TextView android:id="@+id/tvHighestBidder"/>
        <!-- Badge "MỚI" nếu đăng trong N phút -->
        <TextView android:id="@+id/tvNewBadge"/>
    </LinearLayout>
</CardView>
```

**Pagination:** Sử dụng `Paging 3 Library`

**API Endpoint:** `GET /api/products?categoryId={id}&page={page}&limit=20`

---

#### 3.4 Tìm kiếm Full-text
**Mô tả:** Tìm kiếm sản phẩm theo từ khóa

**UI Components (XML):**
- `activity_search.xml`
- `SearchView` trong Toolbar
- `ChipGroup`: Lịch sử tìm kiếm, Gợi ý
- `RecyclerView`: Kết quả tìm kiếm

**Features:**
- Autocomplete suggestions
- Search history (local SQLite)
- Filter by category
- Debounce input (300ms)

**API Endpoint:** `GET /api/products/search?q={query}&categoryId={id}`

---

#### 3.5 Sắp xếp kết quả
**Mô tả:** Sắp xếp danh sách sản phẩm theo tiêu chí

**UI Components (XML):**
- `BottomSheetDialog` với `RadioGroup`
- Options:
  - Thời gian kết thúc giảm dần
  - Giá tăng dần
  - Giá giảm dần
  - Mới nhất

**API Endpoint:** `GET /api/products?sort={sortType}&order={asc|desc}`

---

#### 3.6 Lọc sản phẩm
**Mô tả:** Lọc sản phẩm theo nhiều tiêu chí

**UI Components (XML):**
- `activity_filter.xml` hoặc `BottomSheetDialogFragment`
- `RangeSlider`: Khoảng giá
- `ChipGroup`: Danh mục
- `CheckBox`: Có giá mua ngay, Còn thời gian
- `Button`: Áp dụng, Reset

---

### 📦 NHÓM 4: CHI TIẾT SẢN PHẨM

#### 4.1 Xem chi tiết sản phẩm
**Mô tả:** Hiển thị đầy đủ thông tin sản phẩm

**UI Components (XML):**
- `activity_product_detail.xml`

**Layout Structure:**
```xml
<CoordinatorLayout>
    <AppBarLayout>
        <CollapsingToolbarLayout>
            <ViewPager2 android:id="@+id/vpProductImages"/>
            <!-- Image indicator dots -->
        </CollapsingToolbarLayout>
    </AppBarLayout>
    
    <NestedScrollView>
        <LinearLayout>
            <!-- Tên sản phẩm -->
            <TextView android:id="@+id/tvProductName"/>
            
            <!-- Giá hiện tại -->
            <TextView android:id="@+id/tvCurrentPrice"/>
            
            <!-- Giá mua ngay (nếu có) -->
            <TextView android:id="@+id/tvBuyNowPrice"/>
            
            <!-- Thông tin người bán -->
            <include layout="@layout/layout_seller_info"/>
            
            <!-- Thông tin người đặt giá cao nhất -->
            <include layout="@layout/layout_highest_bidder"/>
            
            <!-- Thời gian -->
            <TextView android:id="@+id/tvStartTime"/>
            <TextView android:id="@+id/tvEndTime"/>
            <TextView android:id="@+id/tvTimeRemaining"/>
            
            <!-- Mô tả sản phẩm (WebView for HTML content) -->
            <WebView android:id="@+id/wvDescription"/>
            
            <!-- Q&A Section -->
            <include layout="@layout/layout_qa_section"/>
            
            <!-- Sản phẩm liên quan -->
            <RecyclerView android:id="@+id/rvRelatedProducts"/>
        </LinearLayout>
    </NestedScrollView>
    
    <!-- Bottom Action Bar -->
    <LinearLayout android:id="@+id/bottomBar">
        <Button android:id="@+id/btnAddToWatchlist"/>
        <Button android:id="@+id/btnPlaceBid"/>
        <Button android:id="@+id/btnBuyNow"/>
    </LinearLayout>
</CoordinatorLayout>
```

**Time Display Logic:**
```java
public String formatTimeRemaining(long endTimeMillis) {
    long remaining = endTimeMillis - System.currentTimeMillis();
    long days = TimeUnit.MILLISECONDS.toDays(remaining);
    
    if (days > 3) {
        return new SimpleDateFormat("dd/MM/yyyy HH:mm").format(new Date(endTimeMillis));
    } else {
        // Relative time: "3 ngày nữa", "10 phút nữa"
        return getRelativeTimeSpan(remaining);
    }
}
```

**API Endpoint:** `GET /api/products/{id}`

---

#### 4.2 Gallery ảnh sản phẩm
**Mô tả:** Xem ảnh sản phẩm full-screen với zoom

**UI Components (XML):**
- `activity_image_gallery.xml`
- `ViewPager2` với `PhotoView` (zoom support)
- `RecyclerView`: Thumbnail navigation

**Dependencies:**
```gradle
implementation 'com.github.chrisbanes:PhotoView:2.3.0'
```

---

#### 4.3 Xem lịch sử đấu giá
**Mô tả:** Hiển thị danh sách các lượt đặt giá

**UI Components (XML):**
- `BottomSheetDialogFragment`
- `RecyclerView` + `item_bid_history.xml`

**Item Layout:**
```xml
<LinearLayout>
    <TextView android:id="@+id/tvBidTime"/>
    <TextView android:id="@+id/tvBidderName"/> <!-- Masked: ****Khoa -->
    <TextView android:id="@+id/tvBidAmount"/>
</LinearLayout>
```

**Name Masking Logic:**
```java
public String maskBidderName(String name) {
    if (name.length() <= 4) return "****" + name;
    return "****" + name.substring(name.length() - 4);
}
```

**API Endpoint:** `GET /api/products/{id}/bid-history`

---

#### 4.4 Q&A Section
**Mô tả:** Xem và đặt câu hỏi cho người bán

**UI Components (XML):**
- `layout_qa_section.xml` (include trong product detail)
- `RecyclerView` + `item_qa.xml`
- `FloatingActionButton`: Đặt câu hỏi mới

**Item Q&A Layout:**
```xml
<CardView>
    <LinearLayout orientation="vertical">
        <!-- Question -->
        <LinearLayout>
            <ImageView android:id="@+id/ivAskerAvatar"/>
            <TextView android:id="@+id/tvQuestion"/>
            <TextView android:id="@+id/tvQuestionTime"/>
        </LinearLayout>
        
        <!-- Answer (if exists) -->
        <LinearLayout android:visibility="gone">
            <ImageView android:id="@+id/ivSellerAvatar"/>
            <TextView android:id="@+id/tvAnswer"/>
            <TextView android:id="@+id/tvAnswerTime"/>
        </LinearLayout>
    </LinearLayout>
</CardView>
```

**API Endpoints:**
- `GET /api/products/{id}/questions`
- `POST /api/products/{id}/questions`

---

#### 4.5 Sản phẩm liên quan
**Mô tả:** Hiển thị 5 sản phẩm cùng danh mục

**UI Components (XML):**
- `RecyclerView` (Horizontal) trong product detail
- Reuse `item_product_horizontal.xml`

**API Endpoint:** `GET /api/products/{id}/related?limit=5`

---

### 💰 NHÓM 5: ĐẤU GIÁ (BIDDING)

#### 5.1 Đặt giá thường
**Mô tả:** Người mua đặt giá cho sản phẩm

**UI Components (XML):**
- `BottomSheetDialogFragment` hoặc `DialogFragment`
- `TextView`: Giá hiện tại, Bước giá, Giá đề nghị
- `EditText`: Số tiền đặt giá
- `Button`: +/- để điều chỉnh theo bước giá
- `Button`: Xác nhận đặt giá

**Validation Logic:**
```java
public class BidValidator {
    public BidValidationResult validate(Product product, User bidder, double bidAmount) {
        // Kiểm tra điểm đánh giá >= 80%
        if (bidder.getRatingScore() < 0.8 && bidder.getTotalRatings() > 0) {
            return BidValidationResult.RATING_TOO_LOW;
        }
        
        // Kiểm tra bidder chưa được đánh giá + seller cho phép
        if (bidder.getTotalRatings() == 0 && !product.isAllowNewBidder()) {
            return BidValidationResult.NEW_BIDDER_NOT_ALLOWED;
        }
        
        // Kiểm tra giá hợp lệ
        double minBid = product.getCurrentPrice() + product.getBidStep();
        if (bidAmount < minBid) {
            return BidValidationResult.BID_TOO_LOW;
        }
        
        return BidValidationResult.VALID;
    }
}
```

**Confirmation Dialog:**
```xml
<AlertDialog>
    <TextView>Xác nhận đặt giá {amount} cho sản phẩm {name}?</TextView>
    <Button android:text="Xác nhận"/>
    <Button android:text="Hủy"/>
</AlertDialog>
```

**API Endpoint:** `POST /api/products/{id}/bids`

---

#### 5.2 Đấu giá tự động
**Mô tả:** Hệ thống tự động đặt giá đến mức tối đa

**UI Components (XML):**
- `dialog_auto_bid.xml`
- `TextView`: Giải thích cơ chế
- `EditText`: Giá tối đa sẵn sàng trả
- `Switch`: Bật/tắt auto-bid
- `Button`: Xác nhận

**Auto-bid Logic (Server-side):**
```
Khi có lượt bid mới:
1. Kiểm tra các auto-bid đang active
2. Tìm auto-bid có giá tối đa cao nhất
3. Đặt giá = giá hiện tại + bước giá (hoặc giá tối đa nếu thấp hơn)
4. Cập nhật người giữ giá
5. Gửi thông báo cho các bên liên quan
```

**API Endpoint:** `POST /api/products/{id}/auto-bid`

---

#### 5.3 Mua ngay
**Mô tả:** Mua sản phẩm với giá mua ngay (nếu có)

**UI Components (XML):**
- `AlertDialog` xác nhận mua ngay
- Navigate đến flow thanh toán

**API Endpoint:** `POST /api/products/{id}/buy-now`

---

#### 5.4 Thêm vào Watchlist
**Mô tả:** Lưu sản phẩm yêu thích để theo dõi

**UI Components (XML):**
- `ImageButton` hoặc `ToggleButton` (heart icon)
- Animation khi thêm/xóa

**Locations:**
- Product list item
- Product detail page

**API Endpoints:**
- `POST /api/watchlist/{productId}`
- `DELETE /api/watchlist/{productId}`

---

#### 5.5 Xem Watchlist
**Mô tả:** Danh sách sản phẩm đang theo dõi

**UI Components (XML):**
- `fragment_watchlist.xml`
- `RecyclerView` + swipe to delete
- `EmptyStateView` khi không có sản phẩm

**API Endpoint:** `GET /api/watchlist`

---

#### 5.6 Xem sản phẩm đang đấu giá
**Mô tả:** Danh sách sản phẩm user đang tham gia đấu giá

**UI Components (XML):**
- `fragment_my_bids.xml`
- `TabLayout`: Đang đấu giá / Đã thắng / Đã thua
- `RecyclerView` cho mỗi tab

**API Endpoints:**
- `GET /api/users/bids?status=active`
- `GET /api/users/bids?status=won`
- `GET /api/users/bids?status=lost`

---

### 🏪 NHÓM 6: QUẢN LÝ SẢN PHẨM (SELLER)

#### 6.1 Đăng sản phẩm đấu giá
**Mô tả:** Seller đăng sản phẩm mới

**UI Components (XML):**
- `activity_create_product.xml`
- Multi-step form với `ViewPager2` + `StepIndicator`

**Step 1: Thông tin cơ bản**
```xml
<LinearLayout>
    <TextInputLayout android:hint="Tên sản phẩm"/>
    <Spinner android:id="@+id/spCategory"/> <!-- 2 cấp -->
</LinearLayout>
```

**Step 2: Hình ảnh (tối thiểu 3)**
```xml
<LinearLayout>
    <RecyclerView android:id="@+id/rvImages"/> <!-- Grid 3 columns -->
    <Button android:text="Thêm ảnh"/>
</LinearLayout>
```

**Step 3: Giá & Thời gian**
```xml
<LinearLayout>
    <TextInputLayout android:hint="Giá khởi điểm"/>
    <TextInputLayout android:hint="Bước giá"/>
    <TextInputLayout android:hint="Giá mua ngay (tùy chọn)"/>
    <TextView android:text="Thời gian kết thúc"/>
    <Button android:id="@+id/btnPickDateTime"/>
    <SwitchCompat android:text="Tự động gia hạn"/>
</LinearLayout>
```

**Step 4: Mô tả sản phẩm**
```xml
<LinearLayout>
    <!-- WYSIWYG Editor -->
    <WebView android:id="@+id/wvEditor"/>
    <!-- Hoặc dùng RichEditor library -->
</LinearLayout>
```

**WYSIWYG Options:**
- [RichEditor-Android](https://github.com/nickeddy/RichEditor-Android)
- WebView + Quill.js

**API Endpoint:** `POST /api/products`

---

#### 6.2 Bổ sung mô tả sản phẩm
**Mô tả:** Thêm thông tin vào mô tả (append, không replace)

**UI Components (XML):**
- `dialog_append_description.xml`
- `EditText` multiline hoặc WYSIWYG editor nhỏ
- `Button`: Thêm mô tả

**Display Format:**
```
[Mô tả gốc]

✏️ 31/10/2025
[Nội dung bổ sung 1]

✏️ 05/11/2025
[Nội dung bổ sung 2]
```

**API Endpoint:** `POST /api/products/{id}/description`

---

#### 6.3 Từ chối Bidder
**Mô tả:** Seller từ chối 1 bidder tham gia đấu giá

**UI Components (XML):**
- Dialog trong product detail (seller view)
- `RecyclerView`: Danh sách bidder
- `Button`: Từ chối

**Logic:**
- Bidder bị từ chối không thể đặt giá sản phẩm này
- Nếu bidder đang giữ giá cao nhất → chuyển cho người thứ 2
- Gửi email thông báo cho bidder

**API Endpoint:** `POST /api/products/{id}/reject-bidder/{bidderId}`

---

#### 6.4 Trả lời câu hỏi
**Mô tả:** Seller trả lời Q&A

**UI Components (XML):**
- `dialog_answer_question.xml`
- `TextView`: Câu hỏi
- `EditText`: Câu trả lời
- `Button`: Gửi

**Notification:**
- Gửi email cho người hỏi và các bidder khác

**API Endpoint:** `PUT /api/questions/{questionId}/answer`

---

#### 6.5 Quản lý sản phẩm của tôi
**Mô tả:** Xem danh sách sản phẩm đã đăng

**UI Components (XML):**
- `fragment_my_products.xml`
- `TabLayout`: Đang đấu giá / Đã kết thúc / Đã bán
- `RecyclerView` cho mỗi tab
- `FloatingActionButton`: Đăng sản phẩm mới

**API Endpoints:**
- `GET /api/users/products?status=active`
- `GET /api/users/products?status=ended`
- `GET /api/users/products?status=sold`

---

### 🔔 NHÓM 7: HỆ THỐNG THÔNG BÁO

#### 7.1 Push Notification
**Mô tả:** Nhận thông báo real-time

**Implementation:**
- Firebase Cloud Messaging (FCM)

**Dependencies:**
```gradle
implementation 'com.google.firebase:firebase-messaging:23.4.0'
```

**Notification Types:**
| Event | Recipient | Title | Body |
|-------|-----------|-------|------|
| Có lượt bid mới | Seller | Có người đặt giá mới | {bidder} đã đặt giá {amount} cho {product} |
| Bị outbid | Previous highest bidder | Bạn đã bị vượt giá | {product} có giá mới {amount} |
| Đấu giá kết thúc | Winner | Chúc mừng! | Bạn đã thắng đấu giá {product} |
| Có câu hỏi mới | Seller | Có câu hỏi mới | {user} hỏi về {product} |
| Sản phẩm sắp kết thúc | Watchlist users | Sắp kết thúc | {product} sẽ kết thúc trong 1 giờ |

**Service:**
```java
public class AuctionFirebaseMessagingService extends FirebaseMessagingService {
    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Handle notification
        showNotification(remoteMessage);
    }
    
    @Override
    public void onNewToken(String token) {
        // Send token to server
        sendRegistrationToServer(token);
    }
}
```

---

#### 7.2 In-app Notification
**Mô tả:** Danh sách thông báo trong app

**UI Components (XML):**
- `fragment_notifications.xml`
- `RecyclerView` + `item_notification.xml`
- Badge count trên icon

**Item Layout:**
```xml
<LinearLayout>
    <ImageView android:id="@+id/ivIcon"/>
    <LinearLayout>
        <TextView android:id="@+id/tvTitle"/>
        <TextView android:id="@+id/tvMessage"/>
        <TextView android:id="@+id/tvTime"/>
    </LinearLayout>
    <View android:id="@+id/unreadIndicator"/>
</LinearLayout>
```

**API Endpoints:**
- `GET /api/notifications`
- `PUT /api/notifications/{id}/read`
- `PUT /api/notifications/read-all`

---

#### 7.3 Email Notification
**Mô tả:** Gửi email cho các sự kiện quan trọng

**Events (Server-side):**
- Đăng ký thành công
- Đặt giá thành công
- Bị vượt giá
- Thắng đấu giá
- Đấu giá kết thúc (không có người mua)
- Có câu hỏi/trả lời mới
- Bidder bị từ chối

---

#### 7.4 Notification Settings
**Mô tả:** Cài đặt loại thông báo muốn nhận

**UI Components (XML):**
- `fragment_notification_settings.xml`
- `SwitchCompat` cho từng loại notification

**Settings:**
- Push notifications (on/off)
- Email notifications (on/off)
- Notify when outbid
- Notify when watchlist item ending
- Notify when have new question

**API Endpoint:** `PUT /api/users/notification-settings`

---

### 💬 NHÓM 8: CHAT & TRAO ĐỔI

#### 8.1 Chat giữa Buyer và Seller
**Mô tả:** Chat real-time sau khi thắng đấu giá

**UI Components (XML):**
- `activity_chat.xml`
- `RecyclerView`: Danh sách tin nhắn
- `EditText`: Nhập tin nhắn
- `ImageButton`: Gửi ảnh
- `Button`: Gửi

**Implementation:**
- Firebase Realtime Database hoặc
- Socket.IO cho real-time

**Dependencies:**
```gradle
implementation 'com.google.firebase:firebase-database:20.3.0'
// hoặc
implementation 'io.socket:socket.io-client:2.1.0'
```

**Message Model:**
```java
public class ChatMessage {
    private String id;
    private String senderId;
    private String receiverId;
    private String message;
    private String imageUrl;
    private long timestamp;
    private boolean isRead;
}
```

---

#### 8.2 Danh sách cuộc trò chuyện
**Mô tả:** Hiển thị các cuộc chat đang có

**UI Components (XML):**
- `fragment_conversations.xml`
- `RecyclerView` + `item_conversation.xml`

**Item Layout:**
```xml
<LinearLayout>
    <ImageView android:id="@+id/ivProductImage"/>
    <LinearLayout>
        <TextView android:id="@+id/tvProductName"/>
        <TextView android:id="@+id/tvOtherUserName"/>
        <TextView android:id="@+id/tvLastMessage"/>
    </LinearLayout>
    <TextView android:id="@+id/tvTime"/>
    <TextView android:id="@+id/tvUnreadCount"/>
</LinearLayout>
```

---

#### 8.3 Gửi hình ảnh trong chat
**Mô tả:** Đính kèm hình ảnh (hóa đơn, chứng từ)

**Implementation:**
- Image picker
- Upload to Firebase Storage / Server
- Send image URL in chat

---

#### 8.4 Thông báo tin nhắn mới
**Mô tả:** Push notification khi có tin nhắn mới

**Integration với FCM**

---

### 💳 NHÓM 9: THANH TOÁN & GIAO DỊCH

#### 9.1 Flow hoàn tất đơn hàng
**Mô tả:** Quy trình 4 bước sau đấu giá

**UI Components (XML):**
- `activity_order_completion.xml`
- `Stepper` hiển thị 4 bước:

**Step 1: Người mua cung cấp thông tin**
```xml
<LinearLayout>
    <TextView android:text="Địa chỉ giao hàng"/>
    <EditText android:id="@+id/etAddress"/>
    <TextView android:text="Hóa đơn thanh toán"/>
    <ImageView android:id="@+id/ivPaymentProof"/>
    <Button android:text="Tải lên hóa đơn"/>
    <Button android:text="Xác nhận"/>
</LinearLayout>
```

**Step 2: Người bán xác nhận**
```xml
<LinearLayout>
    <TextView android:text="Xác nhận đã nhận tiền"/>
    <ImageView android:id="@+id/ivShippingProof"/>
    <Button android:text="Tải lên hóa đơn vận chuyển"/>
    <Button android:text="Xác nhận đã gửi hàng"/>
</LinearLayout>
```

**Step 3: Người mua xác nhận nhận hàng**
```xml
<LinearLayout>
    <TextView android:text="Bạn đã nhận được hàng?"/>
    <Button android:text="Đã nhận hàng"/>
    <Button android:text="Chưa nhận hàng"/>
</LinearLayout>
```

**Step 4: Đánh giá**
```xml
<LinearLayout>
    <TextView android:text="Đánh giá giao dịch"/>
    <RadioGroup>
        <RadioButton android:text="+1 (Tốt)"/>
        <RadioButton android:text="-1 (Không tốt)"/>
    </RadioGroup>
    <EditText android:hint="Nhận xét"/>
    <Button android:text="Gửi đánh giá"/>
</LinearLayout>
```

**Order Status:**
```java
public enum OrderStatus {
    PENDING_BUYER_INFO,      // Chờ buyer cung cấp thông tin
    PENDING_SELLER_CONFIRM,  // Chờ seller xác nhận
    SHIPPED,                 // Đã gửi hàng
    DELIVERED,               // Đã nhận hàng
    COMPLETED,               // Hoàn tất
    CANCELLED                // Đã hủy
}
```

---

#### 9.2 Hủy giao dịch (Seller)
**Mô tả:** Seller hủy giao dịch và đánh giá -1 buyer

**UI Components (XML):**
- `AlertDialog` xác nhận hủy
- Tự động thêm comment: "Người thắng không thanh toán"

**API Endpoint:** `POST /api/orders/{orderId}/cancel`

---

#### 9.3 Xem lịch sử đơn hàng
**Mô tả:** Danh sách các đơn hàng đã thực hiện

**UI Components (XML):**
- `fragment_order_history.xml`
- `TabLayout`: Đang xử lý / Hoàn tất / Đã hủy
- `RecyclerView` + `item_order.xml`

---

#### 9.4 Chi tiết đơn hàng
**Mô tả:** Xem chi tiết một đơn hàng

**UI Components (XML):**
- `activity_order_detail.xml`
- Thông tin sản phẩm
- Thông tin người mua/bán
- Trạng thái đơn hàng
- Timeline các bước
- Nút chat

---

#### 9.5 Thay đổi đánh giá
**Mô tả:** Cho phép thay đổi đánh giá đã gửi

**UI Components (XML):**
- `dialog_edit_rating.xml`
- Hiển thị đánh giá hiện tại
- Cho phép thay đổi +1/-1 và nhận xét

**API Endpoint:** `PUT /api/orders/{orderId}/rating`

---

### ⭐ NHÓM 10: ĐÁNH GIÁ & PHẢN HỒI

#### 10.1 Đánh giá người bán/mua
**Mô tả:** Đánh giá sau giao dịch

**UI Components (XML):**
- `dialog_rating.xml`
- `RadioGroup`: +1 / -1
- `EditText`: Nhận xét
- `Button`: Gửi đánh giá

---

#### 10.2 Xem đánh giá của người dùng khác
**Mô tả:** Xem điểm và nhận xét của user

**UI Components (XML):**
- `activity_user_ratings.xml`
- Header: Avatar, Tên, Điểm tổng
- `RecyclerView`: Danh sách đánh giá

---

#### 10.3 Tính điểm đánh giá
**Mô tả:** Công thức tính điểm

**Logic:**
```java
public class RatingCalculator {
    public double calculateScore(User user) {
        int positive = user.getPositiveRatings();
        int negative = user.getNegativeRatings();
        int total = positive + negative;
        
        if (total == 0) return 1.0; // Chưa được đánh giá = 100%
        return (double) positive / total;
    }
    
    public boolean canBid(User bidder, Product product) {
        double score = calculateScore(bidder);
        int totalRatings = bidder.getTotalRatings();
        
        // Chưa có đánh giá & seller cho phép
        if (totalRatings == 0) {
            return product.isAllowNewBidder();
        }
        
        // Điểm >= 80%
        return score >= 0.8;
    }
}
```

---

#### 10.4 Hiển thị điểm đánh giá
**Mô tả:** UI component hiển thị điểm

**Custom View:**
```xml
<LinearLayout>
    <TextView android:id="@+id/tvScore"/> <!-- 95% -->
    <TextView android:id="@+id/tvTotal"/> <!-- (20 đánh giá) -->
    <ImageView android:id="@+id/ivTrustBadge"/> <!-- Badge nếu điểm cao -->
</LinearLayout>
```

---

### 🤖 NHÓM 11: TÍNH NĂNG AI (BẮT BUỘC)

#### 11.1 AI Gợi ý giá khởi điểm
**Mô tả:** AI phân tích và đề xuất giá khởi điểm phù hợp

**Implementation:**
- Gọi API AI (GPT/Gemini) với thông tin sản phẩm
- Phân tích giá các sản phẩm tương tự đã bán

**UI Components (XML):**
- `Button`: "🤖 Gợi ý giá"
- `dialog_ai_price_suggestion.xml`
- Hiển thị giá đề xuất + lý do

**Prompt Template:**
```
Dựa trên thông tin sau, hãy đề xuất giá khởi điểm phù hợp:
- Tên sản phẩm: {name}
- Danh mục: {category}
- Mô tả: {description}
- Giá các sản phẩm tương tự: {similar_prices}

Trả về JSON: { "suggested_price": number, "reason": string }
```

**API Integration:**
```java
public class AIPriceService {
    private static final String GEMINI_API_URL = "https://generativelanguage.googleapis.com/v1/models/gemini-pro:generateContent";
    
    public void suggestPrice(Product product, AICallback callback) {
        // Build prompt
        // Call Gemini API
        // Parse response
    }
}
```

---

#### 11.2 AI Chatbot hỗ trợ
**Mô tả:** Chatbot trả lời câu hỏi về app, cách đấu giá

**UI Components (XML):**
- `FloatingActionButton`: Icon chatbot
- `activity_ai_chat.xml`
- Chat interface với AI

**Features:**
- Trả lời FAQ
- Hướng dẫn sử dụng app
- Giải thích quy trình đấu giá
- Hỗ trợ tìm kiếm sản phẩm

**Knowledge Base:**
- Quy tắc đấu giá
- Cách tính điểm đánh giá
- Quy trình thanh toán
- FAQ

---

#### 11.3 AI Mô tả sản phẩm tự động
**Mô tả:** Tự động tạo mô tả từ ảnh sản phẩm

**Implementation:**
- Upload ảnh → AI Vision API
- Nhận diện sản phẩm, tình trạng
- Generate mô tả chi tiết

**UI Components (XML):**
- `Button`: "🤖 Tạo mô tả từ ảnh"
- Progress indicator
- `EditText` với mô tả được generate (có thể chỉnh sửa)

**Flow:**
1. User upload ảnh sản phẩm
2. Gọi Gemini Vision API
3. AI phân tích ảnh và tạo mô tả
4. Hiển thị để user review/edit
5. User xác nhận sử dụng

---

#### 11.4 AI Phát hiện gian lận
**Mô tả:** Phát hiện các dấu hiệu đấu giá bất thường

**Features:**
- Phát hiện shill bidding (tự đẩy giá)
- Cảnh báo giá bất thường
- Phát hiện tài khoản fake

**Implementation (Server-side + Notification):**
```java
public class FraudDetectionService {
    public void analyzeBidPattern(Product product) {
        // Phân tích pattern đấu giá
        // Kiểm tra IP trùng lặp
        // Kiểm tra thời gian bid bất thường
        // Alert admin nếu phát hiện gian lận
    }
}
```

---

### 👨‍💼 NHÓM 12: QUẢN TRỊ (ADMIN)

#### 12.1 Dashboard Admin
**Mô tả:** Tổng quan hệ thống

**UI Components (XML):**
- `fragment_admin_dashboard.xml`
- Cards: Tổng users, Sản phẩm đang đấu giá, Doanh thu
- Charts (MPAndroidChart)

**Dependencies:**
```gradle
implementation 'com.github.PhilJay:MPAndroidChart:v3.1.0'
```

---

#### 12.2 Quản lý danh mục
**Mô tả:** CRUD danh mục sản phẩm

**UI Components (XML):**
- `fragment_admin_categories.xml`
- `RecyclerView` expandable (2 cấp)
- `FloatingActionButton`: Thêm danh mục
- Swipe actions: Edit, Delete

**Validation:**
- Không xóa danh mục có sản phẩm

**API Endpoints:**
- `GET /api/admin/categories`
- `POST /api/admin/categories`
- `PUT /api/admin/categories/{id}`
- `DELETE /api/admin/categories/{id}`

---

#### 12.3 Quản lý sản phẩm
**Mô tả:** Xem và gỡ sản phẩm vi phạm

**UI Components (XML):**
- `fragment_admin_products.xml`
- `RecyclerView` với search và filter
- Action: Gỡ sản phẩm

**API Endpoints:**
- `GET /api/admin/products`
- `DELETE /api/admin/products/{id}`

---

#### 12.4 Quản lý người dùng
**Mô tả:** Xem, khóa tài khoản user

**UI Components (XML):**
- `fragment_admin_users.xml`
- `RecyclerView` + search
- `TabLayout`: Tất cả / Bidder / Seller / Bị khóa
- Actions: Xem chi tiết, Khóa/Mở khóa

**API Endpoints:**
- `GET /api/admin/users`
- `PUT /api/admin/users/{id}/status`

---

#### 12.5 Duyệt yêu cầu nâng cấp Seller
**Mô tả:** Xem và duyệt yêu cầu upgrade

**UI Components (XML):**
- `fragment_admin_upgrade_requests.xml`
- `RecyclerView` + `item_upgrade_request.xml`
- Actions: Duyệt, Từ chối

**Item Layout:**
```xml
<CardView>
    <LinearLayout>
        <ImageView android:id="@+id/ivUserAvatar"/>
        <TextView android:id="@+id/tvUserName"/>
        <TextView android:id="@+id/tvRequestDate"/>
        <TextView android:id="@+id/tvReason"/>
        <Button android:text="Duyệt"/>
        <Button android:text="Từ chối"/>
    </LinearLayout>
</CardView>
```

**API Endpoints:**
- `GET /api/admin/upgrade-requests`
- `PUT /api/admin/upgrade-requests/{id}/approve`
- `PUT /api/admin/upgrade-requests/{id}/reject`

---

### 💰 NHÓM 13: VÍ ĐIỆN TỬ & NẠP TIỀN

#### 13.1 Xem số dư ví
**Mô tả:** Hiển thị số dư hiện tại trong ví

**UI Components (XML):**
- `fragment_wallet.xml`
- `CardView`: Hiển thị số dư lớn
- `RecyclerView`: Lịch sử giao dịch gần đây
- `Button`: Nạp tiền, Rút tiền

**Layout Structure:**
```xml
<LinearLayout orientation="vertical">
    <CardView>
        <LinearLayout>
            <TextView android:text="Số dư ví"/>
            <TextView android:id="@+id/tvBalance" android:textSize="32sp"/>
            <TextView android:text="VNĐ"/>
        </LinearLayout>
    </CardView>
    
    <LinearLayout orientation="horizontal">
        <Button android:id="@+id/btnDeposit" android:text="Nạp tiền"/>
        <Button android:id="@+id/btnWithdraw" android:text="Rút tiền"/>
    </LinearLayout>
    
    <TextView android:text="Giao dịch gần đây"/>
    <RecyclerView android:id="@+id/rvTransactions"/>
</LinearLayout>
```

**API Endpoint:** `GET /api/wallet/balance`

---

#### 13.2 Nạp tiền vào ví
**Mô tả:** Nạp tiền qua nhiều phương thức

**UI Components (XML):**
- `activity_deposit.xml`
- `EditText`: Số tiền muốn nạp
- `ChipGroup`: Số tiền gợi ý (100k, 200k, 500k, 1M)
- `RadioGroup`: Phương thức thanh toán

**Phương thức thanh toán:**
- Chuyển khoản ngân hàng
- Momo
- ZaloPay
- VNPay
- Thẻ tín dụng/ghi nợ

**Dependencies:**
```gradle
implementation 'com.momo.sdk:payment:1.0.0'
implementation 'vn.zalopay.sdk:payment:1.0.0'
```

**API Endpoint:** `POST /api/wallet/deposit`

---

#### 13.3 Rút tiền từ ví
**Mô tả:** Rút tiền về tài khoản ngân hàng

**UI Components (XML):**
- `activity_withdraw.xml`
- `EditText`: Số tiền rút
- `Spinner`: Chọn ngân hàng
- `EditText`: Số tài khoản, Tên chủ tài khoản
- `Button`: Xác nhận rút tiền

**Validation:**
- Số tiền rút <= Số dư
- Tối thiểu 50,000 VNĐ
- Xác nhận OTP trước khi rút

**API Endpoint:** `POST /api/wallet/withdraw`

---

#### 13.4 Lịch sử giao dịch ví
**Mô tả:** Xem tất cả giao dịch ví

**UI Components (XML):**
- `activity_transaction_history.xml`
- `TabLayout`: Tất cả / Nạp tiền / Rút tiền / Thanh toán
- `RecyclerView` + `item_transaction.xml`
- Filter theo thời gian

**Transaction Types:**
```java
public enum TransactionType {
    DEPOSIT,        // Nạp tiền
    WITHDRAW,       // Rút tiền
    PAYMENT,        // Thanh toán đấu giá
    REFUND,         // Hoàn tiền
    RECEIVE         // Nhận tiền từ người mua
}
```

**API Endpoint:** `GET /api/wallet/transactions`

---

#### 13.5 Thanh toán đấu giá qua ví
**Mô tả:** Sử dụng số dư ví để thanh toán

**UI Components (XML):**
- `dialog_wallet_payment.xml`
- Hiển thị số dư, số tiền cần thanh toán
- `Button`: Xác nhận thanh toán

**Flow:**
1. Thắng đấu giá → Chọn thanh toán qua ví
2. Kiểm tra số dư đủ
3. Trừ tiền từ ví → Chuyển cho seller
4. Cập nhật trạng thái đơn hàng

**API Endpoint:** `POST /api/wallet/pay/{orderId}`

---

### 📍 NHÓM 14: QUẢN LÝ ĐỊA CHỈ GIAO HÀNG

#### 14.1 Danh sách địa chỉ
**Mô tả:** Quản lý nhiều địa chỉ giao hàng

**UI Components (XML):**
- `fragment_addresses.xml`
- `RecyclerView` + `item_address.xml`
- `FloatingActionButton`: Thêm địa chỉ mới
- Badge "Mặc định" cho địa chỉ chính

**Item Address Layout:**
```xml
<CardView>
    <LinearLayout>
        <TextView android:id="@+id/tvRecipientName"/>
        <TextView android:id="@+id/tvPhone"/>
        <TextView android:id="@+id/tvAddress"/>
        <Chip android:id="@+id/chipDefault" android:text="Mặc định"/>
        <ImageButton android:id="@+id/btnEdit"/>
        <ImageButton android:id="@+id/btnDelete"/>
    </LinearLayout>
</CardView>
```

**API Endpoint:** `GET /api/addresses`

---

#### 14.2 Thêm/Sửa địa chỉ
**Mô tả:** Form nhập địa chỉ chi tiết

**UI Components (XML):**
- `activity_edit_address.xml`
- `EditText`: Tên người nhận, Số điện thoại
- `Spinner`: Tỉnh/Thành phố, Quận/Huyện, Phường/Xã
- `EditText`: Địa chỉ chi tiết
- `SwitchCompat`: Đặt làm mặc định

**Address API:**
- Tích hợp API địa chỉ Việt Nam (provinces.open-api.vn)

**API Endpoints:**
- `POST /api/addresses`
- `PUT /api/addresses/{id}`

---

#### 14.3 Chọn địa chỉ khi thanh toán
**Mô tả:** Bottom sheet chọn địa chỉ giao hàng

**UI Components (XML):**
- `BottomSheetDialogFragment`
- `RecyclerView`: Danh sách địa chỉ
- `Button`: Thêm địa chỉ mới

---

#### 14.4 Xác định vị trí GPS
**Mô tả:** Tự động điền địa chỉ từ GPS

**Implementation:**
- `FusedLocationProviderClient`
- Geocoder để chuyển tọa độ → địa chỉ

**Permissions:**
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
```

---

### 📊 NHÓM 15: BÁO CÁO & THỐNG KÊ (USER)

#### 15.1 Thống kê đấu giá cá nhân
**Mô tả:** Dashboard thống kê hoạt động đấu giá

**UI Components (XML):**
- `fragment_my_statistics.xml`
- Cards: Tổng đấu giá, Tỷ lệ thắng, Tổng chi tiêu
- `PieChart`: Phân bổ danh mục đấu giá
- `LineChart`: Hoạt động theo thời gian

**Statistics Model:**
```java
public class UserStatistics {
    private int totalBids;
    private int wonAuctions;
    private int lostAuctions;
    private double winRate;
    private double totalSpent;
    private double totalEarned; // Nếu là seller
    private Map<String, Integer> categoryDistribution;
}
```

**API Endpoint:** `GET /api/users/statistics`

---

#### 15.2 Thống kê bán hàng (Seller)
**Mô tả:** Dashboard cho seller

**UI Components (XML):**
- `fragment_seller_statistics.xml`
- Cards: Tổng sản phẩm đăng, Đã bán, Doanh thu
- `BarChart`: Doanh thu theo tháng
- `RecyclerView`: Sản phẩm bán chạy nhất

**API Endpoint:** `GET /api/sellers/statistics`

---

#### 15.3 Xuất báo cáo
**Mô tả:** Export báo cáo ra PDF/Excel

**Features:**
- Xuất lịch sử giao dịch
- Xuất báo cáo thuế (cho seller)
- Gửi qua email

**Dependencies:**
```gradle
implementation 'com.itextpdf:itext7-core:7.2.5'
implementation 'org.apache.poi:poi-ooxml:5.2.3'
```

---

#### 15.4 So sánh giá sản phẩm
**Mô tả:** Biểu đồ so sánh giá theo thời gian

**UI Components (XML):**
- `activity_price_comparison.xml`
- `LineChart`: Giá trung bình theo danh mục
- `DateRangePicker`: Chọn khoảng thời gian

---

### 🎁 NHÓM 16: HỆ THỐNG KHUYẾN MÃI & VOUCHER

#### 16.1 Danh sách voucher của tôi
**Mô tả:** Xem voucher đang có

**UI Components (XML):**
- `fragment_my_vouchers.xml`
- `TabLayout`: Còn hạn / Đã sử dụng / Hết hạn
- `RecyclerView` + `item_voucher.xml`

**Voucher Item Layout:**
```xml
<CardView android:background="@drawable/voucher_background">
    <LinearLayout>
        <TextView android:id="@+id/tvDiscount"/> <!-- -10% hoặc -50K -->
        <TextView android:id="@+id/tvVoucherName"/>
        <TextView android:id="@+id/tvCondition"/> <!-- Đơn tối thiểu 500K -->
        <TextView android:id="@+id/tvExpiry"/>
        <Button android:text="Sử dụng"/>
    </LinearLayout>
</CardView>
```

**API Endpoint:** `GET /api/vouchers/my`

---

#### 16.2 Nhập mã voucher
**Mô tả:** Nhập mã để nhận voucher

**UI Components (XML):**
- `dialog_enter_voucher.xml`
- `EditText`: Mã voucher
- `Button`: Áp dụng

**API Endpoint:** `POST /api/vouchers/redeem`

---

#### 16.3 Áp dụng voucher khi thanh toán
**Mô tả:** Chọn voucher khi checkout

**UI Components (XML):**
- `BottomSheetDialogFragment`
- Danh sách voucher áp dụng được
- Tự động tính giảm giá

**Voucher Types:**
```java
public enum VoucherType {
    PERCENTAGE,     // Giảm theo %
    FIXED_AMOUNT,   // Giảm số tiền cố định
    FREE_SHIPPING   // Miễn phí vận chuyển
}
```

---

#### 16.4 Chương trình điểm thưởng
**Mô tả:** Tích điểm khi giao dịch

**UI Components (XML):**
- `fragment_reward_points.xml`
- `TextView`: Tổng điểm
- `RecyclerView`: Lịch sử tích điểm
- `RecyclerView`: Đổi điểm lấy voucher

**Point Rules:**
- Mua hàng: 1% giá trị = điểm
- Bán hàng: 0.5% giá trị = điểm
- 100 điểm = 1,000 VNĐ voucher

**API Endpoints:**
- `GET /api/rewards/points`
- `POST /api/rewards/exchange`

---

#### 16.5 Flash Sale & Ưu đãi đặc biệt
**Mô tả:** Hiển thị các chương trình khuyến mãi

**UI Components (XML):**
- `fragment_promotions.xml`
- `ViewPager2`: Banner khuyến mãi
- `RecyclerView`: Sản phẩm Flash Sale
- Countdown timer

---

### 🚨 NHÓM 17: BÁO CÁO VI PHẠM & HỖ TRỢ

#### 17.1 Báo cáo sản phẩm vi phạm
**Mô tả:** Report sản phẩm có vấn đề

**UI Components (XML):**
- `dialog_report_product.xml`
- `RadioGroup`: Lý do báo cáo
  - Sản phẩm giả/nhái
  - Mô tả sai sự thật
  - Hình ảnh không phù hợp
  - Giá bất thường
  - Khác
- `EditText`: Mô tả chi tiết
- `Button`: Gửi báo cáo

**API Endpoint:** `POST /api/reports/product/{productId}`

---

#### 17.2 Báo cáo người dùng
**Mô tả:** Report user vi phạm

**UI Components (XML):**
- `dialog_report_user.xml`
- `RadioGroup`: Lý do
  - Gian lận
  - Ngôn từ không phù hợp
  - Spam
  - Không giao hàng
  - Khác
- `EditText`: Mô tả

**API Endpoint:** `POST /api/reports/user/{userId}`

---

#### 17.3 Trung tâm hỗ trợ
**Mô tả:** FAQ và liên hệ hỗ trợ

**UI Components (XML):**
- `fragment_help_center.xml`
- `SearchView`: Tìm kiếm FAQ
- `ExpandableListView`: Danh mục FAQ
- `Button`: Chat với hỗ trợ viên
- `Button`: Gọi hotline

**FAQ Categories:**
- Hướng dẫn đấu giá
- Thanh toán & Hoàn tiền
- Vận chuyển
- Tài khoản
- Khiếu nại

---

#### 17.4 Gửi ticket hỗ trợ
**Mô tả:** Tạo yêu cầu hỗ trợ

**UI Components (XML):**
- `activity_create_ticket.xml`
- `Spinner`: Loại vấn đề
- `EditText`: Tiêu đề, Mô tả chi tiết
- `RecyclerView`: Đính kèm ảnh
- `Button`: Gửi

**Ticket Tracking:**
- Xem trạng thái ticket
- Nhận thông báo khi có phản hồi

**API Endpoints:**
- `POST /api/support/tickets`
- `GET /api/support/tickets`
- `GET /api/support/tickets/{id}`

---

### 🌐 NHÓM 18: ĐA NGÔN NGỮ & ACCESSIBILITY

#### 18.1 Chuyển đổi ngôn ngữ
**Mô tả:** Hỗ trợ nhiều ngôn ngữ

**UI Components (XML):**
- `fragment_language_settings.xml`
- `RecyclerView` + `item_language.xml`
- Radio button chọn ngôn ngữ

**Supported Languages:**
- 🇻🇳 Tiếng Việt (mặc định)
- 🇺🇸 English
- 🇯🇵 日本語 (Japanese)
- 🇨🇳 中文 (Chinese)

**Implementation:**
```java
public class LocaleHelper {
    public static void setLocale(Context context, String language) {
        Locale locale = new Locale(language);
        Locale.setDefault(locale);
        Configuration config = context.getResources().getConfiguration();
        config.setLocale(locale);
        context.createConfigurationContext(config);
    }
}
```

**Resources:**
```
res/
├── values/strings.xml          (Vietnamese - default)
├── values-en/strings.xml       (English)
├── values-ja/strings.xml       (Japanese)
└── values-zh/strings.xml       (Chinese)
```

---

#### 18.2 Dark Mode
**Mô tả:** Chế độ tối cho app

**UI Components (XML):**
- `SwitchCompat` trong Settings
- Options: Light / Dark / System default

**Implementation:**
```java
public class ThemeManager {
    public static void setTheme(int mode) {
        AppCompatDelegate.setDefaultNightMode(mode);
        // MODE_NIGHT_NO, MODE_NIGHT_YES, MODE_NIGHT_FOLLOW_SYSTEM
    }
}
```

**Resources:**
```
res/
├── values/colors.xml
├── values/themes.xml
├── values-night/colors.xml
└── values-night/themes.xml
```

---

#### 18.3 Điều chỉnh cỡ chữ
**Mô tả:** Thay đổi kích thước font

**UI Components (XML):**
- `SeekBar`: Điều chỉnh từ 80% - 150%
- Preview text

**Implementation:**
```java
public class FontScaleManager {
    public static void setFontScale(Activity activity, float scale) {
        Configuration config = activity.getResources().getConfiguration();
        config.fontScale = scale;
        activity.getResources().updateConfiguration(config, 
            activity.getResources().getDisplayMetrics());
        activity.recreate();
    }
}
```

---

#### 18.4 Hỗ trợ TalkBack
**Mô tả:** Accessibility cho người khiếm thị

**Implementation:**
- `contentDescription` cho tất cả ImageView
- `labelFor` cho EditText
- Focus navigation hợp lý
- Announce changes với `announceForAccessibility()`

**Best Practices:**
```xml
<ImageButton
    android:contentDescription="@string/btn_place_bid_desc"
    android:importantForAccessibility="yes"/>

<EditText
    android:hint="@string/hint_bid_amount"
    android:labelFor="@+id/tvBidLabel"/>
```

---

### 📴 NHÓM 19: OFFLINE MODE & ĐỒNG BỘ

#### 19.1 Cache dữ liệu offline
**Mô tả:** Xem sản phẩm khi không có mạng

**Implementation:**
- Room Database lưu cache
- NetworkBoundResource pattern
- Hiển thị dữ liệu cũ với indicator "Offline"

**Cached Data:**
- Danh mục sản phẩm
- Sản phẩm đã xem
- Watchlist
- Thông tin profile

**Strategy:**
```java
public abstract class NetworkBoundResource<ResultType, RequestType> {
    // 1. Hiển thị data từ DB
    // 2. Fetch từ network
    // 3. Lưu vào DB
    // 4. Hiển thị data mới
}
```

---

#### 19.2 Queue hành động offline
**Mô tả:** Lưu hành động chờ sync

**Features:**
- Thêm vào watchlist khi offline → sync khi online
- Xóa thông báo khi offline → sync khi online

**Implementation:**
```java
@Entity(tableName = "pending_actions")
public class PendingAction {
    @PrimaryKey(autoGenerate = true)
    private int id;
    private String actionType; // ADD_WATCHLIST, REMOVE_WATCHLIST
    private String payload; // JSON data
    private long createdAt;
}
```

**WorkManager:**
```java
public class SyncWorker extends Worker {
    @Override
    public Result doWork() {
        // Process pending actions
        // Sync with server
        return Result.success();
    }
}
```

---

#### 19.3 Kiểm tra kết nối mạng
**Mô tả:** Theo dõi trạng thái mạng

**UI Components (XML):**
- Banner/Snackbar thông báo mất kết nối
- Auto-hide khi có mạng lại

**Implementation:**
```java
public class NetworkMonitor {
    private ConnectivityManager.NetworkCallback networkCallback;
    
    public LiveData<Boolean> isConnected() {
        // Monitor network state
    }
}
```

---

#### 19.4 Đồng bộ khi có mạng
**Mô tả:** Tự động sync dữ liệu

**Features:**
- Sync watchlist
- Sync notifications read status
- Sync pending bids (nếu còn hạn)
- Pull latest data

**WorkManager Constraints:**
```java
Constraints constraints = new Constraints.Builder()
    .setRequiredNetworkType(NetworkType.CONNECTED)
    .build();

OneTimeWorkRequest syncWork = new OneTimeWorkRequest.Builder(SyncWorker.class)
    .setConstraints(constraints)
    .build();
```

---

### 📲 NHÓM 20: WIDGET & QUICK ACTIONS

#### 20.1 Home Screen Widget
**Mô tả:** Widget hiển thị sản phẩm đang theo dõi

**Widget Types:**
- **Small (2x1):** Countdown sản phẩm sắp kết thúc
- **Medium (4x2):** Danh sách 3 sản phẩm watchlist
- **Large (4x4):** Sản phẩm + giá + countdown

**Implementation:**
```xml
<!-- res/xml/watchlist_widget_info.xml -->
<appwidget-provider
    android:minWidth="180dp"
    android:minHeight="110dp"
    android:updatePeriodMillis="1800000"
    android:initialLayout="@layout/widget_watchlist"
    android:resizeMode="horizontal|vertical"/>
```

**Widget Provider:**
```java
public class WatchlistWidgetProvider extends AppWidgetProvider {
    @Override
    public void onUpdate(Context context, AppWidgetManager manager, int[] ids) {
        // Update widget content
    }
}
```

---

#### 20.2 App Shortcuts
**Mô tả:** Quick actions từ app icon

**Static Shortcuts (res/xml/shortcuts.xml):**
```xml
<shortcuts>
    <shortcut android:shortcutId="search"
        android:icon="@drawable/ic_search"
        android:shortcutShortLabel="@string/shortcut_search">
        <intent android:action="android.intent.action.VIEW"
            android:targetPackage="com.example.auctionhub"
            android:targetClass=".ui.search.SearchActivity"/>
    </shortcut>
    
    <shortcut android:shortcutId="watchlist"
        android:icon="@drawable/ic_favorite"
        android:shortcutShortLabel="@string/shortcut_watchlist">
        <!-- Intent -->
    </shortcut>
    
    <shortcut android:shortcutId="sell"
        android:icon="@drawable/ic_add"
        android:shortcutShortLabel="@string/shortcut_sell">
        <!-- Intent -->
    </shortcut>
</shortcuts>
```

**Dynamic Shortcuts:**
- Sản phẩm đã xem gần đây
- Sản phẩm đang đấu giá

---

#### 20.3 Quick Tile (Quick Settings)
**Mô tả:** Tile trong Quick Settings panel

**Implementation:**
```java
public class AuctionTileService extends TileService {
    @Override
    public void onClick() {
        // Open watchlist with ending soon products
    }
    
    @Override
    public void onStartListening() {
        // Update tile state
        Tile tile = getQsTile();
        tile.setLabel("3 sắp kết thúc");
        tile.updateTile();
    }
}
```

---

#### 20.4 Notification Actions
**Mô tả:** Quick actions trong notification

**Outbid Notification:**
```java
NotificationCompat.Builder builder = new NotificationCompat.Builder(context, CHANNEL_ID)
    .setContentTitle("Bạn đã bị vượt giá!")
    .setContentText("iPhone 15 - Giá mới: 15,000,000đ")
    .addAction(R.drawable.ic_bid, "Đặt giá cao hơn", bidPendingIntent)
    .addAction(R.drawable.ic_view, "Xem chi tiết", detailPendingIntent);
```

**Auction Ending Notification:**
- Button: "Đặt giá ngay"
- Button: "Bỏ qua"

---

## 🏗️ KIẾN TRÚC KỸ THUẬT

### Architecture Pattern: MVVM

```
app/
├── data/
│   ├── local/
│   │   ├── database/         # Room Database
│   │   ├── dao/              # Data Access Objects
│   │   └── preferences/      # SharedPreferences
│   ├── remote/
│   │   ├── api/              # Retrofit API interfaces
│   │   ├── dto/              # Data Transfer Objects
│   │   └── interceptors/     # OkHttp interceptors
│   └── repository/           # Repository implementations
│
├── domain/
│   ├── model/                # Domain models
│   ├── repository/           # Repository interfaces
│   └── usecase/              # Use cases
│
├── presentation/
│   ├── ui/
│   │   ├── auth/             # Login, Register, OTP
│   │   ├── home/             # Home, Categories
│   │   ├── product/          # List, Detail, Create
│   │   ├── bid/              # Bidding screens
│   │   ├── profile/          # User profile
│   │   ├── chat/             # Chat screens
│   │   ├── order/            # Order management
│   │   ├── notification/     # Notifications
│   │   └── admin/            # Admin screens
│   ├── viewmodel/            # ViewModels
│   ├── adapter/              # RecyclerView Adapters
│   └── custom/               # Custom Views
│
├── di/                       # Dependency Injection (Hilt)
├── utils/                    # Utility classes
└── AuctionApplication.java   # Application class
```

### Dependencies (build.gradle)

```gradle
plugins {
    id 'com.android.application'
    id 'com.google.gms.google-services'
    id 'com.google.dagger.hilt.android'
}

android {
    compileSdk 34
    
    defaultConfig {
        applicationId "com.example.auctionhub"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }
    
    buildFeatures {
        viewBinding true
        dataBinding true
    }
}

dependencies {
    // AndroidX
    implementation 'androidx.core:core-ktx:1.12.0'
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.11.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    implementation 'androidx.lifecycle:lifecycle-viewmodel:2.7.0'
    implementation 'androidx.lifecycle:lifecycle-livedata:2.7.0'
    implementation 'androidx.navigation:navigation-fragment:2.7.6'
    implementation 'androidx.navigation:navigation-ui:2.7.6'
    implementation 'androidx.paging:paging-runtime:3.2.1'
    
    // Networking
    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
    implementation 'com.squareup.okhttp3:logging-interceptor:4.12.0'
    
    // Image Loading
    implementation 'com.github.bumptech.glide:glide:4.16.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.16.0'
    
    // Database
    implementation 'androidx.room:room-runtime:2.6.1'
    annotationProcessor 'androidx.room:room-compiler:2.6.1'
    
    // Firebase
    implementation platform('com.google.firebase:firebase-bom:32.7.0')
    implementation 'com.google.firebase:firebase-auth'
    implementation 'com.google.firebase:firebase-messaging'
    implementation 'com.google.firebase:firebase-database'
    implementation 'com.google.firebase:firebase-storage'
    
    // Dependency Injection
    implementation 'com.google.dagger:hilt-android:2.48'
    annotationProcessor 'com.google.dagger:hilt-compiler:2.48'
    
    // Social Login
    implementation 'com.google.android.gms:play-services-auth:20.7.0'
    implementation 'com.facebook.android:facebook-login:16.2.0'
    
    // UI Components
    implementation 'de.hdodenhof:circleimageview:3.1.0'
    implementation 'com.github.chrisbanes:PhotoView:2.3.0'
    implementation 'com.github.PhilJay:MPAndroidChart:v3.1.0'
    
    // Security
    implementation 'androidx.security:security-crypto:1.1.0-alpha06'
    implementation 'org.mindrot:jbcrypt:0.4'
    
    // AI Integration
    implementation 'com.google.ai.client.generativeai:generativeai:0.1.2'
}
```

---

## 📱 NAVIGATION STRUCTURE

### Bottom Navigation
```
Home | Search | Sell | Notifications | Profile
```

### Navigation Graph
```
nav_graph.xml
├── auth_nav_graph.xml
│   ├── loginFragment
│   ├── registerFragment
│   └── otpFragment
├── home_nav_graph.xml
│   ├── homeFragment
│   ├── categoryListFragment
│   └── productListFragment
├── product_nav_graph.xml
│   ├── productDetailFragment
│   ├── bidHistoryFragment
│   └── createProductFragment
├── profile_nav_graph.xml
│   ├── profileFragment
│   ├── editProfileFragment
│   ├── watchlistFragment
│   └── myBidsFragment
└── admin_nav_graph.xml
    ├── adminDashboardFragment
    ├── manageCategoriesFragment
    └── manageUsersFragment
```

---

## 📊 DATABASE SCHEMA (Room)

### Entities

```java
@Entity(tableName = "users")
public class UserEntity {
    @PrimaryKey
    private int id;
    private String email;
    private String name;
    private String avatar;
    private String role; // GUEST, BIDDER, SELLER, ADMIN
    private double ratingScore;
    private int totalRatings;
}

@Entity(tableName = "products")
public class ProductEntity {
    @PrimaryKey
    private int id;
    private String name;
    private int categoryId;
    private double startingPrice;
    private double currentPrice;
    private double buyNowPrice;
    private double bidStep;
    private String description;
    private long startTime;
    private long endTime;
    private int sellerId;
    private boolean autoExtend;
    private String status;
}

@Entity(tableName = "categories")
public class CategoryEntity {
    @PrimaryKey
    private int id;
    private String name;
    private Integer parentId;
}

@Entity(tableName = "bids")
public class BidEntity {
    @PrimaryKey
    private int id;
    private int productId;
    private int bidderId;
    private double amount;
    private long timestamp;
    private boolean isAutoBid;
}

@Entity(tableName = "watchlist")
public class WatchlistEntity {
    @PrimaryKey(autoGenerate = true)
    private int id;
    private int userId;
    private int productId;
    private long addedAt;
}

@Entity(tableName = "notifications")
public class NotificationEntity {
    @PrimaryKey
    private int id;
    private String title;
    private String message;
    private String type;
    private long timestamp;
    private boolean isRead;
}
```

---

## 🔄 API ENDPOINTS SUMMARY

### Authentication
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /api/auth/register | Đăng ký |
| POST | /api/auth/verify-otp | Xác nhận OTP |
| POST | /api/auth/login | Đăng nhập |
| POST | /api/auth/social-login | Đăng nhập social |
| POST | /api/auth/forgot-password | Quên mật khẩu |
| POST | /api/auth/reset-password | Reset mật khẩu |
| POST | /api/auth/logout | Đăng xuất |

### Products
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/products | Danh sách sản phẩm |
| GET | /api/products/{id} | Chi tiết sản phẩm |
| POST | /api/products | Tạo sản phẩm |
| PUT | /api/products/{id} | Cập nhật sản phẩm |
| GET | /api/products/search | Tìm kiếm |
| GET | /api/products/{id}/bid-history | Lịch sử đấu giá |
| POST | /api/products/{id}/bids | Đặt giá |
| POST | /api/products/{id}/auto-bid | Đấu giá tự động |

### Users
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/users/profile | Xem profile |
| PUT | /api/users/profile | Cập nhật profile |
| PUT | /api/users/change-password | Đổi mật khẩu |
| GET | /api/users/ratings | Xem đánh giá |
| POST | /api/users/upgrade-request | Yêu cầu nâng cấp |

### Watchlist
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/watchlist | Danh sách yêu thích |
| POST | /api/watchlist/{productId} | Thêm vào watchlist |
| DELETE | /api/watchlist/{productId} | Xóa khỏi watchlist |

---

## 📅 KẾ HOẠCH PHÁT TRIỂN (Gợi ý)

### Sprint 1 (Tuần 1-2): Foundation
- [ ] Setup project structure
- [ ] Authentication (Login, Register, OTP)
- [ ] Basic navigation
- [ ] API integration setup

### Sprint 2 (Tuần 3-4): Core Features
- [ ] Home screen
- [ ] Category listing
- [ ] Product listing với pagination
- [ ] Product detail

### Sprint 3 (Tuần 5-6): Bidding System
- [ ] Place bid functionality
- [ ] Auto-bid
- [ ] Bid history
- [ ] Watchlist

### Sprint 4 (Tuần 7-8): Seller Features
- [ ] Create product
- [ ] Manage products
- [ ] Q&A system
- [ ] Reject bidder

### Sprint 5 (Tuần 9-10): Communication
- [ ] Push notifications
- [ ] Chat system
- [ ] Email integration

### Sprint 6 (Tuần 11-12): AI & Admin
- [ ] AI price suggestion
- [ ] AI chatbot
- [ ] Admin dashboard
- [ ] User management

### Sprint 7 (Tuần 13-14): Polish
- [ ] Order completion flow
- [ ] Rating system
- [ ] Bug fixes
- [ ] UI/UX improvements
- [ ] Testing

---

## 📝 GIT COMMIT GUIDELINES

Để đạt yêu cầu **30 commits trong 15 ngày**, follow convention:

```
feat: Add login functionality
fix: Fix OTP verification bug
ui: Update product detail layout
refactor: Restructure API service
docs: Update README
test: Add unit tests for BidValidator
```

**Commit Schedule:**
- 2-3 commits/ngày
- Mỗi feature hoàn thành = 1 commit
- Bug fixes riêng biệt

---

## ✅ CHECKLIST HOÀN THÀNH

- [ ] 20 nhóm tính năng (5 thành viên × 4)
- [ ] Tính năng AI (Nhóm 11)
- [ ] Git repository với 30+ commits
- [ ] Commits phân bố trong 15+ ngày
- [ ] Documentation đầy đủ
- [ ] Demo video/presentation

---

## 👥 PHÂN CÔNG THÀNH VIÊN (Gợi ý)

### Thành viên 1 (TV1)
| Nhóm | Tên | Số tính năng |
|------|-----|--------------|
| 1 | Xác thực & Bảo mật | 6 |
| 11 | Tính năng AI | 4 |
| 16 | Hệ thống khuyến mãi & Voucher | 5 |
| **Tổng** | | **15** |

### Thành viên 2 (TV2)
| Nhóm | Tên | Số tính năng |
|------|-----|--------------|
| 3 | Duyệt & Tìm kiếm sản phẩm | 6 |
| 12 | Quản trị (Admin) | 5 |
| 17 | Báo cáo vi phạm & Hỗ trợ | 4 |
| **Tổng** | | **15** |

### Thành viên 3 (TV3)
| Nhóm | Tên | Số tính năng |
|------|-----|--------------|
| 5 | Đấu giá (Bidding) | 6 |
| 13 | Ví điện tử & Nạp tiền | 5 |
| 18 | Đa ngôn ngữ & Accessibility | 4 |
| **Tổng** | | **15** |

### Thành viên 4 (TV4)
| Nhóm | Tên | Số tính năng |
|------|-----|--------------|
| 7 | Hệ thống thông báo | 4 |
| 8 | Chat & Trao đổi | 4 |
| 14 | Quản lý địa chỉ giao hàng | 4 |
| 19 | Offline Mode & Đồng bộ | 4 |
| **Tổng** | | **16** |

### Thành viên 5 (TV5)
| Nhóm | Tên | Số tính năng |
|------|-----|--------------|
| 9 | Thanh toán & Giao dịch | 5 |
| 10 | Đánh giá & Phản hồi | 4 |
| 15 | Báo cáo & Thống kê (User) | 4 |
| 20 | Widget & Quick Actions | 4 |
| **Tổng** | | **17** |

### Công việc chung (Tất cả thành viên)
| Nhóm | Tên | Phân công |
|------|-----|-----------|
| 2 | Quản lý hồ sơ người dùng | Chia đều |
| 4 | Chi tiết sản phẩm | Chia đều |
| 6 | Quản lý sản phẩm (Seller) | Chia đều |

---

## 📚 TÀI LIỆU THAM KHẢO

1. [Android Developer Documentation](https://developer.android.com/docs)
2. [Material Design Guidelines](https://material.io/design)
3. [Firebase Documentation](https://firebase.google.com/docs)
4. [Gemini AI API](https://ai.google.dev/docs)
5. [MVVM Architecture](https://developer.android.com/topic/architecture)

---

**Tài liệu được tạo:** 30/01/2026  
**Version:** 1.0  
**Author:** AuctionHub Development Team
