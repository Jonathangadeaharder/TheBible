# React Inter-Process Communication - Essentials

## Key Principle

Keep data fetching separate from UI components and manage state effectively.

## Common Communication Methods

### HTTP/REST with Fetch or Axios
**Best for**: Standard API calls, CRUD operations

```javascript
import axios from 'axios';

// Custom hook for data fetching
function useApi(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await axios.get(url);
        setData(response.data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Using the hook in a component
const UserList = () => {
  const { data: users, loading, error } = useApi('/api/users');

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};
```

### WebSockets for Real-time Communication
**Best for**: Chat applications, live updates, notifications

```javascript
import { useState, useEffect } from 'react';

function useWebSocket(url) {
  const [ws, setWs] = useState(null);
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const websocket = new WebSocket(url);

    websocket.onmessage = (event) => {
      const message = JSON.parse(event.data);
      setMessages(prev => [...prev, message]);
    };

    setWs(websocket);

    return () => {
      websocket.close();
    };
  }, [url]);

  const sendMessage = (message) => {
    if (ws && ws.readyState === WebSocket.OPEN) {
      ws.send(JSON.stringify(message));
    }
  };

  return { messages, sendMessage };
}
```

### Context for State Management
**Best for**: Sharing data across multiple components

```javascript
import React, { createContext, useContext, useState } from 'react';

const ApiContext = createContext();

export const ApiProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const [notifications, setNotifications] = useState([]);

  const login = async (credentials) => {
    // Login logic
    setUser(userData);
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <ApiContext.Provider value={{ user, notifications, login, logout }}>
      {children}
    </ApiContext.Provider>
  );
};

export const useApi = () => {
  const context = useContext(ApiContext);
  if (!context) {
    throw new Error('useApi must be used within ApiProvider');
  }
  return context;
};
```

## Quick Reference

| Method | Use Case | Complexity |
|--------|----------|------------|
| Fetch/Axios | API calls | Low |
| WebSockets | Real-time | Medium |
| Context | State sharing | Low |
| Redux/Zustand | Complex state | Medium |

## Implementation Guidelines

### For Data Fetching
1. Use custom hooks to encapsulate data fetching logic
2. Handle loading and error states
3. Implement caching when appropriate
4. Cancel requests when components unmount

### For Real-time Features
1. Handle connection lifecycle (open, close, error)
2. Implement reconnection logic
3. Manage message queuing during disconnects
4. Clean up connections on component unmount

### For State Management
1. Keep state as local as possible
2. Lift state up only when necessary
3. Use Context for moderate complexity
4. Consider Redux-like libraries for complex applications

## Best Practices

1. **Separation of Concerns**: Keep data fetching separate from UI
2. **Error Boundaries**: Handle network errors gracefully
3. **Performance**: Implement caching and request deduplication
4. **Security**: Validate and sanitize all data from APIs