# AcadeX — College DBMS Backend
> Node.js + Express + MongoDB | JWT Auth | Role-Based Access

## Folder Structure
```
academx/
├── server.js                  # Entry point — mounts all routes
├── package.json
├── .env.example               # Copy to .env and fill in values
├── config/db.js               # MongoDB connection
├── middleware/auth.js         # JWT protect + role authorize
├── models/
│   ├── User.js                # bcrypt password, role enum
│   ├── Student.js             # admNo, year, semester, avatar
│   ├── Faculty.js             # employeeId, subjects array
│   ├── Subject.js             # CE40/CE60, semester, faculty ref
│   ├── Marks.js               # s1/s2/assign/cmark + publish flags
│   └── Attendance.js          # daily records, auto % calc
├── controllers/
│   └── authController.js      # login / getMe / changePassword
├── routes/
│   ├── auth.js                # POST /login, GET /me, PUT /change-password
│   ├── users.js               # Admin: CRUD all users
│   ├── students.js            # List/view students
│   ├── faculty.js             # List/manage faculty
│   ├── subjects.js            # Semester-wise subjects CRUD
│   ├── marks.js               # Bulk entry, publish, CE compute
│   ├── attendance.js          # Daily mark, leaderboard, summary
│   └── dashboard.js           # Role-based aggregated dashboards
├── utils/seed.js              # Seeds all demo data
└── frontend/index.html        # Complete single-file frontend
```

## Quick Setup

### 1. Install MongoDB
- **Local:** Install from https://www.mongodb.com/try/download/community
- **Cloud (recommended):** Free tier at https://cloud.mongodb.com

### 2. Clone & install dependencies
```bash
cd academx
npm install
```

### 3. Configure environment
```bash
cp .env.example .env
# Edit .env:
#   MONGODB_URI=mongodb+srv://<user>:<pass>@cluster.mongodb.net/academx
#   JWT_SECRET=your_very_long_random_secret_key_here
#   PORT=5000
```

### 4. Seed the database
```bash
node utils/seed.js
```
This creates all users, students, faculty, subjects, marks and attendance records.

### 5. Start the backend
```bash
npm run dev     # with nodemon (auto-restart)
# or
npm start       # production
```

### 6. Open the frontend
Open `frontend/index.html` in your browser.
- Works in demo mode even without the backend!
- For full persistence: update `API_BASE` in index.html to your backend URL.

## API Reference

### Auth
| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| POST | /api/auth/login | Public | Login, returns JWT |
| GET | /api/auth/me | All | Get own profile |
| PUT | /api/auth/change-password | All | Change password |

### Users (Admin only)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/users | List all users |
| POST | /api/users | Create user + profile |
| DELETE | /api/users/:id | Deactivate user |
| PUT | /api/users/:id | Update user info |

### Marks (Faculty enter, Students view published)
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /api/marks/bulk | Save marks for whole class |
| POST | /api/marks/publish-subject | Publish a mark type |
| GET | /api/marks/student/:id | Student's marks |
| GET | /api/marks/subject/:id | All students for a subject |
| GET | /api/marks/ce/:sid/:subid | Compute CE for student-subject |

### Attendance
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /api/attendance/mark | Save daily attendance |
| GET | /api/attendance/leaderboard | Monthly ranking |
| GET | /api/attendance/student/:id | Student's attendance |
| GET | /api/attendance/summary/:id | Year-wise summary |

### Dashboards (role-gated)
| Endpoint | Role |
|----------|------|
| GET /api/dashboard/student | student |
| GET /api/dashboard/faculty | faculty |
| GET /api/dashboard/hod | hod |
| GET /api/dashboard/coordinator | coordinator |
| GET /api/dashboard/admin | admin |

## Demo Credentials
| Username | Password | Role |
|----------|----------|------|
| admin | admin123 | Admin |
| beena | hod123 | HOD |
| archa | coord123 | Coordinator |
| rajsr | teacher123 | Faculty |
| aslam | aslam123 | Student |
| aashith | aashith123 | Student |
| mishal | mishal123 | Student |
| kaiz | kaiz123 | Student |

## Database Design Rationale (MongoDB chosen over MySQL)

| Factor | MongoDB ✓ | MySQL |
|--------|-----------|-------|
| Attendance daily records | Nested day-array = perfect fit | Requires separate table + JOINs |
| Student vs Faculty profiles | Flexible schema per role | Requires multiple tables + UNION |
| Dashboard aggregations | $group, $avg pipelines | Complex multi-table JOINs |
| Marks publish flags | Per-field booleans on same doc | Extra columns or junction table |
| Development speed | Schema-less = faster iteration | Migrations needed for every change |
