# Firebase Setup Checklist

## ✅ Step 1: Enable Authentication (Module 4)
1. Go to Firebase Console: https://console.firebase.google.com/
2. Select your project
3. Click **Build** → **Authentication**
4. Click **Get started** (if not already enabled)
5. Go to **Sign-in method** tab
6. Click on **Email/Password**
7. Toggle **Enable** to ON
8. Click **Save**

## ✅ Step 2: Enable Firestore Database (Module 5)
1. In Firebase Console, click **Build** → **Firestore Database**
2. Click **Create database**
3. Select **Start in test mode** (for development)
4. Click **Next**
5. Choose a **location** (closest to you)
6. Click **Enable**

## ✅ Step 3: Set Yourself as Admin (Module 5)
1. Run your app and **sign up** with your email
2. Go to Firebase Console → **Firestore Database**
3. You should see a **users** collection
4. Click on the **users** collection
5. Find the document with your User ID (the long string)
6. Click on that document
7. You should see fields: `email`, `role`, `createdAt`
8. Click the **Edit** icon (pencil) next to the `role` field
9. Change the value from `"user"` to `"admin"` (with quotes)
10. Click **Update**

**Important:** After this, log out and log back in to your app. You should now see the Admin Panel icon in the AppBar.

## ✅ Step 4: Create Firestore Index (Module 11/12.5)

### Option A: Automatic (Recommended)
1. Run your app
2. Log in as a regular user
3. Tap the **"My Orders"** icon (receipt icon) in the AppBar
4. You will see a red error screen
5. In the error message, there will be a **blue link** that says:
   `https://console.firebase.google.com/...`
6. **Click that link** - it will open Firebase Console with all fields pre-filled
7. Click **Create Index**
8. Wait 2-5 minutes for the index to build
9. Refresh your app - the error should be gone

### Option B: Manual
1. Go to Firebase Console → **Firestore Database**
2. Click on the **Indexes** tab
3. Click **Create Index**
4. Fill in:
   - **Collection ID:** `orders`
   - **Field 1:** `userId` → **Order:** Ascending
   - **Field 2:** `createdAt` → **Order:** Descending
5. Click **Create**
6. Wait 2-5 minutes for status to change from "Building" to "Enabled"

## ✅ Step 5: Test Your Setup

### Test Authentication:
- [ ] Can sign up with email/password
- [ ] Can log in
- [ ] Can log out

### Test Admin Panel:
- [ ] Admin icon appears in AppBar (after setting role to admin)
- [ ] Can add products in Admin Panel
- [ ] Products appear on Home Screen

### Test Cart:
- [ ] Can add items to cart
- [ ] Cart badge shows correct count
- [ ] Cart persists after app restart

### Test Orders:
- [ ] Can place order from cart
- [ ] Order appears in "My Orders" screen
- [ ] Admin can see all orders in "Manage Orders"
- [ ] Admin can update order status

## 🔒 Security Rules (For Production)

**Note:** Your Firestore is in "test mode" which allows all reads/writes. For production, you'll need to set up security rules. This is NOT required for the modules, but here's a basic example:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can read/write their own user document
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Users can read products, only admins can write
    match /products/{productId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && 
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Users can read/write their own cart
    match /userCarts/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Users can read their own orders, admins can read all
    match /orders/{orderId} {
      allow read: if request.auth != null && 
        (resource.data.userId == request.auth.uid || 
         get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin');
      allow create: if request.auth != null && request.auth.uid == resource.data.userId;
      allow update: if request.auth != null && 
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
  }
}
```

**For now, test mode is fine for development!**

