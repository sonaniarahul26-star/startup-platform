# FRONTEND SOURCE CODE & QUICK DEPLOYMENT

## 📄 Frontend - package.json

```json
{
  "name": "startup-platform-frontend",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "deploy": "vercel --prod"
  },
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
    "socket.io-client": "^4.5.1",
    "react-icons": "^4.8.0",
    "react-hot-toast": "^2.4.0"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^3.1.0",
    "vite": "^4.2.0",
    "postcss": "^8.4.24",
    "autoprefixer": "^10.4.14"
  }
}
```

---

## 📄 Frontend - vite.config.js

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: 'http://localhost:5000',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '/api')
      }
    }
  },
  build: {
    outDir: 'build',
    sourcemap: false,
    minify: 'terser'
  }
})
```

---

## 📄 Frontend - src/App.jsx

```javascript
import { BrowserRouter as Router, Routes, Route, Navigate } from 'react-router-dom';
import { Toaster } from 'react-hot-toast';
import AuthContext from './context/AuthContext';
import ProtectedRoute from './components/ProtectedRoute';

// Pages
import Home from './pages/Home';
import LoginAdmin from './pages/LoginAdmin';
import LoginClient from './pages/LoginClient';
import AdminDashboard from './components/Admin/AdminDashboard';
import ClientPortal from './components/Client/ClientPortal';
import NotFound from './pages/NotFound';

function App() {
  return (
    <Router>
      <AuthContext>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/login/admin" element={<LoginAdmin />} />
          <Route path="/login/client" element={<LoginClient />} />
          
          <Route 
            path="/admin/*" 
            element={
              <ProtectedRoute requiredRole="admin">
                <AdminDashboard />
              </ProtectedRoute>
            } 
          />
          
          <Route 
            path="/client/*" 
            element={
              <ProtectedRoute requiredRole="client">
                <ClientPortal />
              </ProtectedRoute>
            } 
          />
          
          <Route path="*" element={<NotFound />} />
        </Routes>
        <Toaster />
      </AuthContext>
    </Router>
  );
}

export default App;
```

---

## 📄 Frontend - src/context/AuthContext.jsx

```javascript
import { createContext, useState, useEffect } from 'react';
import axios from 'axios';

export const AuthContext = createContext();

export default function AuthContextProvider({ children }) {
  const [user, setUser] = useState(null);
  const [token, setToken] = useState(localStorage.getItem('token'));
  const [loading, setLoading] = useState(true);

  // Set axios default header
  if (token) {
    axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  }

  useEffect(() => {
    if (token) {
      fetchUser();
    } else {
      setLoading(false);
    }
  }, [token]);

  const fetchUser = async () => {
    try {
      const res = await axios.get('/api/auth/me');
      setUser(res.data);
    } catch (error) {
      console.error('Failed to fetch user:', error);
      logout();
    } finally {
      setLoading(false);
    }
  };

  const login = async (email, password, role = 'client') => {
    try {
      const res = await axios.post('/api/auth/login', { email, password, role });
      const { token, user } = res.data;
      
      localStorage.setItem('token', token);
      setToken(token);
      setUser(user);
      
      axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;
      return true;
    } catch (error) {
      console.error('Login failed:', error);
      return false;
    }
  };

  const logout = () => {
    localStorage.removeItem('token');
    setToken(null);
    setUser(null);
    delete axios.defaults.headers.common['Authorization'];
  };

  const register = async (name, email, password, company, role = 'client') => {
    try {
      const res = await axios.post('/api/auth/register', {
        name,
        email,
        password,
        company,
        role
      });
      const { token, user } = res.data;
      
      localStorage.setItem('token', token);
      setToken(token);
      setUser(user);
      
      axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;
      return true;
    } catch (error) {
      console.error('Registration failed:', error);
      return false;
    }
  };

  return (
    <AuthContext.Provider value={{ user, token, loading, login, logout, register }}>
      {children}
    </AuthContext.Provider>
  );
}
```

---

## 📄 Frontend - src/pages/Home.jsx (with 3D Background)

```javascript
import { Suspense } from 'react';
import { Canvas } from '@react-three/fiber';
import { Stars, Sphere, useTexture } from '@react-three/drei';
import { motion } from 'framer-motion';
import { Link } from 'react-router-dom';
import Navbar from '../components/Navbar';

// 3D Background Component
function AnimatedSphere() {
  return (
    <Sphere args={[1, 100, 200]} scale={2.4}>
      <motion.meshPhongMaterial
        color="#ff006e"
        emissive="#ff006e"
        emissiveIntensity={0.2}
        animate={{
          emissiveIntensity: [0.2, 0.4, 0.2]
        }}
        transition={{ duration: 3, repeat: Infinity }}
      />
    </Sphere>
  );
}

function Canvas3D() {
  return (
    <Canvas camera={{ position: [0, 0, 2] }} style={{ position: 'absolute', top: 0, left: 0 }}>
      <ambientLight intensity={0.5} />
      <Stars radius={100} depth={50} count={500} factor={4} saturation={0.5} fade speed={1} />
      <Suspense fallback={null}>
        <AnimatedSphere />
      </Suspense>
    </Canvas>
  );
}

export default function Home() {
  return (
    <div className="w-full min-h-screen bg-gradient-to-br from-slate-900 to-slate-800 text-white relative overflow-hidden">
      <Canvas3D />
      
      <Navbar />
      
      <div className="relative z-10 container mx-auto px-4 pt-20">
        <motion.div
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.8 }}
          className="text-center py-20"
        >
          <h1 className="text-6xl md:text-7xl font-bold mb-6 bg-gradient-to-r from-pink-500 to-blue-500 bg-clip-text text-transparent">
            Startup Platform
          </h1>
          <p className="text-xl md:text-2xl text-gray-300 mb-12 max-w-2xl mx-auto">
            Manage your portfolio, projects, and clients all in one place
          </p>
          
          <div className="flex gap-4 justify-center flex-wrap">
            <Link
              to="/login/admin"
              className="px-8 py-3 bg-pink-500 hover:bg-pink-600 rounded-lg font-bold transition"
            >
              Admin Login
            </Link>
            <Link
              to="/login/client"
              className="px-8 py-3 bg-blue-500 hover:bg-blue-600 rounded-lg font-bold transition"
            >
              Client Login
            </Link>
          </div>
        </motion.div>

        {/* Features Section */}
        <motion.div
          initial={{ opacity: 0, y: 40 }}
          whileInView={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.8 }}
          className="grid md:grid-cols-3 gap-8 py-20"
        >
          {[
            {
              title: '📁 Portfolio',
              desc: 'Showcase your amazing projects'
            },
            {
              title: '👥 Clients',
              desc: 'Manage all your clients effortlessly'
            },
            {
              title: '📊 Dashboard',
              desc: 'Track analytics and performance'
            }
          ].map((feature, i) => (
            <motion.div
              key={i}
              whileHover={{ scale: 1.05 }}
              className="p-8 bg-white/10 backdrop-blur rounded-xl border border-white/20"
            >
              <h3 className="text-2xl font-bold mb-3">{feature.title}</h3>
              <p className="text-gray-300">{feature.desc}</p>
            </motion.div>
          ))}
        </motion.div>
      </div>
    </div>
  );
}
```

---

## 📄 Frontend - src/pages/LoginAdmin.jsx

```javascript
import { useState, useContext } from 'react';
import { useNavigate } from 'react-router-dom';
import { AuthContext } from '../context/AuthContext';
import toast from 'react-hot-toast';
import { motion } from 'framer-motion';

export default function LoginAdmin() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [loading, setLoading] = useState(false);
  const { login } = useContext(AuthContext);
  const navigate = useNavigate();

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);
    
    try {
      const success = await login(email, password, 'admin');
      if (success) {
        toast.success('Login successful!');
        navigate('/admin');
      } else {
        toast.error('Login failed');
      }
    } catch (error) {
      toast.error('An error occurred');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-slate-900 to-slate-800 flex items-center justify-center p-4">
      <motion.div
        initial={{ opacity: 0, scale: 0.95 }}
        animate={{ opacity: 1, scale: 1 }}
        className="w-full max-w-md bg-white/10 backdrop-blur-xl rounded-2xl border border-white/20 p-8"
      >
        <h2 className="text-3xl font-bold text-white mb-8 text-center">Admin Login</h2>
        
        <form onSubmit={handleSubmit} className="space-y-6">
          <div>
            <label className="block text-white mb-2">Email</label>
            <input
              type="email"
              value={email}
              onChange={(e) => setEmail(e.target.value)}
              className="w-full px-4 py-2 bg-white/10 border border-white/20 rounded-lg text-white placeholder-gray-400 focus:outline-none focus:border-pink-500"
              placeholder="admin@example.com"
              required
            />
          </div>
          
          <div>
            <label className="block text-white mb-2">Password</label>
            <input
              type="password"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              className="w-full px-4 py-2 bg-white/10 border border-white/20 rounded-lg text-white placeholder-gray-400 focus:outline-none focus:border-pink-500"
              placeholder="••••••••"
              required
            />
          </div>
          
          <button
            type="submit"
            disabled={loading}
            className="w-full py-2 bg-gradient-to-r from-pink-500 to-blue-500 text-white font-bold rounded-lg hover:shadow-lg transition disabled:opacity-50"
          >
            {loading ? 'Logging in...' : 'Login'}
          </button>
        </form>
      </motion.div>
    </div>
  );
}
```

---

## 📄 Frontend - src/components/Admin/AdminDashboard.jsx

```javascript
import { useState, useContext } from 'react';
import { Routes, Route, Link } from 'react-router-dom';
import { AuthContext } from '../../context/AuthContext';
import ProjectManager from './ProjectManager';
import ClientManager from './ClientManager';
import Analytics from './Analytics';

export default function AdminDashboard() {
  const { user, logout } = useContext(AuthContext);
  const [activeTab, setActiveTab] = useState('projects');

  return (
    <div className="min-h-screen bg-slate-900">
      {/* Sidebar */}
      <div className="fixed left-0 top-0 w-64 h-screen bg-slate-800 border-r border-white/10 p-6 z-50">
        <h2 className="text-2xl font-bold text-white mb-8">Admin</h2>
        
        <nav className="space-y-4">
          {[
            { id: 'projects', label: 'Projects', icon: '📁' },
            { id: 'clients', label: 'Clients', icon: '👥' },
            { id: 'analytics', label: 'Analytics', icon: '📊' },
          ].map(item => (
            <Link
              key={item.id}
              to={`/admin/${item.id}`}
              onClick={() => setActiveTab(item.id)}
              className={`block px-4 py-3 rounded-lg transition ${
                activeTab === item.id
                  ? 'bg-pink-500 text-white'
                  : 'text-gray-300 hover:bg-white/10'
              }`}
            >
              {item.icon} {item.label}
            </Link>
          ))}
        </nav>
        
        <div className="absolute bottom-6 left-6 right-6">
          <p className="text-gray-400 mb-4">Logged in as: {user?.name}</p>
          <button
            onClick={logout}
            className="w-full py-2 bg-red-500 text-white rounded-lg hover:bg-red-600 transition"
          >
            Logout
          </button>
        </div>
      </div>

      {/* Main Content */}
      <div className="ml-64 p-8">
        <Routes>
          <Route path="/projects" element={<ProjectManager />} />
          <Route path="/clients" element={<ClientManager />} />
          <Route path="/analytics" element={<Analytics />} />
          <Route path="/" element={<ProjectManager />} />
        </Routes>
      </div>
    </div>
  );
}
```

---

## 📄 Frontend - src/.env.example

```
VITE_API_URL=http://localhost:5000
```

---

## 🚀 QUICK DEPLOYMENT GUIDE

### Deploy to Vercel (Fastest - 2 minutes)

```bash
# 1. Install Vercel CLI
npm i -g vercel

# 2. Go to frontend folder
cd frontend

# 3. Deploy
vercel --prod

# 4. Follow prompts to connect GitHub
# 5. Done! Your app is live
```

### Deploy to Netlify (Drag & Drop)

```bash
# 1. Build the project
npm run build

# 2. Go to netlify.com
# 3. Drag & drop the 'build' folder
# 4. Done!
```

### Deploy Backend to Railway

```bash
# 1. Create account at railway.app
# 2. Connect your GitHub
# 3. Create new project
# 4. Select Nodejs
# 5. Add variables from .env
# 6. Deploy!
```

---

## 🎯 MINIMAL SETUP (10 minutes)

1. **Download files**
   ```bash
   git clone <your-repo>
   ```

2. **Backend Setup**
   ```bash
   cd backend
   npm install
   cp .env.example .env
   # Edit .env with MongoDB URI
   npm start
   ```

3. **Frontend Setup**
   ```bash
   cd frontend
   npm install
   cp .env.example .env
   npm run dev
   ```

4. **Test Locally**
   - Frontend: http://localhost:3000
   - Backend: http://localhost:5000/api/health

5. **Deploy**
   - Frontend: `vercel --prod`
   - Backend: Use Railway or Render

---

## ✅ FINAL CHECKLIST

- [ ] All files downloaded
- [ ] Backend .env configured
- [ ] Frontend .env configured
- [ ] MongoDB connection working
- [ ] `npm start` backend works
- [ ] `npm run dev` frontend works
- [ ] 3D animation displays
- [ ] Login pages work
- [ ] Admin dashboard accessible
- [ ] Client portal accessible
- [ ] Frontend deployed to Vercel
- [ ] Backend deployed to Railway
- [ ] Domain connected
- [ ] HTTPS enabled

**You're all set! Your startup platform is ready!** 🎉

Need more features? I can add:
- Payment integration (Stripe)
- Email notifications
- File uploads
- Real-time notifications
- Team collaboration tools
