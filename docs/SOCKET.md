# Socket.io Events Documentation

## Connection Setup

### Client Connection

```javascript
import { io } from "socket.io-client";

const socket = io(SERVER_URL, {
  withCredentials: true,
  query: {
    userId: currentUser.id,
  },
});
```

### Server Configuration

```javascript
const io = new SocketIOServer(server, {
  cors: {
    origin: process.env.ORIGIN,
    methods: ["GET", "POST"],
    credentials: true,
  },
});
```

## Events

### Connection Events

#### `connection`

**Direction:** Server → Client  
**Description:** Fired when a client successfully connects to the server.

**Server Handler:**

```javascript
io.on("connection", (socket) => {
  const userId = socket.handshake.query.userId;
  if (userId) {
    userSocketMap.set(userId, socket.id);
    console.log(`User connected: ${userId} with socket ID: ${socket.id}`);
  }
});
```

#### `disconnect`

**Direction:** Client → Server  
**Description:** Fired when a client disconnects from the server.

**Server Handler:**

```javascript
socket.on("disconnect", () => {
  disconnect(socket);
});

const disconnect = (socket) => {
  console.log(`Client Disconnected: ${socket.id}`);
  for (const [userId, socketId] of userSocketMap.entries()) {
    if (socketId === socket.id) {
      userSocketMap.delete(userId);
      break;
    }
  }
};
```

### Message Events

#### `sendMessage`

**Direction:** Client → Server  
**Description:** Send a new message to another user.

**Client Usage:**

```javascript
socket.emit("sendMessage", {
  sender: currentUser.id,
  recipient: recipientUser.id,
  messageType: "text",
  content: "Hello, how are you?",
  fileUrl: null, // for text messages
});
```

**Message Object Structure:**

```javascript
{
  sender: String,        // ObjectId of sender
  recipient: String,     // ObjectId of recipient
  messageType: String,   // "text" or "file"
  content: String,       // Message content (required for text)
  fileUrl: String        // File URL (required for file messages)
}
```

**Server Handler:**

```javascript
socket.on("sendMessage", sendMessage);

const sendMessage = async (message) => {
  const senderSocketId = userSocketMap.get(message.sender);
  const recipientSocketId = userSocketMap.get(message.recipient);

  // Save message to database
  const createdMessage = await Message.create(message);

  // Populate sender and recipient details
  const messageData = await Message.findById(createdMessage._id)
    .populate("sender", "id email firstName lastName image color")
    .populate("recipient", "id email firstName lastName image color");

  // Send to both sender and recipient if online
  if (recipientSocketId) {
    io.to(recipientSocketId).emit("receiveMessage", messageData);
  }
  if (senderSocketId) {
    io.to(senderSocketId).emit("receiveMessage", messageData);
  }
};
```

#### `receiveMessage`

**Direction:** Server → Client  
**Description:** Receive a new message from another user.

**Client Handler:**

```javascript
socket.on("receiveMessage", (message) => {
  // Add message to the conversation
  addMessage(message);

  // Update UI
  updateChatInterface(message);

  // Show notification if chat is not active
  if (!isChatActive) {
    showNotification(message);
  }
});
```

**Message Data Structure:**

```javascript
{
  _id: String,
  sender: {
    id: String,
    email: String,
    firstName: String,
    lastName: String,
    image: String,
    color: Number
  },
  recipient: {
    id: String,
    email: String,
    firstName: String,
    lastName: String,
    image: String,
    color: Number
  },
  messageType: String,   // "text" or "file"
  content: String,       // Message content (for text messages)
  fileUrl: String,       // File URL (for file messages)
  timestamp: Date
}
```

## User Management

### User Socket Mapping

The server maintains a Map to track online users and their socket connections:

```javascript
const userSocketMap = new Map();

// Add user on connection
userSocketMap.set(userId, socket.id);

// Remove user on disconnection
userSocketMap.delete(userId);

// Get user's socket ID
const socketId = userSocketMap.get(userId);
```

### Online Status

Currently, the application doesn't explicitly broadcast online/offline status, but this can be implemented using the user socket mapping.

## Error Handling

### Connection Errors

```javascript
socket.on("connect_error", (error) => {
  console.error("Connection failed:", error);
  // Handle connection failure
});
```

### Message Delivery

Messages are delivered to users who are currently online. Offline message handling would require additional implementation for:

- Storing messages for offline users
- Sending missed messages on reconnection
- Push notifications

## Security Considerations

### Authentication

- User ID is passed via query parameters during connection
- Consider implementing socket authentication middleware for enhanced security

### Authorization

- Messages are only sent to intended recipients
- Socket rooms could be implemented for group chats

### Rate Limiting

- Consider implementing rate limiting for message sending
- Prevent spam and abuse

## Future Enhancements

### Typing Indicators

```javascript
// Client sends typing status
socket.emit("typing", { recipient: recipientId, isTyping: true });

// Server broadcasts to recipient
socket.on("typing", (data) => {
  const recipientSocketId = userSocketMap.get(data.recipient);
  if (recipientSocketId) {
    io.to(recipientSocketId).emit("userTyping", {
      sender: socket.handshake.query.userId,
      isTyping: data.isTyping,
    });
  }
});
```

### Message Read Receipts

```javascript
// Client confirms message read
socket.emit("messageRead", { messageId: messageId });

// Server updates message status and notifies sender
socket.on("messageRead", async (data) => {
  await Message.findByIdAndUpdate(data.messageId, { read: true });
  // Notify sender about read status
});
```

### Group Chat Support

```javascript
// Join/leave rooms for group chats
socket.join(`room_${groupId}`);
socket.leave(`room_${groupId}`);

// Broadcast to room
io.to(`room_${groupId}`).emit("groupMessage", messageData);
```

### Presence/Status Updates

```javascript
// Broadcast user status changes
socket.emit("statusUpdate", { status: "online" });
io.emit("userStatusUpdate", { userId, status });
```
