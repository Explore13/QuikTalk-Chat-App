# QuikTalk Chat App - Server

The backend API server for QuikTalk Chat App, built with Node.js, Express, Socket.io, and MongoDB.

## 🚀 Quick Start

```bash
# Install dependencies
npm install

# Start development server (with nodemon)
npm run dev

# Start production server
npm start
```

## 🛠 Tech Stack

- **Node.js** - JavaScript runtime
- **Express.js** - Web application framework
- **Socket.io** - Real-time bidirectional communication
- **MongoDB** - NoSQL database
- **Mongoose** - MongoDB object modeling
- **JWT** - JSON Web Tokens for authentication
- **bcrypt** - Password hashing
- **Multer** - File upload handling
- **Zod** - Schema validation
- **CORS** - Cross-origin resource sharing

## 📁 Project Structure

```
server/
├── controllers/         # Route handlers and business logic
│   ├── AuthController.js      # Authentication operations
│   ├── ContactController.js   # Contact management
│   └── MessagesController.js  # Message handling
├── models/             # Database schemas
│   ├── UserModel.js           # User data model
│   └── MessagesModel.js       # Message data model
├── routes/             # API route definitions
│   ├── AuthRoutes.js          # Authentication routes
│   ├── ContactRoutes.js       # Contact routes
│   └── MessagesRoutes.js      # Message routes
├── middlewares/        # Express middlewares
│   └── AuthMiddleware.js      # JWT authentication
├── uploads/            # File storage
│   ├── files/                 # Shared files
│   └── profiles/              # Profile images
├── index.js            # Server entry point
├── socket.js           # Socket.io configuration
└── package.json
```

## 🔧 Environment Variables

Create a `.env` file in the server directory:

```env
# Server Configuration
PORT=3001
NODE_ENV=development

# Database
DATABASE_URL=mongodb://localhost:27017/quiktalk
# Or MongoDB Atlas:
# DATABASE_URL=mongodb+srv://username:password@cluster.mongodb.net/quiktalk

# JWT Configuration
JWT_KEY=your-super-secret-jwt-key

# CORS
ORIGIN=http://localhost:5173

# Optional Configuration
MAX_FILE_SIZE=5242880  # 5MB in bytes
```

## 🗄️ Database Models

### User Model

```javascript
{
  email: String (required, unique),
  password: String (required, hashed),
  firstName: String,
  lastName: String,
  image: String,              // Profile image path
  color: Number,              // Avatar color theme (0-4)
  profileSetup: Boolean,      // Whether profile is complete
  timestamps: true
}
```

### Message Model

```javascript
{
  sender: ObjectId (ref: Users),
  recipient: ObjectId (ref: Users),
  messageType: "text" | "file",
  content: String,            // Required for text messages
  fileUrl: String,           // Required for file messages
  timestamp: Date
}
```

## 📡 API Endpoints

### Authentication Routes (`/api/auth`)

- `POST /signup` - Register new user
- `POST /login` - User login
- `GET /user-info` - Get current user info
- `POST /update-profile` - Update user profile
- `POST /add-profile-image` - Upload profile image
- `DELETE /remove-profile-image` - Remove profile image
- `POST /logout` - User logout

### Contact Routes (`/api/contacts`)

- `POST /search` - Search for users
- `GET /get-contacts-for-dm` - Get user's contact list

### Message Routes (`/api/messages`)

- `POST /get-messages` - Get conversation messages
- `POST /upload-file` - Upload file attachment

## 🔌 Socket.io Events

### Connection Events

- `connection` - User connects
- `disconnect` - User disconnects

### Message Events

- `sendMessage` - Send a message
- `receiveMessage` - Receive a message

## 🛡️ Security Features

- **JWT Authentication** - Secure token-based auth
- **Password Hashing** - bcrypt with salt
- **CORS Protection** - Configured origins
- **Input Validation** - Zod schema validation
- **File Upload Security** - Type and size limits
- **HTTP-only Cookies** - Secure token storage

## 📂 File Upload

### Configuration

- **Profile Images**: 5MB max, images only
- **Chat Files**: 10MB max, multiple types
- **Storage**: Local filesystem in `uploads/` directory
- **URL Access**: Served as static files

### Supported File Types

- **Images**: jpg, jpeg, png, gif, webp
- **Documents**: pdf, txt, doc, docx
- **Archives**: zip, rar

## 🚦 Middleware

### Authentication Middleware

```javascript
// Protects routes requiring authentication
const verifyToken = (req, res, next) => {
  const token = req.cookies.jwt;
  // Validates JWT and sets req.userId
};
```

### CORS Middleware

```javascript
// Configured for specific origins
app.use(
  cors({
    origin: [process.env.ORIGIN],
    methods: ["GET", "POST", "PUT", "PATCH", "DELETE"],
    credentials: true,
  })
);
```

## 🔧 Development

### Database Setup

```bash
# Start MongoDB locally
mongod

# Or use MongoDB Atlas cloud service
# Update DATABASE_URL in .env
```

### Hot Reload

The development server uses nodemon for automatic restart on file changes.

### Debugging

```bash
# Enable debug output
DEBUG=* npm run dev

# Or specific modules
DEBUG=socket.io:* npm run dev
```

## 📊 Available Scripts

- `npm start` - Start production server
- `npm run dev` - Start with nodemon (development)

## 🏗️ Production Deployment

### Environment Setup

- Set `NODE_ENV=production`
- Use secure JWT keys
- Configure production database
- Set up proper CORS origins
- Enable HTTPS

### Process Management

Consider using PM2 for production:

```bash
npm install -g pm2
pm2 start index.js --name quiktalk-server
pm2 startup
pm2 save
```

## 🧪 Testing

```bash
# Run tests (when implemented)
npm test

# Test API endpoints
curl -X POST http://localhost:3001/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}'
```

## 📈 Monitoring

### Health Check

```bash
# Check server health
curl http://localhost:3001/health
```

### Logs

Monitor application logs and database connections for debugging and performance insights.

## 🤝 Contributing

See the main project README for contribution guidelines.

## 📄 License

This project is part of the QuikTalk Chat App. See the main project for license information.
