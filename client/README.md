# QuikTalk Chat App - Client

The frontend application for QuikTalk Chat App, built with React, Vite, and modern web technologies.

## 🚀 Quick Start

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## 🛠 Tech Stack

- **React 18** - Modern React with hooks
- **Vite** - Fast build tool and dev server
- **Tailwind CSS** - Utility-first CSS framework
- **Radix UI** - Headless UI components
- **Zustand** - Lightweight state management
- **Socket.io Client** - Real-time communication
- **React Router** - Client-side routing
- **Axios** - HTTP client
- **Lucide React** - Beautiful icons

## 📁 Project Structure

```
src/
├── components/          # Reusable UI components
│   ├── ui/             # Base UI components
│   └── contact-list.jsx
├── pages/              # Route components
│   ├── auth/           # Authentication
│   ├── chat/           # Chat interface
│   └── profile/        # User profile
├── store/              # State management
├── context/            # React contexts
├── lib/                # Utilities
└── utils/              # Constants and helpers
```

## 🎨 Features

- **Real-time Messaging** - Instant chat with Socket.io
- **Modern UI** - Beautiful interface with Tailwind CSS
- **Responsive Design** - Works on all devices
- **File Sharing** - Upload and share files
- **Emoji Support** - Rich emoji picker
- **Dark/Light Theme** - Theme switching support
- **Profile Management** - Customizable user profiles

## 🔧 Environment Variables

Create a `.env` file in the client directory:

```env
VITE_SERVER_URL=http://localhost:3001
```

## 📖 Available Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run lint` - Run ESLint

## 🏗 Build Configuration

The project uses Vite with the following plugins:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) - Fast Refresh with Babel
- Built-in PostCSS support for Tailwind CSS
- Path aliases configured for clean imports

## 📱 Browser Support

- Chrome/Edge 88+
- Firefox 78+
- Safari 14+
- Modern mobile browsers

## 🤝 Contributing

See the main project README for contribution guidelines.

## 📄 License

This project is part of the QuikTalk Chat App. See the main project for license information.
