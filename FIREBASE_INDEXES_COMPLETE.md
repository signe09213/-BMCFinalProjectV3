# Complete Firebase Indexes Guide
## All Indexes Needed for Your App

---

## 📋 Summary

You need **4 indexes** total. Here's the complete list:

---

## ✅ Index 1: Orders - User Orders (REQUIRED)

**Collection:** `orders`

**Fields:**
- `userId` → Ascending
- `createdAt` → Descending

**Used in:** `order_history_screen.dart`

**How to Create:**
1. Run app → Go to "My Orders"
2. Click the blue link in the error
3. Or manually: Firestore → Indexes → Create Index

---

## ✅ Index 2: Notifications - Unread Query (REQUIRED)

**Collection:** `notifications`

**Fields:**
- `userId` → Ascending
- `isRead` → Ascending
- `createdAt` → Descending

**Used in:** `notification_icon.dart`

**How to Create:**
1. Run app → Tap notification bell
2. Click the blue link in the error
3. Or manually create composite index

---

## ✅ Index 3: Chats - Sort by Last Message (REQUIRED)

**Collection:** `chats`

**Fields:**
- `lastMessageAt` → Descending

**Used in:** `admin_chat_list_screen.dart`

**How to Create:**
1. Run app as admin → Admin Panel → View User Chats
2. Click the blue link in the error
3. Or manually: Single-field index

---

## ✅ Index 4: Messages - Sort by Date (Usually Auto-Created)

**Collection:** `chats/{chatRoomId}/messages` (Subcollection)

**Fields:**
- `createdAt` → Ascending (or Descending)

**Used in:** `chat_screen.dart`

**How to Create:**
1. **Firestore usually auto-creates this!** Just run your app and open a chat.
2. If you see an error, click the blue link in the error message
3. It will open Firebase Console with fields pre-filled
4. Click "Create Index"
5. Wait 2-5 minutes

**Note:** 
- Firestore **automatically creates** single-field indexes for subcollections in most cases
- You **do NOT** need to use "Add exemption" - that's for disabling auto-indexing
- If you see an error, use the error link to create it normally

---

## ⏱️ Index Building Time

- **Small indexes:** 1-2 minutes
- **Large indexes:** 5-10 minutes
- **Subcollection indexes:** 5-10 minutes

**Status will show:**
- ⏳ **Building** - Wait
- ✅ **Enabled** - Ready to use!
- ❌ **Error** - Check the error message

---

## 🔍 How to Check Index Status

1. Firebase Console → Firestore Database → **Indexes** tab
2. You'll see all your indexes listed
3. Check the status column

---

## ⚠️ Important Notes

1. **You MUST create all 4 indexes** - The app won't work without them
2. **Wait for indexes to finish building** - Don't test until status is "Enabled"
3. **Use the error links** - They're the easiest way to create indexes
4. **Subcollection indexes are different** - Use "Add exemption" for messages

---

## 🎯 Quick Reference

| Index | Collection | Fields | Status |
|-------|-----------|--------|--------|
| 1 | `orders` | userId (Asc) + createdAt (Desc) | ⬜ |
| 2 | `notifications` | userId (Asc) + isRead (Asc) + createdAt (Desc) | ⬜ |
| 3 | `chats` | lastMessageAt (Desc) | ⬜ |
| 4 | `messages` | createdAt (Asc/Desc) | ⬜ |

Check them off as you create each one!

