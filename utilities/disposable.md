# Disposable

Resource management pattern for automatic cleanup.

## IDisposable

```typescript
interface IDisposable {
  dispose(): void;
}
```

Any object that needs cleanup implements this interface.

## Disposable Base Class

Extend `Disposable` and use `_register()` for auto-cleanup:

```typescript
import { Disposable } from 'risotron';

class MyFeature extends Disposable {
  constructor() {
    super();

    // These will be disposed when MyFeature is disposed
    this._register(emitter.event(this.onEvent, this));
    this._register(setInterval(() => this.tick(), 1000));
  }
}

// Later
feature.dispose(); // All registered items cleaned up
```

## DisposableStore

Container for multiple disposables:

```typescript
import { DisposableStore } from 'risotron';

const store = new DisposableStore();

store.add(listener1);
store.add(listener2);
store.add(subscription);

// Clear all but keep store usable
store.clear();

// Or dispose everything
store.dispose();
```

## MutableDisposable

For swappable single disposable:

```typescript
import { MutableDisposable } from 'risotron';

const current = new MutableDisposable();

// Set new value (disposes previous)
current.value = createWatcher('/path/a');
current.value = createWatcher('/path/b'); // Previous disposed

// Clear without disposing container
current.clear();

// Dispose container
current.dispose();
```

## Helper Functions

```typescript
import { toDisposable, combinedDisposable, isDisposable } from 'risotron';

// Create from function
const d = toDisposable(() => {
  console.log('Cleanup!');
});

// Combine multiple
const combined = combinedDisposable(d1, d2, d3);
combined.dispose(); // Disposes all

// Check if disposable
if (isDisposable(obj)) {
  obj.dispose();
}
```

## Best Practices

```typescript
class MyComponent extends Disposable {
  private readonly _onDidChange = this._register(new Emitter<void>());
  readonly onDidChange = this._onDidChange.event;

  constructor(
    @IConfigurationService config: IConfigurationService
  ) {
    super();

    // Always register event subscriptions
    this._register(config.onDidChangeConfiguration(e => {
      if (e.affectsConfiguration('my.setting')) {
        this._onDidChange.fire();
      }
    }));

    // Register cleanup callbacks
    this._register(toDisposable(() => {
      this.cleanup();
    }));
  }

  private cleanup() {
    // Custom cleanup logic
  }
}
```
