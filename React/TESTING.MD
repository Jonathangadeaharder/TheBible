# React Testing Guide

> **Note**: For general testing principles and common anti-patterns to avoid, please refer to the main [TESTING.MD](../TESTING.MD) file.

## State-of-the-Art Testing Frameworks

### Unit Testing Framework
**Jest** - Facebook's JavaScript testing framework, comes preconfigured with Create React App

### Component Testing Library
**React Testing Library** - The recommended way to test React components, focusing on user behavior

## Installation

If using Create React App, Jest is already included:
```bash
# Already included in CRA
```

For React Testing Library:
```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

## Unit Test Example

```javascript
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import '@testing-library/jest-dom';
import UserProfile from './UserProfile';

// Mock component dependencies
jest.mock('./services/api', () => ({
  getUser: jest.fn()
}));

describe('UserProfile', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  test('displays user name when user is loaded', async () => {
    // Arrange
    const mockUser = { id: 1, name: 'John Doe', email: 'john@example.com' };
    require('./services/api').getUser.mockResolvedValue(mockUser);

    // Act
    render(<UserProfile userId={1} />);

    // Assert
    expect(screen.getByText('Loading...')).toBeInTheDocument();
    expect(await screen.findByText('John Doe')).toBeInTheDocument();
    expect(screen.getByText('john@example.com')).toBeInTheDocument();
  }, 60000); // 1 minute timeout

  test('calls onDelete when delete button is clicked', async () => {
    // Arrange
    const mockUser = { id: 1, name: 'John Doe', email: 'john@example.com' };
    const mockDelete = jest.fn();
    require('./services/api').getUser.mockResolvedValue(mockUser);

    render(<UserProfile userId={1} onDelete={mockDelete} />);
    await screen.findByText('John Doe');

    // Act
    const deleteButton = screen.getByRole('button', { name: 'Delete' });
    await userEvent.click(deleteButton);

    // Assert
    expect(mockDelete).toHaveBeenCalledWith(1);
  }, 60000); // 1 minute timeout

  test('shows error message when user loading fails', async () => {
    // Arrange
    require('./services/api').getUser.mockRejectedValue(new Error('User not found'));

    // Act
    render(<UserProfile userId={999} />);

    // Assert
    expect(await screen.findByText('Failed to load user')).toBeInTheDocument();
  }, 60000); // 1 minute timeout
});
```

## Integration Test Example

```javascript
import { render, screen } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import userEvent from '@testing-library/user-event';
import App from './App';

// Mock API calls
global.fetch = jest.fn();

describe('App Integration', () => {
  beforeEach(() => {
    fetch.mockClear();
  });

  test('user can navigate to profile and see user details', async () => {
    // Arrange
    const mockUsers = [
      { id: 1, name: 'John Doe', email: 'john@example.com' }
    ];

    fetch.mockResolvedValueOnce({
      json: () => Promise.resolve(mockUsers),
      ok: true
    });

    // Act
    render(
      <BrowserRouter>
        <App />
      </BrowserRouter>
    );

    // Navigate to users page
    const usersLink = screen.getByRole('link', { name: 'Users' });
    await userEvent.click(usersLink);

    // Assert
    expect(await screen.findByText('John Doe')).toBeInTheDocument();

    // Click on user to view details
    const userLink = screen.getByRole('link', { name: 'John Doe' });
    await userEvent.click(userLink);

    expect(await screen.findByText('john@example.com')).toBeInTheDocument();
  }, 300000); // 5 minutes timeout
});
```

## End-to-End Test Example

```javascript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import App from './App';

describe('Full Application Workflow', () => {
  test('complete user registration and profile management workflow', async () => {
    // Arrange
    const startTime = Date.now();

    // Act
    render(<App />);

    // Navigate to registration
    const registerLink = screen.getByRole('link', { name: 'Register' });
    await userEvent.click(registerLink);

    // Fill registration form
    const nameInput = screen.getByLabelText('Name');
    const emailInput = screen.getByLabelText('Email');
    await userEvent.type(nameInput, 'Test User');
    await userEvent.type(emailInput, 'test@example.com');

    const submitButton = screen.getByRole('button', { name: 'Register' });
    await userEvent.click(submitButton);

    // Verify profile creation
    expect(await screen.findByText('Welcome, Test User')).toBeInTheDocument();

    // Update profile
    const editButton = screen.getByRole('button', { name: 'Edit Profile' });
    await userEvent.click(editButton);

    const bioInput = screen.getByLabelText('Bio');
    await userEvent.type(bioInput, 'This is my bio');

    const saveButton = screen.getByRole('button', { name: 'Save' });
    await userEvent.click(saveButton);

    // Verify update
    expect(await screen.findByText('Profile updated successfully')).toBeInTheDocument();

    const endTime = Date.now();
    const duration = endTime - startTime;

    // Assert
    expect(duration).toBeLessThan(600000); // 10 minutes timeout
  }, 600000); // 10 minutes timeout
});
```

## Jest Configuration for Timeouts

Add to your `jest.config.js` or in `package.json`:

```javascript
// jest.config.js
module.exports = {
  // ... other config
  testTimeout: 60000, // 1 minute default timeout
  // ... other config
};
```

Or in `package.json`:
```json
{
  "jest": {
    "testTimeout": 60000
  }
}
```

## Best Practices

1. Use React Testing Library for component tests (focus on user behavior)
2. Test user interactions rather than implementation details
3. Mock external dependencies like API calls
4. Use descriptive test names that explain the behavior
5. Follow the AAA pattern (Arrange, Act, Assert)
6. Test both happy paths and error conditions
7. Use async/await for asynchronous operations
8. Clean up mocks between tests
9. Use data-testid attributes sparingly, prefer semantic queries
10. Separate unit and integration tests
11. Test edge cases and boundary conditions
12. Use jest.mock() for mocking modules
13. Use fake timers for time-dependent code
14. **Always set timeouts: 1 minute for unit tests, 5 minutes for integration tests, 10 minutes for end-to-end tests**
15. Set test timeouts as the second parameter to test functions
16. Configure default timeouts in Jest configuration
17. Monitor test execution times to identify performance regressions
18. Use jest.setTimeout() for global timeout configuration when needed
19. **Keep unit tests concise: maximum 10 lines of code (LOC), up to 20 LOC with explanatory comment**

## Mocking Anti-Patterns to Avoid

1. **The Mockery**: Avoid excessive mocking that tests mock interactions rather than real behavior. This often indicates components with too many dependencies.

2. **Overusing Mocks for Implementation Details**: Don't mock to assert on internal component structure or private behaviors. Focus on testing observable user interactions rather than implementation specifics.

3. **Leaky Mocks**: Ensure mocks don't leak between tests. Each test should have isolated mock configurations to maintain consistency.

4. **Mocking Everything Indiscriminately**: Don't mock all dependencies by default. Use mocks sparingly for true isolation, preferring fakes or stubs for simpler simulations.

Focus on testing behavior over internals. If mocking feels excessive, it might signal design issues like components with too many responsibilities.