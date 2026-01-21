# Project Structure & Setup Instructions

## 📁 Complete Project Folder Layout

```
startup-platform/
│
├── frontend/                    # React + 3D Portfolio
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── components/
│   │   │   ├── Navbar.jsx
│   │   │   ├── Hero3D.jsx
│   │   │   ├── ProjectShowcase.jsx
│   │   │   ├── Admin/
│   │   │   │   ├── AdminDashboard.jsx
│   │   │   │   ├── ProjectManager.jsx
│   │   │   │   ├── ClientManager.jsx
│   │   │   │   └── Analytics.jsx
│   │   │   └── Client/
│   │   │       ├── ClientPortal.jsx
│   │   │       ├── ProjectStatus.jsx
│   │   │       └── Files.jsx
│   │   ├── pages/
│   │   │   ├── Home.jsx
│   │   │   ├── LoginAdmin.jsx
│   │   │   ├── LoginClient.jsx
│   │   │   ├── Dashboard.jsx
│   │   │   └── NotFound.jsx
│   │   ├── context/
│   │   │   ├── AuthContext.jsx
│   │   │   └── ProjectContext.jsx
│   │   ├── hooks/
│   │   │   ├── useAuth.js
│   │   │   ├── useProjects.js
│   │   │   └── useFetch.js
│   │   ├── services/
│   │   │   ├── api.js
│   │   │   ├── authService.js
│   │   │   └── projectService.js
│   │   ├── utils/
│   │   │   ├── animations.js
│   │   │   └── validators.js
│   │   ├── styles/
│   │   │   ├── global.css
│   │   │   ├── animations.css
│   │   │   └── responsive.css
│   │   ├── App.jsx
│   │   └── index.js
│   ├── package.json
│   ├── .env.example
│   └── vite.config.js
│
├── backend/                     # Node.js + Express API
│   ├── config/
│   │   ├── db.js
│   │   ├── jwt.js
│   │   └── email.js
│   ├── controllers/
│   │   ├── authController.js
│   │   ├── projectController.js
│   │   ├── clientController.js
│   │   ├── taskController.js
│   │   └── analyticsController.js
│   ├── models/
│   │   ├── User.js
│   │   ├── Project.js
│   │   ├── Client.js
│   │   ├── Task.js
│   │   ├── Invoice.js
│   │   └── Analytics.js
│   ├── routes/
│   │   ├── auth.js
│   │   ├── projects.js
│   │   ├── clients.js
│   │   ├── tasks.js
│   │   └── admin.js
│   ├── middleware/
│   │   ├── auth.js
│   │   ├── errorHandler.js
│   │   └── validation.js
│   ├── utils/
│   │   ├── logger.js
│   │   ├── errors.js
│   │   └── email.js
│   ├── server.js
│   ├── package.json
│   └── .env.example
│
├── database/
│   └── schemas.json            # Database schema documentation
│
├── .gitignore
├── README.md
└── deployment-guide.md
```

---

## 🚀 Step-by-Step Setup

### Prerequisites
- Node.js (v16 or higher)
- MongoDB (free account at mongodb.com/atlas)
- Git

### 1. Clone Repository
```bash
git clone https://github.com/yourusername/startup-platform.git
cd startup-platform
```

### 2. Backend Setup

```bash
cd backend

# Install dependencies
npm install

# Create .env file
cp .env.example .env

# Edit .env with your values
nano .env

# Start server
npm start
# Server runs on http://localhost:5000
```

**Backend Dependencies:**
```json
{
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^7.0.0",
    "jsonwebtoken": "^9.0.0",
    "bcryptjs": "^2.4.3",
    "dotenv": "^16.0.3",
    "cors": "^2.8.5",
    "multer": "^1.4.5",
    "nodemailer": "^6.9.0",
    "express-validator": "^7.0.0",
    "socket.io": "^4.5.1"
  }
}
```

### 3. Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Create .env
cp .env.example .env

# Edit .env with API URL
nano .env

# Development
npm run dev

# Production build
npm run build
```

**Frontend Dependencies:**
```json
{
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.10.0",
    "axios": "^1.3.0",
    "zustand": "^4.3.0",
    "three": "^r148",
    "@react-three/fiber": "^8.11.0",
    "@react-three/drei": "^9.62.0",
    "framer-motion": "^10.10.0",
    "tailwindcss": "^3.2.7",
    "recharts": "^2.5.0",
    "socket.io-client": "^4.5.1"
  }
}
```

---

## 🔧 Configuration Files

### Backend .env Template
```
# Server
PORT=5000
NODE_ENV=production

# Database
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/startup-db

# JWT
JWT_SECRET=your-super-secret-jwt-key-change-this
JWT_EXPIRE=7d

# Email
EMAIL_SERVICE=gmail
EMAIL_USER=your-email@gmail.com
EMAIL_PASS=your-app-password

# CORS
FRONTEND_URL=https://yourdomain.com

# File Upload
MAX_FILE_SIZE=10485760

# API Keys (Optional)
STRIPE_SECRET_KEY=sk_test_...
GITHUB_TOKEN=ghp_...
```

### Frontend .env Template
```
REACT_APP_API_URL=https://api.yourdomain.com
REACT_APP_ENV=production
```

---

## 🗄️ Database Setup (MongoDB Atlas)

1. **Create Account**
   - Go to mongodb.com/atlas
   - Sign up free
   - Create organization & project

2. **Create Cluster**
   - Click "Create Deployment"
   - Choose Shared (Free tier)
   - Select region closest to you
   - Click "Create"

3. **Get Connection String**
   - Click "Connect"
   - Choose "Connect your application"
   - Copy connection string
   - Replace `<username>`, `<password>`, `<cluster>`
   - Add to .env as MONGODB_URI

4. **Create Collections** (Auto-created by schema)
   - Users
   - Projects
   - Clients
   - Tasks
   - Invoices
   - Analytics

---

## 📦 Database Schemas

### User Schema
```javascript
{
  _id: ObjectId,
  name: String,
  email: String (unique),
  password: String (hashed),
  role: String (admin, client, team),
  company: String,
  avatar: String (URL),
  phone: String,
  createdAt: Date,
  updatedAt: Date
}
```

### Project Schema
```javascript
{
  _id: ObjectId,
  title: String,
  description: String,
  image: String,
  technologies: [String],
  link: String,
  github: String,
  status: String (active, completed, archived),
  startDate: Date,
  endDate: Date,
  client: ObjectId (ref: Client),
  team: [ObjectId] (ref: User),
  tasks: [ObjectId] (ref: Task),
  attachments: [String] (URLs),
  createdBy: ObjectId (ref: User),
  createdAt: Date,
  updatedAt: Date
}
```

### Client Schema
```javascript
{
  _id: ObjectId,
  name: String,
  email: String,
  company: String,
  phone: String,
  address: String,
  projects: [ObjectId] (ref: Project),
  status: String (active, inactive),
  notes: String,
  logo: String,
  website: String,
  createdAt: Date,
  updatedAt: Date
}
```

### Task Schema
```javascript
{
  _id: ObjectId,
  title: String,
  description: String,
  project: ObjectId (ref: Project),
  assignee: ObjectId (ref: User),
  status: String (todo, inprogress, review, done),
  priority: String (low, medium, high),
  dueDate: Date,
  createdAt: Date,
  updatedAt: Date
}
```

---

## 🚢 Deployment Options

### Option 1: Vercel (Frontend Only)
```bash
npm i -g vercel
cd frontend
vercel --prod
```

### Option 2: Railway (Full Stack)
1. Connect GitHub repo
2. Create services for frontend & backend
3. Add environment variables
4. Deploy

### Option 3: AWS
- Frontend: S3 + CloudFront
- Backend: EC2 or Lambda
- Database: MongoDB Atlas

### Option 4: DigitalOcean
- App Platform
- Container Registry
- Managed Databases

---

## ✅ Final Checklist

- [ ] Node.js installed
- [ ] MongoDB Atlas account created
- [ ] Project cloned to local
- [ ] Backend .env configured
- [ ] Frontend .env configured
- [ ] Dependencies installed (npm install)
- [ ] Backend tested locally
- [ ] Frontend tested locally
- [ ] 3D animations working
- [ ] Admin login functional
- [ ] Client portal functional
- [ ] Database connected
- [ ] Email service configured
- [ ] Deployed to production
- [ ] Custom domain connected
- [ ] SSL certificate enabled
- [ ] Backups configured

---

## 📞 Getting Help

- Check logs: `npm start` shows errors
- Debug mode: Add `DEBUG=*` before npm start
- Console: Check browser DevTools (F12)
- Check API: Use Postman or curl
- Database: Check MongoDB Atlas dashboard

You're all set! 🎉
