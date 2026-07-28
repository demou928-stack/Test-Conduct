# School Online Examination Portal (Free)

A completely free, AI-friendly online test website for schools using **Firebase Free Tier** + pure HTML/CSS/JS.

## Features

- **Teacher Portal**: Create/edit/publish tests (MCQ, True/False, Fill blanks, Short/Long answer), set marks, duration, schedule, shuffle, auto-grade objective questions, view/export results (Excel/CSV).
- **Student Portal**: Login with Roll Number + Password, take timed tests (one question at a time), auto-submit, view scores (if allowed).
- Responsive (Mobile/Tablet/Desktop), Dark/Light mode, Blue-White school theme.
- Excel export via SheetJS, charts, announcements, profiles, etc.
- Hosted free on Firebase Hosting or GitHub Pages.

## Tech Stack (100% Free)

- HTML5, CSS3, Vanilla JavaScript
- Firebase Authentication (Email/Password for teachers, custom for students)
- Cloud Firestore (database)
- Firebase Storage (logos, etc.)
- SheetJS (xlsx export)
- Chart.js (analytics)
- Firebase Hosting / GitHub Pages

## Project Structure

```
exam-portal/
├── index.html                 # Landing page
├── css/
│   ├── style.css              # Main styles + dark/light
│   └── responsive.css
├── js/
│   ├── firebase-config.js     # Your Firebase keys
│   ├── auth.js
│   ├── teacher.js
│   ├── student.js
│   ├── test-engine.js
│   └── utils.js
├── teacher/
│   ├── login.html
│   ├── dashboard.html
│   ├── create-test.html
│   ├── manage-tests.html
│   ├── results.html
│   └── profile.html
├── student/
│   ├── login.html
│   ├── dashboard.html
│   ├── take-test.html
│   ├── result.html
│   └── profile.html
├── assets/                    # logos, icons
└── README.md
```

## Setup Instructions

### 1. Create Firebase Project (Free)

1. Go to [https://console.firebase.google.com](https://console.firebase.google.com)
2. Create a new project (Spark / Free plan)
3. Enable **Authentication** → Sign-in method → Email/Password
4. Create **Firestore Database** (start in production mode, then set rules)
5. Enable **Storage** if you want logo upload
6. Go to Project Settings → General → Your apps → Add Web app
7. Copy the `firebaseConfig` object

### 2. Configure the Project

Edit `js/firebase-config.js` and paste your config:

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "...",
  appId: "..."
};
```

### 3. Firestore Security Rules (Important)

Paste these rules in Firestore → Rules (adjust as needed for production):

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Teachers collection
    match /teachers/{teacherId} {
      allow read, write: if request.auth != null && request.auth.uid == teacherId;
    }
    // Students - teachers can manage, students can read own
    match /students/{studentId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null; // tighten later
    }
    // Tests
    match /tests/{testId} {
      allow read: if true; // public for students during window
      allow write: if request.auth != null;
    }
    // Submissions
    match /submissions/{subId} {
      allow read, write: if request.auth != null;
    }
    // Results
    match /results/{resultId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

### 4. Local Testing

Simply open `index.html` in a browser **or** use a local server:

```bash
# If you have Python
python -m http.server 5500
# Then visit http://localhost:5500
```

Firebase requires HTTPS or localhost for Auth.

### 5. Deploy (Free)

**Option A – Firebase Hosting**
```bash
npm install -g firebase-tools
firebase login
firebase init hosting
# Select public directory as the project root
firebase deploy
```

**Option B – GitHub Pages**
1. Push the folder to a GitHub repo
2. Settings → Pages → Source: main branch / root
3. Your site will be at `https://username.github.io/repo-name`

## Default Teacher Account

After first load, register a teacher from the Teacher Login page (or seed one in Firestore).

Students are created by the teacher (or bulk import via CSV later).

## How to Use

1. Teacher registers / logs in
2. Creates a test → adds questions of various types → sets schedule & duration → Publishes
3. Students log in with Roll Number + Password (set by teacher)
4. Students take the test (timer, one question view)
5. Objective questions auto-graded; subjective pending
6. Teacher views results, exports Excel/CSV, generates reports

## Notes

- Free Firebase quotas are generous for a school (50k reads/day, 20k writes/day on Spark plan).
- For larger schools consider upgrading later or optimizing queries.
- All core features work offline after first load if you add service worker (optional).
- SheetJS is loaded via CDN for Excel export.
- Chart.js via CDN for analytics.

## License

MIT – Free for school use. Modify as needed.

---

Created for school teachers who want a zero-cost examination platform.
