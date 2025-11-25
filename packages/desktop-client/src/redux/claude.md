# Redux State Management

This directory manages global application state using Redux Toolkit 2.10.

## Files Overview

- [store.ts](store.ts) - Redux store configuration and root reducer
- [index.ts](index.ts) - Redux provider setup and exports
- [mock.tsx](mock.tsx) - Mock store for testing components

## State Structure

The Redux store manages:

### App-Level State
- User authentication status
- Current budget file
- UI preferences (theme, sidebar visibility)
- Modal state (which modals are open)
- Navigation state
- Loading/error states

### Feature-Specific State
State is typically organized into slices by feature:
- **Account state**: Selected account, filters
- **Transaction state**: Selected transactions, edit mode
- **Budget state**: Current month, selected categories
- **Settings state**: User preferences
- **Notification state**: Toast messages, alerts

## When to Use Redux vs Local State

### Use Redux for:
- Data shared across multiple pages/components
- User preferences that persist across sessions
- Modal open/close state (when modals affect app-wide state)
- Authentication and user info
- Current route/navigation state

### Use Local State for:
- Form inputs (until submission)
- UI toggles (dropdowns, tooltips) that don't affect other components
- Temporary component state
- Data from live queries (use hooks instead)

### Use Queries for:
- Server data (accounts, transactions, budgets)
- See [../queries/claude.md](../queries/claude.md) for query patterns

## Common Patterns

### Reading State
```tsx
import { useSelector } from 'react-redux';
import type { RootState } from '../redux/store';

function MyComponent() {
  const theme = useSelector((state: RootState) => state.prefs.theme);
  const currentBudget = useSelector((state: RootState) => state.app.budgetId);

  // Use the state
  return <div>Current budget: {currentBudget}</div>;
}
```

### Dispatching Actions
```tsx
import { useDispatch } from 'react-redux';
import { setTheme } from '../redux/prefsSlice'; // Example slice

function MyComponent() {
  const dispatch = useDispatch();

  const handleThemeChange = (theme: string) => {
    dispatch(setTheme(theme));
  };

  return <button onClick={() => handleThemeChange('dark')}>Dark Mode</button>;
}
```

### Creating a Slice
```tsx
// Example: prefsSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';

interface PrefsState {
  theme: 'light' | 'dark';
  sidebarOpen: boolean;
}

const initialState: PrefsState = {
  theme: 'light',
  sidebarOpen: true,
};

const prefsSlice = createSlice({
  name: 'prefs',
  initialState,
  reducers: {
    setTheme: (state, action: PayloadAction<'light' | 'dark'>) => {
      state.theme = action.payload;
    },
    toggleSidebar: (state) => {
      state.sidebarOpen = !state.sidebarOpen;
    },
  },
});

export const { setTheme, toggleSidebar } = prefsSlice.actions;
export default prefsSlice.reducer;
```

## Testing with Redux

### Using Mock Store
```tsx
import { render } from '@testing-library/react';
import { MockReduxProvider } from '../redux/mock';
import MyComponent from './MyComponent';

test('renders with Redux state', () => {
  const initialState = {
    prefs: { theme: 'dark' },
  };

  render(
    <MockReduxProvider initialState={initialState}>
      <MyComponent />
    </MockReduxProvider>
  );

  // Test component behavior
});
```

## State Persistence

Some Redux state persists across sessions:
- User preferences are saved to local storage
- Budget file selection is remembered
- UI state (sidebar position, etc.) persists

Persistence is typically handled by middleware or selectors that sync with:
- Browser localStorage (web)
- Electron store (desktop)
- Server preferences (synced across devices)

## Debugging Redux

### Redux DevTools
Redux DevTools are enabled in development:
1. Install Redux DevTools browser extension
2. Open DevTools panel
3. View state, actions, and time-travel debug

### Common Issues

**State not updating?**
- Check if action is dispatched (`console.log` in reducer)
- Verify selector is pointing to correct state path
- Ensure component is wrapped in Redux Provider

**Stale state in component?**
- Check if using correct selector
- Verify state is actually changing in Redux DevTools
- Check for memo/shallow equality issues

**Performance issues?**
- Use `useSelector` with specific selectors (avoid selecting entire state)
- Use `reselect` for derived/computed state
- Consider moving local UI state out of Redux

## Data Flow

```
User Interaction (Component)
    ↓
dispatch(action)
    ↓
Reducer updates state
    ↓
useSelector triggers re-render
    ↓
Component updates with new state
```

## Related Documentation

- Parent: [../claude.md](../claude.md) - Desktop client overview
- Components: [../components/claude.md](../components/claude.md) - Component patterns
- Queries: [../queries/claude.md](../queries/claude.md) - Data fetching
- Testing: [../../e2e/claude.md](../../e2e/claude.md) - E2E test patterns

## Key Files to Know

- [store.ts](store.ts) - Start here to understand overall state structure
- [mock.tsx](mock.tsx) - Use for testing components with Redux
- [index.ts](index.ts) - Provider setup and exports
