# Firestore Security Rules Setup Guide

## 📋 What Rules to Add

Copy the rules from `firestore.rules` file and paste them into Firebase Console.

## 🚀 How to Add Rules in Firebase Console

### Step 1: Open Firestore Rules
1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Select your project: **my-ecommerce-app-123**
3. Click **Build** → **Firestore Database**
4. Click on the **Rules** tab (at the top)

### Step 2: Replace the Default Rules
You'll see default test mode rules that look like this:
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.time < timestamp.date(2024, 12, 31);
    }
  }
}
```

### Step 3: Paste the New Rules
1. **Delete** all the existing rules
2. **Copy** the entire content from `firestore.rules` file
3. **Paste** it into the rules editor
4. Click **Publish**

## 📝 What Each Rule Does

### 1. **Users Collection** (`/users/{userId}`)
- ✅ Users can read/write their own user document
- ✅ Admins can read all user documents
- ✅ Users can create their own user document (during signup)
- ✅ Only admins can delete users

### 2. **Products Collection** (`/products/{productId}`)
- ✅ All authenticated users can read products (view product list)
- ✅ Only admins can create products (add new products)
- ✅ Only admins can update products
- ✅ Only admins can delete products

### 3. **User Carts Collection** (`/userCarts/{userId}`)
- ✅ Users can only read/write their own cart
- ✅ Users cannot see or modify other users' carts

### 4. **Orders Collection** (`/orders/{orderId}`)
- ✅ Users can read their own orders (view order history)
- ✅ Users can create orders (place order - only for themselves)
- ✅ Admins can read all orders (view all orders in admin panel)
- ✅ Admins can update order status (change status to Processing, Shipped, etc.)
- ✅ Only admins can delete orders

## ⚠️ Important Notes

### For Development/Testing:
- **Test mode is fine** - it allows all reads/writes for 30 days
- You can keep test mode if you're still developing
- Rules are **optional** during development

### For Production:
- **You MUST add these rules** before going live
- Test mode expires after 30 days
- Without proper rules, your database is vulnerable

## 🧪 Testing Your Rules

After adding rules, test them:

1. **Test as Regular User:**
   - ✅ Can view products
   - ✅ Can add to cart
   - ✅ Can place orders
   - ✅ Can view own orders
   - ❌ Cannot add products
   - ❌ Cannot see other users' carts
   - ❌ Cannot see all orders

2. **Test as Admin:**
   - ✅ Can view products
   - ✅ Can add products
   - ✅ Can view all orders
   - ✅ Can update order status
   - ✅ Can view all users

## 🔧 Troubleshooting

### Error: "Missing or insufficient permissions"
- Check if you're logged in
- Check if your user role is set correctly
- Verify the rules are published (not just saved)

### Error: "Permission denied"
- Make sure you clicked **Publish** (not just Save)
- Wait a few seconds for rules to propagate
- Try logging out and back in

### Rules Not Working?
1. Check Firebase Console → Rules tab → Make sure rules are published
2. Check your user's role in Firestore → `users/{yourUserId}` → `role` should be `"admin"` or `"user"`
3. Make sure you're authenticated (logged in)

## 📄 Current Rules File

The complete rules are saved in `firestore.rules` in your project root. You can copy from there or use the Firebase Console editor.

