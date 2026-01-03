# Async

Async utilities including cancellation, timeout, debounce, and throttle.

## CancellationToken

Cooperative cancellation for async operations:

```typescript
import { CancellationTokenSource, CancellationToken } from 'risotron';

async function longOperation(token: CancellationToken) {
  for (let i = 0; i < 100; i++) {
    if (token.isCancellationRequested) {
      throw new CancellationError();
    }
    await doStep(i);
  }
}

// Usage
const cts = new CancellationTokenSource();

// Cancel after 5 seconds
setTimeout(() => cts.cancel(), 5000);

try {
  await longOperation(cts.token);
} catch (e) {
  if (isCancellationError(e)) {
    console.log('Operation cancelled');
  }
}

// Cleanup
cts.dispose();
```

## Timeout

```typescript
import { timeout } from 'risotron';

// Simple delay
await timeout(1000);

// With cancellation
const cts = new CancellationTokenSource();
try {
  await timeout(5000, cts.token);
} catch (e) {
  // Cancelled
}
```

## Debounce

Delay execution until input stops:

```typescript
import { debounce } from 'risotron';

const debouncedSave = debounce((content: string) => {
  saveToFile(content);
}, 300);

// Rapid calls
debouncedSave('a');
debouncedSave('ab');
debouncedSave('abc');
// Only last call executes after 300ms
```

## Throttle

Limit execution rate:

```typescript
import { throttle } from 'risotron';

const throttledScroll = throttle((position: number) => {
  updateUI(position);
}, 100);

// Rapid calls
window.addEventListener('scroll', () => {
  throttledScroll(window.scrollY);
  // Executes at most once per 100ms
});
```

## Interval

```typescript
import { interval } from 'risotron';

// Returns disposable
const disposable = interval(() => {
  console.log('Tick');
}, 1000);

// Stop
disposable.dispose();
```

## Retry

Retry with exponential backoff:

```typescript
import { retry } from 'risotron';

const result = await retry(
  async () => {
    const response = await fetch(url);
    if (!response.ok) throw new Error('Failed');
    return response.json();
  },
  {
    maxAttempts: 3,
    initialDelay: 100,
    maxDelay: 5000,
    factor: 2
  }
);
```

## Race

Promise race with cancellation:

```typescript
import { raceCancellation } from 'risotron';

const result = await raceCancellation(
  fetchData(),
  token
);
// Returns undefined if cancelled
```
