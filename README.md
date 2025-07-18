# QuikTalk Chat App

A real-time chat application built with React and Node.js featuring instant messaging, file sharing, and user authentication.

## 🌐 Live Demo

**[Try QuikTalk Chat App Live](https://quiktalk-chat-app.onrender.com)**

Experience the app in action at: https://quiktalk-chat-app.onrender.com

## 📋 Table of Contents

- [Live Demo](#live-demo)
- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Application](#running-the-application)
- [API Documentation](#api-documentation)
- [Socket Events](#socket-events)
- [Database Schema](#database-schema)
- [Contributing](#contributing)
- [License](#license)

## 🔍 Overview

QuikTalk is a modern, responsive chat application that enables users to communicate in real-time. Built with a React frontend and Express.js backend, it utilizes Socket.io for instant messaging and MongoDB for data persistence.

## ✨ Features

- **Real-time Messaging**: Instant message delivery using WebSocket connections
- **User Authentication**: Secure JWT-based authentication system
- **Profile Management**: User profile creation and customization
- **File Sharing**: Upload and share files in conversations
- **Contact Management**: Search and add contacts for direct messaging
- **Responsive Design**: Mobile-friendly interface built with Tailwind CSS
- **Modern UI**: Clean interface using Radix UI components
- **Dark/Light Theme**: Theme switching support
- **Emoji Support**: Emoji picker for enhanced messaging
- **Avatar System**: Custom avatars with color themes

## 🛠 Tech Stack

### Frontend

- **React 18** - JavaScript library for building user interfaces
- **Vite** - Fast build tool and development server
- **React Router** - Client-side routing
- **Tailwind CSS** - Utility-first CSS framework
- **Radix UI** - Headless UI component library
- **Zustand** - State management
- **Socket.io Client** - Real-time communication
- **Axios** - HTTP client
- **Lucide React** - Icon library
- **React Lottie** - Animation library

### Backend

- **Node.js** - JavaScript runtime
- **Express.js** - Web application framework
- **Socket.io** - Real-time bidirectional communication
- **MongoDB** - NoSQL database
- **Mongoose** - MongoDB object modeling
- **JWT** - JSON Web Tokens for authentication
- **bcrypt** - Password hashing
- **Multer** - File upload handling
- **Zod** - Schema validation

## 📁 Project Structure

```
QuikTalk-Chat-App/
├── client/                          # Frontend application
│   ├── public/                      # Static assets
│   ├── src/
│   │   ├── assets/                  # Images and animations
│   │   ├── components/              # Reusable UI components
│   │   │   ├── ui/                  # Base UI components
│   │   │   └── contact-list.jsx     # Contact management
│   │   ├── context/                 # React context providers
│   │   │   └── SocketContext.jsx    # Socket.io context
│   │   ├── lib/                     # Utility libraries
│   │   │   ├── api-client.js        # API client configuration
│   │   │   └── utils.js             # Helper functions
│   │   ├── pages/                   # Application pages
│   │   │   ├── auth/                # Authentication pages
│   │   │   ├── chat/                # Chat interface
│   │   │   └── profile/             # User profile
│   │   ├── store/                   # State management
│   │   │   ├── index.js             # Store configuration
│   │   │   └── slices/              # State slices
│   │   └── utils/
│   │       └── constants.js         # API endpoints and constants
│   ├── package.json
│   └── vite.config.js
├── server/                          # Backend application
│   ├── controllers/                 # Route controllers
│   │   ├── AuthController.js        # Authentication logic
│   │   ├── ContactController.js     # Contact management
│   │   └── MessagesController.js    # Message handling
│   ├── middlewares/                 # Express middlewares
│   │   └── AuthMiddleware.js        # JWT authentication
│   ├── models/                      # Database models
│   │   ├── UserModel.js             # User schema
│   │   └── MessagesModel.js         # Message schema
│   ├── routes/                      # API routes
│   │   ├── AuthRoutes.js            # Authentication routes
│   │   ├── ContactRoutes.js         # Contact routes
│   │   └── MessagesRoutes.js        # Message routes
│   ├── uploads/                     # File upload storage
│   │   ├── files/                   # Shared files
│   │   └── profiles/                # Profile images
│   ├── index.js                     # Server entry point
│   ├── socket.js                    # Socket.io configuration
│   └── package.json
└── README.md
```

## 📋 Prerequisites

Before running this application, make sure you have the following installed:

- **Node.js** (v16 or higher)
- **npm** or **yarn**
- **MongoDB** (local installation or MongoDB Atlas)

## 🚀 Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/Explore13/QuikTalk-Chat-App.git
   cd QuikTalk-Chat-App
   ```

2. **Install server dependencies**

   ```bash
   cd server
   npm install
   ```

3. **Install client dependencies**
   ```bash
   cd ../client
   npm install
   ```

## ⚙️ Configuration

### Server Configuration

Create a `.env` file in the `server` directory with the following variables:

```env
# Server Configuration
PORT=3001
NODE_ENV=development

# Database
DATABASE_URL=mongodb://localhost:27017/quiktalk
# Or for MongoDB Atlas:
# DATABASE_URL=mongodb+srv://username:password@cluster.mongodb.net/quiktalk

# JWT Configuration
JWT_KEY=your-super-secret-jwt-key

# CORS
ORIGIN=http://localhost:5173

# File Upload (optional)
MAX_FILE_SIZE=5242880  # 5MB in bytes
```

### Client Configuration

Create a `.env` file in the `client` directory:

```env
# API Configuration
VITE_SERVER_URL=http://localhost:3001
```

## 🏃‍♂️ Running the Application

### Development Mode

1. **Start the server**

   ```bash
   cd server
   npm run dev
   ```

   The server will start on `http://localhost:3001`

2. **Start the client** (in a new terminal)
   ```bash
   cd client
   npm run dev
   ```
   The client will start on `http://localhost:5173`

### Production Mode

1. **Build the client**

   ```bash
   cd client
   npm run build
   ```

2. **Start the server**
   ```bash
   cd server
   npm start
   ```

## 📡 API Documentation

### Authentication Endpoints

| Method | Endpoint                         | Description          | Body                             |
| ------ | -------------------------------- | -------------------- | -------------------------------- |
| POST   | `/api/auth/signup`               | Register new user    | `{ email, password }`            |
| POST   | `/api/auth/login`                | User login           | `{ email, password }`            |
| GET    | `/api/auth/user-info`            | Get user profile     | -                                |
| POST   | `/api/auth/update-profile`       | Update user profile  | `{ firstName, lastName, color }` |
| POST   | `/api/auth/add-profile-image`    | Upload profile image | FormData                         |
| DELETE | `/api/auth/remove-profile-image` | Remove profile image | -                                |
| POST   | `/api/auth/logout`               | User logout          | -                                |

### Contact Endpoints

| Method | Endpoint                            | Description         | Query            |
| ------ | ----------------------------------- | ------------------- | ---------------- |
| POST   | `/api/contacts/search`              | Search users        | `{ searchTerm }` |
| GET    | `/api/contacts/get-contacts-for-dm` | Get user's contacts | -                |

### Message Endpoints

| Method | Endpoint                     | Description               | Body     |
| ------ | ---------------------------- | ------------------------- | -------- |
| POST   | `/api/messages/get-messages` | Get conversation messages | `{ id }` |
| POST   | `/api/messages/upload-file`  | Upload file               | FormData |

## 🔌 Socket Events

### Client to Server Events

- `sendMessage` - Send a new message
- `disconnect` - User disconnection

### Server to Client Events

- `receiveMessage` - Receive new message
- `connection` - Successful connection

### Message Structure

```javascript
{
  sender: ObjectId,           // Sender's user ID
  recipient: ObjectId,        // Recipient's user ID
  messageType: "text|file",   // Message type
  content: String,            // Text content (for text messages)
  fileUrl: String,           // File URL (for file messages)
  timestamp: Date            // Message timestamp
}
```

## 🗄️ Database Schema

### User Model

```javascript
{
  email: String (required, unique),
  password: String (required, hashed),
  firstName: String,
  lastName: String,
  image: String,              // Profile image URL
  color: Number,              // Avatar color theme
  profileSetup: Boolean,      // Profile completion status
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

## 🛡️ Security Features

- **JWT Authentication**: Secure token-based authentication
- **Password Hashing**: bcrypt for secure password storage
- **CORS Protection**: Configured for specific origins
- **Input Validation**: Zod schema validation
- **File Upload Security**: File type and size restrictions
- **Authentication Middleware**: Protected route access

## 🎨 UI Components

The application uses a combination of custom and Radix UI components:

- **Avatar**: User profile pictures with fallbacks
- **Button**: Customizable button components
- **Dialog**: Modal dialogs for user interactions
- **Input**: Form input fields
- **ScrollArea**: Scrollable content areas
- **Tabs**: Tabbed interfaces
- **Tooltip**: Helpful tooltips

## 📱 Responsive Design

The application is fully responsive and works on:

- Desktop computers
- Tablets
- Mobile phones

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🐛 Troubleshooting

### Common Issues

1. **Connection Issues**

   - Ensure MongoDB is running
   - Check environment variables
   - Verify CORS settings

2. **File Upload Issues**

   - Check file size limits
   - Verify upload directory permissions
   - Ensure multer configuration is correct

3. **Socket Connection Issues**
   - Verify server is running
   - Check CORS configuration
   - Ensure client is connecting to correct URL

### Support

For support, please open an issue on the GitHub repository or contact the development team.

---

**Built with ❤️ by the Surya Ghosh**
