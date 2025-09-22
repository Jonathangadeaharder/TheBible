# React Design Patterns - Essentials

## Key Principle

Focus on component composition and state management rather than complex patterns.

## Essential Patterns

### Component Composition
**When to use**: Build reusable, flexible components

```jsx
// Container component
const UserList = ({ users, onSelect }) => (
  <div>
    {users.map(user => (
      <UserItem key={user.id} user={user} onClick={() => onSelect(user)} />
    ))}
  </div>
);

// Presentational component
const UserItem = ({ user, onClick }) => (
  <div onClick={onClick}>
    <h3>{user.name}</h3>
    <p>{user.email}</p>
  </div>
);
```

### Custom Hooks
**When to use**: Reuse stateful logic across components

```jsx
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  const setValue = (value) => {
    setStoredValue(value);
    window.localStorage.setItem(key, JSON.stringify(value));
  };

  return [storedValue, setValue];
}
```

### Context Provider
**When to use**: Share data across multiple components

```jsx
import React, { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
};
```

## Quick Reference

| Pattern | Problem | When to Use |
|---------|---------|-------------|
| Component Composition | Reusability | Building UI components |
| Custom Hooks | Logic reuse | Sharing stateful logic |
| Context | Prop drilling | Global state sharing |
| Render Props | Render flexibility | Component customization |

## Learning Approach

1. Master JSX and component basics first
2. Understand when to split components
3. Learn hooks before complex patterns
4. Practice with real-world examples