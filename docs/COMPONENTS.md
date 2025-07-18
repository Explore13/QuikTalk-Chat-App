# Component Documentation

## Overview

This document provides detailed information about the React components used in the QuikTalk Chat App client application.

## Component Architecture

The application follows a component-based architecture with the following structure:

```
src/components/
├── ui/                    # Base UI components (Radix UI based)
│   ├── avatar.jsx
│   ├── button.jsx
│   ├── dialog.jsx
│   ├── input.jsx
│   ├── scroll-area.jsx
│   ├── sonner.jsx
│   ├── tabs.jsx
│   └── tooltip.jsx
└── contact-list.jsx       # Feature-specific components
```

## Base UI Components

### Avatar Component

**File:** `src/components/ui/avatar.jsx`

A flexible avatar component with fallback support.

```jsx
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar";

// Usage
<Avatar>
  <AvatarImage src={user.image} alt={user.firstName} />
  <AvatarFallback>{user.firstName?.[0]}</AvatarFallback>
</Avatar>;
```

**Props:**

- Standard HTML div props
- Uses Radix UI Avatar primitives

**Features:**

- Automatic fallback when image fails to load
- Responsive sizing
- Accessibility support

### Button Component

**File:** `src/components/ui/button.jsx`

A versatile button component with multiple variants and sizes.

```jsx
import { Button } from "@/components/ui/button";

// Usage examples
<Button variant="default">Default Button</Button>
<Button variant="outline" size="sm">Small Outline</Button>
<Button variant="ghost" disabled>Disabled Ghost</Button>
```

**Variants:**

- `default` - Primary button style
- `destructive` - For dangerous actions
- `outline` - Outlined button
- `secondary` - Secondary styling
- `ghost` - Minimal styling
- `link` - Link appearance

**Sizes:**

- `default` - Standard size
- `sm` - Small
- `lg` - Large
- `icon` - Square icon button

### Dialog Component

**File:** `src/components/ui/dialog.jsx`

Modal dialog component for overlays and forms.

```jsx
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog";

// Usage
<Dialog>
  <DialogTrigger asChild>
    <Button>Open Dialog</Button>
  </DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Dialog Title</DialogTitle>
      <DialogDescription>Dialog description</DialogDescription>
    </DialogHeader>
    {/* Dialog content */}
  </DialogContent>
</Dialog>;
```

**Components:**

- `Dialog` - Root component
- `DialogTrigger` - Trigger element
- `DialogContent` - Modal content container
- `DialogHeader` - Header section
- `DialogTitle` - Title text
- `DialogDescription` - Description text

### Input Component

**File:** `src/components/ui/input.jsx`

Styled input field component.

```jsx
import { Input } from "@/components/ui/input";

// Usage
<Input
  type="email"
  placeholder="Enter your email"
  value={email}
  onChange={(e) => setEmail(e.target.value)}
/>;
```

**Features:**

- Consistent styling across the app
- Focus states and transitions
- Error state styling
- Accessibility support

### Scroll Area Component

**File:** `src/components/ui/scroll-area.jsx`

Custom scrollable area with styled scrollbars.

```jsx
import { ScrollArea } from "@/components/ui/scroll-area";

// Usage
<ScrollArea className="h-96 w-full">
  <div className="p-4">{/* Scrollable content */}</div>
</ScrollArea>;
```

**Features:**

- Custom scrollbar styling
- Smooth scrolling
- Touch-friendly on mobile
- Keyboard navigation support

### Tabs Component

**File:** `src/components/ui/tabs.jsx`

Tabbed interface component.

```jsx
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";

// Usage
<Tabs defaultValue="tab1">
  <TabsList>
    <TabsTrigger value="tab1">Tab 1</TabsTrigger>
    <TabsTrigger value="tab2">Tab 2</TabsTrigger>
  </TabsList>
  <TabsContent value="tab1">Content 1</TabsContent>
  <TabsContent value="tab2">Content 2</TabsContent>
</Tabs>;
```

**Components:**

- `Tabs` - Root container
- `TabsList` - Tab navigation
- `TabsTrigger` - Individual tab button
- `TabsContent` - Tab panel content

### Tooltip Component

**File:** `src/components/ui/tooltip.jsx`

Hover tooltip component.

```jsx
import {
  Tooltip,
  TooltipContent,
  TooltipProvider,
  TooltipTrigger,
} from "@/components/ui/tooltip";

// Usage
<TooltipProvider>
  <Tooltip>
    <TooltipTrigger asChild>
      <Button variant="outline">Hover me</Button>
    </TooltipTrigger>
    <TooltipContent>
      <p>Tooltip content</p>
    </TooltipContent>
  </Tooltip>
</TooltipProvider>;
```

## Page Components

### Authentication Page

**File:** `src/pages/auth/index.jsx`

Handles user login and registration.

**Features:**

- Animated background with Lottie
- Form validation
- Responsive design
- Victory animation on success

**State Management:**

- Form data (email, password, confirmPassword)
- UI state (isLogin/signup mode)
- Loading states

### Chat Page

**File:** `src/pages/chat/index.jsx`

Main chat interface with sidebar and chat area.

**Structure:**

```jsx
<div className="chat-container">
  <ContactsContainer />
  {selectedChatType ? <ChatContainer /> : <EmptyChatContainer />}
</div>
```

**Features:**

- Real-time messaging
- Contact management
- File sharing
- Message history

### Profile Page

**File:** `src/pages/profile/index.jsx`

User profile management interface.

**Features:**

- Profile image upload
- Personal information editing
- Color theme selection
- Form validation

## Chat Components

### Chat Container

**File:** `src/pages/chat/components/chat-container/index.jsx`

Main chat interface container.

**Sub-components:**

- `ChatHeader` - Contact info and actions
- `MessageContainer` - Message history display
- `MessageBar` - Message input and send

### Chat Header

**File:** `src/pages/chat/components/chat-container/components/chat-header/index.jsx`

Displays current chat contact information.

**Features:**

- Contact avatar and name
- Online status (future feature)
- Chat actions menu

### Message Container

**File:** `src/pages/chat/components/chat-container/components/message-container/index.jsx`

Scrollable message history display.

**Features:**

- Auto-scroll to latest message
- Message grouping by sender
- Timestamp display
- File message handling
- Loading states

**Message Types:**

- Text messages
- File attachments
- System messages (future)

### Message Bar

**File:** `src/pages/chat/components/chat-container/components/message-bar/index.jsx`

Message input interface.

**Features:**

- Text input with emoji support
- File attachment button
- Send button
- Emoji picker integration

**File Upload:**

- Drag and drop support
- File type validation
- Progress indication
- Error handling

### Contacts Container

**File:** `src/pages/chat/components/contacts-container/index.jsx`

Sidebar with contacts and profile management.

**Sub-components:**

- `ProfileInfo` - Current user profile
- `NewDM` - Add new contacts
- Contact list display

### New DM Component

**File:** `src/pages/chat/components/contacts-container/components/new-dm/index.jsx`

Interface for searching and adding new contacts.

**Features:**

- Real-time search
- Contact suggestions
- Add to contacts functionality

### Profile Info Component

**File:** `src/pages/chat/components/contacts-container/components/profile-info/index.jsx`

Current user profile display in sidebar.

**Features:**

- Profile image display
- Quick profile edit access
- Logout functionality

## Contact List Component

**File:** `src/components/contact-list.jsx`

Reusable contact list component.

**Features:**

- Contact search and filtering
- Avatar display with fallbacks
- Online status indicators
- Click handlers for chat initiation

**Props:**

```typescript
interface ContactListProps {
  contacts: Contact[];
  onContactSelect: (contact: Contact) => void;
  selectedContact?: Contact;
  searchTerm?: string;
}
```

## Context Providers

### Socket Context

**File:** `src/context/SocketContext.jsx`

Provides Socket.io connection throughout the app.

```jsx
export const useSocket = () => {
  const context = useContext(SocketContext);
  if (!context) {
    throw new Error("useSocket must be used within SocketProvider");
  }
  return context;
};
```

**Features:**

- Automatic connection management
- Event listener setup
- Connection state tracking
- Error handling

## Utility Components

### Loading States

Common loading patterns used throughout the app:

```jsx
// Skeleton loading for contacts
<div className="animate-pulse">
  <div className="h-12 bg-gray-300 rounded mb-2"></div>
  <div className="h-4 bg-gray-300 rounded mb-1"></div>
  <div className="h-4 bg-gray-300 rounded w-2/3"></div>
</div>

// Spinner for actions
<div className="animate-spin rounded-full h-6 w-6 border-b-2 border-primary"></div>
```

### Error Boundaries

Error handling components for graceful error display:

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <div>Something went wrong.</div>;
    }
    return this.props.children;
  }
}
```

## Component Props and TypeScript

While the project uses JavaScript, here are the expected prop interfaces:

```typescript
// Common interfaces
interface User {
  id: string;
  email: string;
  firstName?: string;
  lastName?: string;
  image?: string;
  color?: number;
  profileSetup: boolean;
}

interface Message {
  _id: string;
  sender: User;
  recipient: User;
  messageType: "text" | "file";
  content?: string;
  fileUrl?: string;
  timestamp: Date;
}

interface Contact extends User {
  lastMessageTime?: Date;
}
```

## Best Practices

### Component Organization

- Keep components small and focused
- Use composition over inheritance
- Implement proper prop validation
- Handle loading and error states

### Performance Optimization

- Use React.memo for expensive renders
- Implement virtual scrolling for long lists
- Optimize re-renders with useCallback and useMemo
- Lazy load non-critical components

### Accessibility

- Use semantic HTML elements
- Implement proper ARIA labels
- Ensure keyboard navigation
- Maintain color contrast ratios

### Testing

- Unit tests for individual components
- Integration tests for component interactions
- Accessibility testing
- Visual regression testing

This component documentation serves as a reference for developers working with the QuikTalk Chat App frontend codebase.
