# Query Layer Documentation

This directory contains the data query layer that connects React components to the loot-core database.

## Query Types

### 1. Live Queries (`liveQuery`)

Real-time queries that automatically re-render components when data changes.

```tsx
import { liveQuery } from '../queries';

function MyComponent() {
  const accounts = liveQuery(() => q('accounts').select('*'), []);

  if (!accounts) return <div>Loading...</div>;

  return (
    <div>
      {accounts.map(account => (
        <div key={account.id}>{account.name}</div>
      ))}
    </div>
  );
}
```

**When to use:**

- Display data that may change in real-time
- Lists that need to stay synchronized (accounts, categories, payees)
- Data that multiple users might edit (with sync enabled)

**How it works:**

- Subscribes to database changes
- Automatically re-queries when dependencies change
- Re-renders component with fresh data
- Cleans up subscription on unmount

### 2. AQL Queries (`aqlQuery`)

Actual Query Language - SQL-like queries for complex data needs.

```tsx
import { aqlQuery } from '../queries';

// Basic query
const transactions = await aqlQuery(
  q('transactions')
    .filter({ account: accountId })
    .orderBy({ date: 'desc' })
    .limit(50),
);

// Join query
const transactionsWithPayees = await aqlQuery(
  q('transactions')
    .leftJoin('payees', { 'transactions.payee': 'payees.id' })
    .select(['transactions.*', 'payees.name as payee_name']),
);

// Aggregation
const totals = await aqlQuery(
  q('transactions')
    .filter({ category: categoryId })
    .calculate({ sum: '$amount' }),
);
```

**When to use:**

- One-time data fetches (not live updates)
- Complex queries with joins/aggregations
- Data transformations
- Reports and analytics

**Query Methods:**

- `.filter({ field: value })` - WHERE clause
- `.select(['field1', 'field2'])` - SELECT fields
- `.orderBy({ field: 'asc'|'desc' })` - ORDER BY
- `.limit(n)` - LIMIT rows
- `.offset(n)` - OFFSET for pagination
- `.leftJoin()`, `.innerJoin()` - JOIN tables
- `.calculate({ sum: '$field', count: '*' })` - Aggregations

### 3. Paged Queries (`pagedQuery`)

Efficient pagination for large datasets.

```tsx
import { usePagedQuery } from '../hooks/usePagedQuery';

function TransactionList() {
  const { data, isLoading, hasMore, loadMore } = usePagedQuery({
    query: q('transactions').orderBy({ date: 'desc' }),
    pageSize: 50,
  });

  return (
    <div>
      {data.map(transaction => (
        <TransactionRow key={transaction.id} transaction={transaction} />
      ))}
      {hasMore && <button onClick={loadMore}>Load More</button>}
    </div>
  );
}
```

**When to use:**

- Large lists (thousands of transactions)
- Infinite scroll implementations
- Load-on-demand scenarios
- Performance optimization for big datasets

## Query Builder (`q` function)

The `q()` function builds queries for all query types:

```tsx
import { q } from '../queries';

// Table selection
q('accounts');
q('transactions');
q('categories');
q('payees');
q('tags');

// Chaining methods
q('transactions')
  .filter({ account: '123' })
  .filter({ amount: { $gt: 0 } }) // Operators: $gt, $lt, $gte, $lte, $ne
  .orderBy({ date: 'desc' })
  .limit(100);
```

## Common Query Patterns

### Get All Accounts

```tsx
const accounts = liveQuery(() => q('accounts').select('*'), []);
```

### Get Transactions for Account

```tsx
const transactions = liveQuery(
  () =>
    q('transactions').filter({ account: accountId }).orderBy({ date: 'desc' }),
  [accountId],
);
```

### Get Budget Category Totals

```tsx
const categoryTotals = liveQuery(
  () => q('category_budgets').filter({ month: currentMonth }).select('*'),
  [currentMonth],
);
```

### Get Payees with Transaction Count

```tsx
const payeesWithCount = await aqlQuery(
  q('payees')
    .leftJoin('transactions', { 'payees.id': 'transactions.payee' })
    .select(['payees.*'])
    .calculate({ count: 'transactions.id' })
    .groupBy('payees.id'),
);
```

### Search Transactions

```tsx
const searchResults = await aqlQuery(
  q('transactions')
    .filter({ notes: { $like: `%${searchTerm}%` } })
    .orderBy({ date: 'desc' })
    .limit(50),
);
```

## Query Optimization

### Use Dependencies Correctly

```tsx
// ✅ Good: Dependencies match what's used in query
const data = liveQuery(() => q('transactions').filter({ account: id }), [id]);

// ❌ Bad: Missing dependencies
const data = liveQuery(() => q('transactions').filter({ account: id }), []);

// ❌ Bad: Too many dependencies (causes unnecessary re-queries)
const data = liveQuery(
  () => q('transactions').filter({ account: id }),
  [id, otherVar],
);
```

### Select Only Needed Fields

```tsx
// ✅ Good: Select specific fields
const accounts = liveQuery(() => q('accounts').select(['id', 'name']), []);

// ❌ Bad: Select everything when you only need a few fields
const accounts = liveQuery(() => q('accounts').select('*'), []);
```

### Use Paged Queries for Large Lists

```tsx
// ✅ Good: Paginated for 10,000+ transactions
const { data } = usePagedQuery({
  query: q('transactions').orderBy({ date: 'desc' }),
  pageSize: 50,
});

// ❌ Bad: Loading all transactions at once
const data = liveQuery(() => q('transactions').select('*'), []);
```

## Direct Database Access

For complex operations, you can access the database directly:

```tsx
import { send } from '../platform/client/fetch';

// Execute custom query
const result = await send('query', {
  query: q('transactions').filter({ account: id }),
});

// Execute mutation
await send('transaction-update', {
  id: transactionId,
  fields: { amount: 1000, notes: 'Updated' },
});
```

## Common Tables

- `accounts` - Bank accounts
- `transactions` - All transactions
- `categories` - Budget categories
- `category_groups` - Category groups
- `payees` - Payees
- `tags` - Transaction tags
- `category_budgets` - Monthly budget amounts
- `schedules` - Recurring transactions
- `rules` - Transaction rules

## Query Debugging

### Log Query Results

```tsx
const data = liveQuery(() => {
  const result = q('transactions').filter({ account: id });
  console.log('Query:', result);
  return result;
}, [id]);
```

### Check Query Performance

```tsx
console.time('query');
const data = await aqlQuery(q('transactions').select('*'));
console.timeEnd('query');
```

### Common Issues

**Query returns undefined?**

- Live query hasn't loaded yet (check for loading state)
- Dependencies incorrect (not triggering re-query)
- Table or field name typo

**Query not updating?**

- Missing from `liveQuery` dependency array
- Using `aqlQuery` instead of `liveQuery` (doesn't auto-update)
- Data changed but query filter excludes it

**Query too slow?**

- Select only needed fields
- Add appropriate filters early in query chain
- Use paged queries for large datasets
- Check for missing indexes (see [../../loot-core/src/server/db/claude.md](../../loot-core/src/server/db/claude.md))

## Related Documentation

- Parent: [../claude.md](../claude.md) - Desktop client overview
- Components: [../components/claude.md](../components/claude.md) - Using queries in components
- Database: [../../loot-core/src/server/db/claude.md](../../loot-core/src/server/db/claude.md) - Database schema
- Redux: [../redux/claude.md](../redux/claude.md) - State management

## Key Files to Know

- Query builder and live query implementation
- AQL query engine
- Paged query hooks
- Query type definitions
