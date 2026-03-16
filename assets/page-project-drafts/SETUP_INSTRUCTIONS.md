# LettersGR Help & Contact Setup

Follow these steps to enable the production-safe support system.

### 1. Dependencies
Install the required Firebase SDKs:
```bash
npm install firebase
```

### 2. Firebase Project Setup
1. Create a project at [Firebase Console](https://console.firebase.google.com/).
2. Enable **Firestore Database** (start in production mode).
3. Enable **Firebase Storage**.
4. Register a **Web App** and paste the config into `firebase.ts`.

### 3. Firestore Rules
Add these rules in the Firestore Rules tab:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /support_messages/{messageId} {
      allow create: if request.resource.data.email != "" 
                    && request.resource.data.message != "";
      allow read, update, delete: if false; // Only admin via Console/Functions
    }
  }
}
```

### 4. Storage Rules
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /screenshots/{allPaths=**} {
      allow write: if request.resource.size < 5 * 1024 * 1024; // Limit 5MB
      allow read: if true;
    }
  }
}
```

### 5. Cloud Function Deployment
1. Install Firebase CLI: `npm install -g firebase-tools`.
2. Run `firebase init functions`.
3. Copy `functions/index.js` content to the generated `functions/index.js`.
4. Deploy: `firebase deploy --only functions`.

---
*Note: For Gmail, you'll need an "App Password" enabled via Google Account security settings.*