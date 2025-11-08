# Module 20: Firebase Setup & Build Instructions
## Gaming Gear & Smart Home Devices Store

---

## 📋 Firebase Collections Used

Your app uses these Firestore collections:

1. **`users`** - User accounts with roles
2. **`products`** - Product catalog
3. **`userCarts`** - User shopping carts
4. **`orders`** - Customer orders
5. **`notifications`** - Order status notifications
6. **`chats`** - Chat rooms (parent collection)
7. **`chats/{userId}/messages`** - Individual chat messages (subcollection)

---

## 🔥 Required Firestore Indexes

You need to create **3 indexes** for your app to work properly:

### Index 1: Orders - User Orders Query (REQUIRED)
**Collection:** `orders`
- Field 1: `userId` → **Ascending**
- Field 2: `createdAt` → **Descending**

**Why:** Used in OrderHistoryScreen to show user's orders sorted by date.

**How to Create:**
1. Run your app and go to "My Orders"
2. You'll see an error with a blue link
3. Click the link → Firebase Console opens with fields pre-filled
4. Click "Create Index"
5. Wait 2-5 minutes

### Index 2: Notifications - Unread Notifications (REQUIRED)
**Collection:** `notifications`
- Field 1: `userId` → **Ascending**
- Field 2: `isRead` → **Ascending**
- Field 3: `createdAt` → **Descending**

**Why:** Used in NotificationIcon to show unread notification badge.

**How to Create:**
1. Run your app and tap the notification bell icon
2. You'll see an error with a blue link
3. Click the link → Create index
4. Wait 2-5 minutes

### Index 3: Chats - Admin Chat List (REQUIRED)
**Collection:** `chats`
- Field 1: `lastMessageAt` → **Descending**

**Why:** Used in AdminChatListScreen to show chats sorted by most recent.

**How to Create:**
1. Run your app as admin → Admin Panel → View User Chats
2. You'll see an error with a blue link
3. Click the link → Create index
4. Wait 2-5 minutes

### Index 4: Messages Subcollection (Usually Auto-Created)
**Collection:** `chats/{chatRoomId}/messages` (Subcollection)
- Field 1: `createdAt` → **Ascending** (or Descending)

**Why:** Used in ChatScreen to display messages in order.

**How to Create:**
1. **Firestore usually auto-creates this!** Just run your app and open a chat.
2. If you see an error message, click the **blue link** in the error
3. It will open Firebase Console with all fields pre-filled
4. Click **"Create Index"**
5. Wait 2-5 minutes

**Important Note:** 
- Firestore **automatically creates** single-field indexes for subcollections in most cases
- You **do NOT** need to use "Add exemption" - that's for **disabling** auto-indexing, not creating indexes
- If you see an error, just click the error link to create it normally (same as other indexes)

---

## 🔒 Updated Firestore Security Rules

Copy these rules to Firebase Console → Firestore Database → Rules:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper function to check if user is admin
    function isAdmin() {
      return request.auth != null && 
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Helper function to check if user is authenticated
    function isAuthenticated() {
      return request.auth != null;
    }
    
    // Users Collection
    match /users/{userId} {
      allow read: if isAuthenticated() && (request.auth.uid == userId || isAdmin());
      allow create: if isAuthenticated() && request.auth.uid == userId;
      allow update: if isAuthenticated() && request.auth.uid == userId;
      allow delete: if isAuthenticated() && isAdmin();
    }
    
    // Products Collection
    match /products/{productId} {
      allow read: if isAuthenticated();
      allow create: if isAuthenticated() && isAdmin();
      allow update: if isAuthenticated() && isAdmin();
      allow delete: if isAuthenticated() && isAdmin();
    }
    
    // User Carts Collection
    match /userCarts/{userId} {
      allow read, write: if isAuthenticated() && request.auth.uid == userId;
    }
    
    // Orders Collection
    match /orders/{orderId} {
      allow read: if isAuthenticated() && 
        (resource.data.userId == request.auth.uid || isAdmin());
      allow create: if isAuthenticated() && 
        request.resource.data.userId == request.auth.uid;
      allow update: if isAuthenticated() && isAdmin();
      allow delete: if isAuthenticated() && isAdmin();
    }
    
    // Notifications Collection
    match /notifications/{notificationId} {
      allow read: if isAuthenticated() && 
        resource.data.userId == request.auth.uid;
      allow create: if isAuthenticated() && isAdmin();
      allow update: if isAuthenticated() && 
        resource.data.userId == request.auth.uid;
      allow delete: if isAuthenticated() && 
        resource.data.userId == request.auth.uid;
    }
    
    // Chats Collection (Parent)
    match /chats/{chatRoomId} {
      allow read: if isAuthenticated() && 
        (chatRoomId == request.auth.uid || isAdmin());
      allow write: if isAuthenticated() && 
        (chatRoomId == request.auth.uid || isAdmin());
    }
    
    // Messages Subcollection
    match /chats/{chatRoomId}/messages/{messageId} {
      allow read: if isAuthenticated() && 
        (chatRoomId == request.auth.uid || isAdmin());
      allow create: if isAuthenticated() && 
        (chatRoomId == request.auth.uid || isAdmin());
      allow update, delete: if isAuthenticated() && isAdmin();
    }
  }
}
```

---

## 📱 Building the Release APK

### Step 1: Clean Build (Optional but Recommended)
```bash
flutter clean
```

### Step 2: Build Release APK
```bash
flutter build apk --release
```

### Step 3: Find Your APK
After the build completes, navigate to:
```
build/app/outputs/flutter-apk/app-release.apk
```

### Step 4: Install on Android Phone
1. Transfer `app-release.apk` to your Android phone
2. On your phone, go to Settings → Security
3. Enable "Unknown sources" or "Install unknown apps"
4. Open the APK file and install

---

## ✅ Complete Firebase Setup Checklist

### Authentication
- [ ] Firebase Console → Authentication → Enable Email/Password

### Firestore Database
- [ ] Firebase Console → Firestore Database → Create database (test mode is fine for development)

### User Role Setup
- [ ] Sign up with your email in the app
- [ ] Go to Firestore → `users` collection → Your user document
- [ ] Change `role` field from `"user"` to `"admin"`

### Firestore Indexes
- [ ] Index 1: `orders` - userId (Asc) + createdAt (Desc)
- [ ] Index 2: `notifications` - userId (Asc) + isRead (Asc) + createdAt (Desc)
- [ ] Index 3: `chats` - lastMessageAt (Desc)
- [ ] Index 4: `messages` subcollection - createdAt (Usually auto-created, or use error link)

### Security Rules
- [ ] Copy the rules above to Firebase Console → Firestore → Rules → Publish

---

## 🎨 Your Custom Theme

Your app now uses a **Gaming Gear & Smart Home** theme with:
- **Primary Color:** Electric Blue (#00D4FF)
- **Secondary Color:** Purple Accent (#7B2CBF)
- **Background:** Light Gray (#F5F7FA)
- **Font:** Lato (Google Fonts)

All buttons, text fields, and cards now have rounded corners (12px) and match your theme!

---

## 🐛 Troubleshooting

### "Query requires an index" Error
- Solution: Click the blue link in the error message to auto-create the index
- Or manually create it in Firebase Console → Indexes

### "Permission denied" Error
- Solution: Make sure you published the security rules (not just saved)
- Check that your user role is set correctly in Firestore

### Chat messages not showing
- Solution: Firestore usually auto-creates this. If you see an error, click the error link to create it normally
- Make sure you're logged in

### Notifications not appearing
- Solution: Create the notifications composite index
- Make sure admin updated an order status

---

## 📊 Firebase Collections Structure

```
users/
  └── {userId}/
      ├── email: string
      ├── role: "user" | "admin"
      └── createdAt: timestamp

products/
  └── {productId}/
      ├── name: string
      ├── description: string
      ├── price: number
      ├── imageUrl: string
      └── createdAt: timestamp

userCarts/
  └── {userId}/
      └── cartItems: array of CartItem objects

orders/
  └── {orderId}/
      ├── userId: string
      ├── items: array
      ├── subtotal: number
      ├── vat: number
      ├── totalPrice: number
      ├── itemCount: number
      ├── status: "Pending" | "Processing" | "Shipped" | "Delivered" | "Cancelled"
      └── createdAt: timestamp

notifications/
  └── {notificationId}/
      ├── userId: string
      ├── title: string
      ├── body: string
      ├── orderId: string
      ├── isRead: boolean
      └── createdAt: timestamp

chats/
  └── {userId}/  (chatRoomId = user's ID)
      ├── userEmail: string
      ├── lastMessage: string
      ├── lastMessageAt: timestamp
      ├── unreadByUserCount: number
      ├── unreadByAdminCount: number
      └── messages/  (subcollection)
          └── {messageId}/
              ├── text: string
              ├── senderId: string
              ├── senderEmail: string
              └── createdAt: timestamp
```

---

## 🎯 Final Testing Checklist

Before submitting your project:

- [ ] App builds and runs without errors
- [ ] Splash screen and app icon are customized
- [ ] Login and Sign Up work
- [ ] Profile screen shows email and change password works
- [ ] Logout correctly redirects to login
- [ ] Admin can add products
- [ ] Products display on home screen
- [ ] Quantity selector works (can add multiple items)
- [ ] Cart shows subtotal, VAT (12%), and total
- [ ] Payment screen works (3-second delay)
- [ ] Orders are created in Firestore
- [ ] Order history shows user's orders
- [ ] Admin can view and update order status
- [ ] Notifications appear when admin updates order
- [ ] Chat works for users and admins
- [ ] Unread counts work on chat badges
- [ ] All Firestore indexes are created
- [ ] Security rules are published
- [ ] App theme matches Gaming Gear/Smart Home style

---

## 🚀 You're Ready!

Your complete e-commerce app is now ready with:
- ✅ Full authentication system
- ✅ Product management
- ✅ Shopping cart with VAT
- ✅ Order management
- ✅ Real-time notifications
- ✅ Real-time chat system
- ✅ Professional Gaming Gear/Smart Home theme

Good luck with your final project! 🎮🏠

