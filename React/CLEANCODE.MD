# React/JavaScript Clean Code Guidelines

This document outlines React and JavaScript-specific clean code principles that build upon the general clean code principles.

## Naming Conventions

### Components
- Use PascalCase for component names: `UserProfile`, `NavigationMenu`
- Use camelCase for prop names: `userName`, `isLoading`
- File names should match component names: `UserProfile.jsx`, `NavigationMenu.js`

### Functions and Variables
- Use camelCase for functions and variables: `calculateTotal()`, `userName`
- Use descriptive names that explain what the function does or variable represents
- Boolean props should start with `is`, `has`, or `should`: `isLoading`, `hasError`, `shouldRefresh`

### Constants
- Use UPPER_SNAKE_CASE for global constants: `MAX_RETRY_ATTEMPTS`, `API_BASE_URL`
- Use camelCase for local constants: `defaultConfig`, `initialState`

## React-Specific Best Practices

### Component Structure
- Prefer functional components with hooks over class components
- Keep components small and focused on a single responsibility
- Extract custom hooks for reusable stateful logic
- Use fragments (`<>...</>`) instead of unnecessary div wrappers

### Props and State
- Destructure props at the beginning of functional components:
  ```javascript
  function UserCard({ name, email, avatar }) {
    // component logic
  }
  ```
- Use PropTypes or TypeScript for prop validation
- Keep state as local as possible to where it's used
- Lift state up only when necessary for sharing between components

### Hooks
- Follow the Rules of Hooks (only call hooks at the top level, only call hooks from React functions)
- Use `useState` for simple state management
- Use `useEffect` for side effects, specifying dependency arrays appropriately
- Use `useCallback` and `useMemo` for performance optimization when needed

### Event Handlers
- Define event handlers inside components using useCallback for performance:
  ```javascript
  const handleClick = useCallback(() => {
    // handle click
  }, [dependencies]);
  ```
- Name event handlers with `handle` prefix: `handleClick`, `handleSubmit`

## JavaScript-Specific Best Practices

### ES6+ Features
- Use arrow functions for anonymous functions and callbacks
- Use destructuring for objects and arrays:
  ```javascript
  const { name, age } = user;
  const [first, second] = items;
  ```
- Use template literals for string interpolation:
  ```javascript
  const message = `Hello ${name}, you have ${count} messages`;
  ```

### Imports and Exports
- Use ES6 import/export syntax
- Group imports in order:
  1. External libraries
  2. Internal modules
  3. Components
  4. Assets
- Use absolute imports when appropriate with path mapping

### Error Handling
- Use try/catch for async operations and error-prone code
- Handle promise rejections properly
- Use Error Boundaries for catching JavaScript errors in React components

## Performance Considerations

### Rendering Optimization
- Use React.memo for functional components that render frequently
- Use useMemo for expensive calculations:
  ```javascript
  const expensiveValue = useMemo(() => calculateExpensiveValue(a, b), [a, b]);
  ```
- Use useCallback for functions passed as props to prevent unnecessary re-renders

### Bundle Size
- Lazy load components that aren't immediately needed:
  ```javascript
  const LazyComponent = lazy(() => import('./LazyComponent'));
  ```
- Code-split by routes or features
- Tree-shake unused dependencies

### Data Fetching
- Use libraries like React Query or SWR for data fetching and caching
- Implement proper loading and error states
- Cancel requests when components unmount

## Testing Considerations

### Component Testing
- Use React Testing Library for testing user interactions and behavior
- Test components based on how users interact with them, not implementation details
- Mock external dependencies like API calls
- Test both happy paths and error conditions

### Hook Testing
- Test custom hooks separately using libraries like react-hooks-testing-library
- Test hook behavior with different inputs and edge cases
- Ensure hooks clean up properly when needed

### Integration Testing
- Test component compositions and data flow
- Use mock service workers (MSW) for API mocking in integration tests
- Test user workflows across multiple components

## Styling Best Practices

### CSS and Styling
- Use CSS modules or styled-components for component-scoped styles
- Prefer utility-first CSS frameworks like Tailwind CSS when appropriate
- Avoid inline styles for complex styling
- Use CSS variables for themeable values

### Responsive Design
- Use CSS media queries for responsive layouts
- Prefer mobile-first approach
- Test components across different screen sizes

## State Management

### Local State
- Use useState and useReducer for local component state
- Keep state flat when possible to simplify updates
- Derive computed values instead of storing them in state

### Global State
- Use Context API for simple global state needs
- Consider Redux, Zustand, or Jotai for complex state management
- Normalize state shape to avoid duplication