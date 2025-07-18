# Development Guide

## Getting Started

### Development Environment Setup

1. **Clone and Install**

   ```bash
   git clone https://github.com/Explore13/QuikTalk-Chat-App.git
   cd QuikTalk-Chat-App

   # Install server dependencies
   cd server && npm install

   # Install client dependencies
   cd ../client && npm install
   ```

2. **Environment Configuration**

   **Server (.env):**

   ```env
   PORT=3001
   DATABASE_URL=mongodb://localhost:27017/quiktalk
   JWT_KEY=your-development-jwt-key
   ORIGIN=http://localhost:5173
   NODE_ENV=development
   ```

   **Client (.env):**

   ```env
   VITE_SERVER_URL=http://localhost:3001
   ```

3. **Database Setup**

   ```bash
   # Start MongoDB (if running locally)
   mongod

   # Or use MongoDB Atlas cloud database
   # Update DATABASE_URL in server/.env with your Atlas connection string
   ```

## Architecture Overview

### Frontend Architecture

The client follows a component-based architecture with clear separation of concerns:

```
src/
├── components/          # Reusable UI components
│   ├── ui/             # Base UI components (Radix UI based)
│   └── contact-list.jsx # Feature-specific components
├── pages/              # Route-based page components
│   ├── auth/           # Authentication flow
│   ├── chat/           # Main chat interface
│   └── profile/        # User profile management
├── store/              # Zustand state management
├── context/            # React context providers
├── lib/                # Utility libraries
└── utils/              # Helper functions and constants
```

### Backend Architecture

The server follows a RESTful API design with real-time capabilities:

```
server/
├── controllers/        # Business logic for routes
├── models/            # Database schemas and models
├── routes/            # API route definitions
├── middlewares/       # Custom Express middlewares
├── uploads/           # File storage
├── socket.js          # Socket.io configuration
└── index.js           # Server entry point
```

## Development Workflow

### 1. Feature Development

1. **Create Feature Branch**

   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Frontend Development**

   ```bash
   cd client
   npm run dev
   ```

3. **Backend Development**
   ```bash
   cd server
   npm run dev  # Uses nodemon for auto-restart
   ```

### 2. Code Style and Standards

#### Frontend Standards

- Use functional components with hooks
- Follow React naming conventions (PascalCase for components)
- Use Tailwind CSS for styling
- Implement proper prop validation
- Keep components small and focused

#### Backend Standards

- Use ES6+ module syntax
- Follow RESTful API conventions
- Implement proper error handling
- Use middleware for cross-cutting concerns
- Validate input data with Zod

#### File Naming Conventions

- React components: `PascalCase.jsx`
- JavaScript files: `camelCase.js`
- Constant files: `UPPER_CASE.js`
- Directories: `kebab-case`

### 3. State Management

#### Client State (Zustand)

```javascript
// store/slices/auth-slice.js
export const createAuthSlice = (set, get) => ({
  userInfo: undefined,
  setUserInfo: (userInfo) => set({ userInfo }),
  // ... other auth actions
});
```

#### Socket State Management

```javascript
// context/SocketContext.jsx
const SocketContext = createContext();

export const useSocket = () => {
  return useContext(SocketContext);
};
```

## Database Design

### User Collection

```javascript
{
  _id: ObjectId,
  email: String (unique, required),
  password: String (hashed, required),
  firstName: String,
  lastName: String,
  image: String,
  color: Number,
  profileSetup: Boolean,
  createdAt: Date,
  updatedAt: Date
}
```

### Messages Collection

```javascript
{
  _id: ObjectId,
  sender: ObjectId (ref: Users),
  recipient: ObjectId (ref: Users),
  messageType: String (enum: ["text", "file"]),
  content: String,
  fileUrl: String,
  timestamp: Date
}
```

## API Development Guidelines

### Error Handling

```javascript
// Consistent error response format
res.status(400).json({
  success: false,
  message: "Error message",
  errors: validationErrors, // optional
});

// Success response format
res.status(200).json({
  success: true,
  data: responseData,
  message: "Success message", // optional
});
```

### Input Validation with Zod

```javascript
import { z } from "zod";

const signupSchema = z.object({
  email: z.string().email(),
  password: z.string().min(6),
});

// In controller
const validatedData = signupSchema.parse(req.body);
```

### Authentication Middleware

```javascript
// middlewares/AuthMiddleware.js
export const verifyToken = (req, res, next) => {
  const token = req.cookies.jwt;
  if (!token) {
    return res.status(401).json({ message: "Token is required" });
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_KEY);
    req.userId = decoded.userId;
    next();
  } catch (error) {
    return res.status(403).json({ message: "Invalid token" });
  }
};
```

## Testing

### Unit Testing Setup

```bash
# Install testing dependencies
npm install --save-dev jest supertest

# Run tests
npm test
```

### Example Test Structure

```javascript
// tests/auth.test.js
describe("Authentication", () => {
  test("should register new user", async () => {
    const response = await request(app).post("/api/auth/signup").send({
      email: "test@example.com",
      password: "password123",
    });

    expect(response.status).toBe(201);
    expect(response.body.user.email).toBe("test@example.com");
  });
});
```

## Performance Optimization

### Frontend Optimization

- Use React.memo for component memoization
- Implement virtual scrolling for long message lists
- Optimize bundle size with code splitting
- Use lazy loading for routes

### Backend Optimization

- Implement database indexing
- Use connection pooling for MongoDB
- Add caching layer (Redis)
- Optimize file upload handling

### Socket.io Optimization

- Implement connection clustering
- Use Redis adapter for scaling
- Optimize message payload size
- Implement message batching

## Security Considerations

### Authentication Security

```javascript
// JWT configuration
const jwtOptions = {
  httpOnly: true, // Prevent XSS
  secure: process.env.NODE_ENV === "production",
  sameSite: "strict", // CSRF protection
  maxAge: 7 * 24 * 60 * 60 * 1000, // 7 days
};
```

### File Upload Security

```javascript
// Multer configuration
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, "uploads/files/");
  },
  filename: (req, file, cb) => {
    const uniqueName = Date.now() + "-" + Math.round(Math.random() * 1e9);
    cb(null, uniqueName + path.extname(file.originalname));
  },
});

const fileFilter = (req, file, cb) => {
  const allowedTypes = ["image/jpeg", "image/png", "image/gif"];
  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true);
  } else {
    cb(new Error("Invalid file type"), false);
  }
};
```

## Deployment

### Production Build

```bash
# Build client
cd client
npm run build

# The build files will be in client/dist/
```

### Environment Variables for Production

```env
# Server production .env
NODE_ENV=production
PORT=3001
DATABASE_URL=mongodb+srv://username:password@cluster.mongodb.net/quiktalk
JWT_KEY=super-secure-production-key
ORIGIN=https://your-domain.com
```

### Docker Setup

```dockerfile
# Dockerfile.server
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3001
CMD ["node", "index.js"]
```

## Monitoring and Logging

### Error Logging

```javascript
// Add winston or similar logging library
import winston from "winston";

const logger = winston.createLogger({
  level: "info",
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: "error.log", level: "error" }),
    new winston.transports.File({ filename: "combined.log" }),
  ],
});
```

### Health Check Endpoint

```javascript
app.get("/health", (req, res) => {
  res.status(200).json({
    status: "OK",
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
  });
});
```

## Contributing Guidelines

1. **Code Review Process**

   - All changes must be reviewed
   - Write descriptive commit messages
   - Include tests for new features

2. **Branch Naming**

   - `feature/feature-name`
   - `bugfix/bug-description`
   - `hotfix/critical-fix`

3. **Pull Request Template**
   - Description of changes
   - Testing performed
   - Screenshots (if UI changes)
   - Breaking changes (if any)
