# 🔥 CRITICAL: Firestore Security Rules Fix

## The Problem
Your app is crashing because Firestore is denying permission to read/write user documents.

**Error from logs:**
```
PERMISSION_DENIED: Missing or insufficient permissions.
```

## The Solution: Update Firestore Security Rules

### Step 1: Go to Firebase Console
1. Open [Firebase Console](https://console.firebase.google.com/)
2. Select your **Arcadia** project (`arcadia-e5d44`)
3. Click **Firestore Database** in the left menu
4. Click the **Rules** tab at the top

### Step 2: Replace the Rules

Replace whatever rules you have with these:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Allow authenticated users to read and write their own user document
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Optionally: Allow users to read all user documents (for profiles)
    // match /users/{userId} {
    //   allow read: if request.auth != null;
    //   allow write: if request.auth != null && request.auth.uid == userId;
    // }
  }
}
```

### Step 3: Publish the Rules
1. Click **Publish** button
2. Wait for confirmation

### Step 4: Test Your App
1. Uninstall the app from your device/emulator
2. Reinstall
3. Try to sign in again

---

## Explanation of the Rules

### Current Rule (Secure):
```javascript
match /users/{userId} {
  allow read, write: if request.auth != null && request.auth.uid == userId;
}
```

This means:
- ✅ User can ONLY read/write their OWN document
- ✅ Must be authenticated
- ✅ Most secure option

### Alternative Rule (If you need users to see other profiles):
```javascript
match /users/{userId} {
  allow read: if request.auth != null;
  allow write: if request.auth != null && request.auth.uid == userId;
}
```

This means:
- ✅ Any authenticated user can READ any profile
- ✅ User can only WRITE their own document
- 🔓 Less secure but allows profile viewing

---

## Quick Test in Firebase Console

After publishing the rules, you can test them:

1. Go to **Rules playground** tab
2. Select **get** operation
3. Location: `/users/Jk2EewjD0OVGqAQT4k3vekGtRR83`
4. Check **Authenticated**
5. Set UID: `Jk2EewjD0OVGqAQT4k3vekGtRR83`
6. Click **Run**
7. Should show: ✅ **Allowed**

---

## What Changed in Code

I also fixed the crash issue where Toast was being shown from a background thread. The callbacks now properly switch to the Main thread before showing UI messages.

---

## Summary

**Before:** Firestore blocked all access → Permission Denied → App crashed

**After:** Firestore allows authenticated users to access their own data → Sign-in works → Profile loads

**This fix is REQUIRED for the app to work!** 🚀

