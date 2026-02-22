# 🚀 Quick Start Guide

## Step-by-Step Setup Instructions

### 1️⃣ Install Python Dependencies
```bash
cd main_project
pip install -r requirements.txt
```

### 2️⃣ Create Firebase Project

1. **Go to Firebase Console**: https://console.firebase.google.com/
2. **Create New Project**:
   - Click "Add project"
   - Project name: `attendance-system` (or your choice)
   - Disable Google Analytics (optional)
   - Click "Create Project"

### 3️⃣ Enable Firebase Services

#### A. Enable Authentication
1. In your Firebase project, click "Authentication" in the left sidebar
2. Click "Get Started"
3. Go to "Sign-in method" tab
4. Enable "Email/Password"
5. Save

#### B. Enable Firestore Database
1. Click "Firestore Database" in the left sidebar
2. Click "Create database"
3. Select "Start in production mode"
4. Choose location: `us-central1` (or closest to you)
5. Click "Enable"

> ~~**Firebase Storage**~~ — **Not needed!** The face embedding uniquely identifies each student. No photos are stored in the cloud.

### 4️⃣ Set Security Rules

#### Firestore Rules
1. Go to Firestore Database → Rules tab
2. Replace the rules with the following:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper function to check if user is authenticated
    function isSignedIn() {
      return request.auth != null;
    }
    
    // Helper function to check if user is admin
    function isAdmin() {
      return isSignedIn() && request.auth.token.role == 'admin';
    }
    
    // Helper function to check if user is the owner
    function isOwner(userId) {
      return isSignedIn() && request.auth.uid == userId;
    }
    
    // Admins collection: Only admins can read/write
    match /admins/{adminId} {
      allow read, write: if isAdmin();
    }
    
    // Students collection: Admins can do anything, students can only read their own profile
    match /students/{studentId} {
      allow read, write: if isAdmin();
      allow read: if isOwner(studentId);
    }
    
    // Embeddings collection: Only admins can read/write
    match /embeddings/{embeddingId} {
      allow read, write: if isAdmin();
    }
    
    // Attendance log: Admins can do anything, students can only read logs where they appear
    match /attendance_log/{logId} {
      allow read, write: if isAdmin();
      allow read: if isSignedIn();  // Students can browse attendance
    }
  }
}
```

3. Click "Publish"

> **Storage Rules: Not needed** — Firebase Storage is not used in this project.

### 5️⃣ Download Firebase Credentials

1. Click the ⚙️ (gear icon) → **Project Settings**
2. Go to **Service Accounts** tab
3. Click **"Generate New Private Key"**
4. Save the downloaded JSON file as:
   ```
   main_project/firebase/firebase_config.json
   ```

### 6️⃣ Configure Environment Variables

1. From inside the `main_project/` folder, create a `.env` file:
   ```bash
   copy firebase\.env.example .env
   ```
   This places `.env` at `main_project/.env` — the correct location.

2. Open `.env` and set:
   ```
   FIREBASE_CREDENTIALS_PATH=./firebase/firebase_config.json
   ```

3. That's it — no other variables needed!

### 7️⃣ Create First Admin Account

```bash
cd scripts
python create_admin.py
```

Follow the prompts to create your admin account.

### 8️⃣ Test Firebase Connection

Open Python and run:
```python
from firebase.firebase_service import initialize_firebase

db = initialize_firebase()
print("✅ Firebase connected successfully!")
```

### 9️⃣ Enroll Your First Student

1. Place a student photo in `temp_uploads/` folder
2. Open `notebooks/enrollment.ipynb` in Jupyter
3. Follow the notebook instructions
4. Update these variables:
   - `STUDENT_NAME`
   - `ROLL_NO`
   - `EMAIL`
   - `PASSWORD`
   - `PHOTO_PATH`
5. Run all cells

### 🔟 Mark Attendance

1. Place a group photo in `temp_uploads/` folder
2. Open `notebooks/attendance.ipynb` in Jupyter
3. Update `GROUP_PHOTO_PATH`
4. Run all cells
5. Check the generated CSV in `attendance/` folder

---

## 🎯 Workflow Summary

```
1. Admin creates account → scripts/create_admin.py
2. Admin enrolls students → notebooks/enrollment.ipynb
3. Admin takes group photo → Camera
4. Admin marks attendance → notebooks/attendance.ipynb
5. Students login to view → web/app.py (coming soon)
```

---

## ⚠️ Troubleshooting

### "Firebase credentials not found"
- Make sure `firebase_config.json` is in the `firebase/` folder
- Check `.env` file has correct path

### "No module named firebase_admin"
- Run: `pip install firebase-admin`

### "Email already exists"
- Each student needs a unique email
- Use format: `rollno@college.edu`

### "No face detected"
- Use clear, well-lit photos
- Face should be visible and front-facing
- Try adjusting `conf=0.4` parameter

### "Similarity too low"
- Enrollment photo and group photo should have similar lighting
- Face should not be occluded (no masks, sunglasses)
- Try lowering threshold from 0.4 to 0.3

---

## 📚 Next Steps

After basic setup:
1. Build web interface (Flask/Streamlit)
2. Add bulk enrollment feature
3. Add attendance report dashboard
4. Deploy to cloud (Firebase Hosting)

See [README.md](README.md) for full project documentation.
