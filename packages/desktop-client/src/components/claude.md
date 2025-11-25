# Desktop Client Components Directory

This directory contains 441+ React components organized by feature. All components use React 19 with hooks (no class components).

## Directory Organization

### Budget Components

- [budget/](budget/) - Budget view components (envelope, tracking, rollover budgets)
  - [budget/envelope/](budget/envelope/) - Envelope budgeting UI
  - [budget/tracking/](budget/tracking/) - Tracking budget UI
  - [budget/rollover/](budget/rollover/) - Rollover budget UI
  - Key files: `BudgetSummaries.tsx`, `BudgetTable.tsx`, `BudgetCategories.tsx`

### Account & Transaction Components

- [accounts/](accounts/) - Account list, account page, account header components
- [transactions/](transactions/) - Transaction table, transaction entry, splits
  - Key: `TransactionList.tsx`, `TransactionEdit.tsx`, `MobileTransaction.tsx`

### Reports & Analytics

- [reports/](reports/) - All reporting and visualization components
  - Net worth, cash flow, spending, custom reports
  - Chart components and report configuration

### Rules & Automation

- [rules/](rules/) - Transaction rule creation and management UI
- [schedules/](schedules/) - Recurring transaction scheduling UI

### Data Management

- [payees/](payees/) - Payee management and merge tools
- [tags/](tags/) - Tag creation and management
- [filters/](filters/) - Transaction filtering components

### Bank Integration

- [banksync/](banksync/) - Bank synchronization UI
- [gocardless/](gocardless/) - GoCardless-specific integration UI

### Settings & Configuration

- [settings/](settings/) - All settings pages (UI, encryption, export, etc.)
- [modals/](modals/) - Dialog/modal components used throughout app

### UI Primitives & Common Components

- [common/](common/) - Shared UI components (Button, Input, Select, etc.)
  - Imports from `@actual-app/components` for base primitives
- [responsive/](responsive/) - Responsive layout wrappers and utilities
- [mobile/](mobile/) - Mobile-specific component variants
- [forms/](forms/) - Form input components and utilities
- [select/](select/) - Custom select/dropdown components
- [autocomplete/](autocomplete/) - Autocomplete input components

### Specialized Components

- [spreadsheet/](spreadsheet/) - Spreadsheet-style view components
- [util/](util/) - Component utility functions and helpers

## Component Patterns

### Import Structure

```tsx
// UI primitives from component library
import { Button, Input, View, Text } from '@actual-app/components';

// Hooks from hooks directory
import { useAccounts } from '../../hooks/useAccounts';

// Redux for global state
import { useSelector, useDispatch } from 'react-redux';

// Queries for data
import { liveQuery } from '../../queries';
```

### Styling

- Uses Emotion CSS-in-JS
- Theme accessed via `useTheme()` hook
- Common pattern: `style={{ ...styles.container, color: theme.pageTextPositive }}`

### State Management

1. **Local state**: `useState()`, `useReducer()` for component-specific state
2. **Global state**: Redux store via `useSelector()`/`useDispatch()`
3. **Server data**: Live queries via `liveQuery()` from [../../queries/](../../queries/)

### Mobile vs Desktop

- Desktop components in main directories
- Mobile variants in [mobile/](mobile/) directory
- Use responsive components from [responsive/](responsive/) for adaptive layouts

## Common Component Locations

### Need to modify budget display?

→ [budget/envelope/EnvelopeBudgetComponents.tsx](budget/envelope/EnvelopeBudgetComponents.tsx)
→ [budget/BudgetSummaries.tsx](budget/BudgetSummaries.tsx)

### Need to modify transaction list?

→ [transactions/TransactionList.tsx](transactions/TransactionList.tsx)
→ [transactions/MobileTransaction.tsx](transactions/MobileTransaction.tsx)

### Need to modify account page?

→ [accounts/Account.tsx](accounts/Account.tsx)
→ [accounts/MobileAccounts.tsx](accounts/MobileAccounts.tsx)

### Need to add a modal/dialog?

→ [modals/](modals/) - Check existing modals first
→ Use `Modal` component from `@actual-app/components`

### Need to modify settings?

→ [settings/](settings/) - All settings pages organized by feature

## Testing

- Component tests: `*.test.tsx` files alongside components
- E2E tests: [../../../e2e/](../../../e2e/) directory
- Run component tests: `yarn test`
- Run E2E tests: `yarn e2e`

## Bug Fix Workflow

1. **Find the component**: Use Grep to search for text/functionality
2. **Check parent components**: Trace up the component tree
3. **Check data flow**: Look at Redux state and queries
4. **Verify business logic**: Check [../../{feature}/](../../) directories
5. **Check server logic**: Look in [../../../loot-core/src/server/](../../../loot-core/src/server/)

## Key Files to Know

- [App.tsx](App.tsx) - Main app component and routing
- [Titlebar.tsx](Titlebar.tsx) - App titlebar/navigation
- [Page.tsx](common/Page.tsx) - Page layout wrapper
- [Modals.tsx](modals/Modals.tsx) - Modal rendering system

## Related Documentation

- Parent: [../claude.md](../claude.md) - Desktop client overview
- Queries: [../queries/claude.md](../queries/claude.md) - Data query patterns
- Redux: [../redux/claude.md](../redux/claude.md) - State management
- Component Library: [../../../component-library/src/claude.md](../../../component-library/src/claude.md) - UI primitives
