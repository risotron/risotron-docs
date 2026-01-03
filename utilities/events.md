# Events

Type-safe event system following VSCode patterns.

## Event<T>

```typescript
interface Event<T> {
  (listener: (e: T) => void, thisArgs?: unknown): IDisposable;
}
```

An event is a function that accepts a listener and returns a disposable.

## Emitter<T>

```typescript
import { Emitter } from 'risotron';

class DataService {
  // Private emitter
  private readonly _onDataChanged = new Emitter<Data>();

  // Public event
  readonly onDataChanged = this._onDataChanged.event;

  updateData(data: Data) {
    this.data = data;
    this._onDataChanged.fire(data); // Notify listeners
  }

  dispose() {
    this._onDataChanged.dispose();
  }
}
```

## Subscribing

```typescript
// Subscribe
const subscription = service.onDataChanged(data => {
  console.log('Data changed:', data);
});

// Unsubscribe
subscription.dispose();
```

## With Disposable

```typescript
class MyFeature extends Disposable {
  constructor(service: DataService) {
    super();

    // Auto-disposed when feature is disposed
    this._register(service.onDataChanged(this.handleChange, this));
  }

  private handleChange(data: Data) {
    // Handle event
  }
}
```

## Event Utilities

### Once

```typescript
import { eventOnce } from 'risotron';

// Fire only once
const onceEvent = eventOnce(emitter.event);
onceEvent(data => {
  // Called only first time
});
```

### Map

```typescript
import { eventMap } from 'risotron';

// Transform event data
const mappedEvent = eventMap(
  emitter.event,
  data => data.value
);
```

### Filter

```typescript
import { eventFilter } from 'risotron';

// Only pass matching events
const filteredEvent = eventFilter(
  emitter.event,
  data => data.isValid
);
```

### Debounce

```typescript
import { eventDebounce } from 'risotron';

// Debounce rapid events
const debouncedEvent = eventDebounce(
  emitter.event,
  100 // ms
);
```

## PauseableEmitter

```typescript
import { PauseableEmitter } from 'risotron';

const emitter = new PauseableEmitter<Data>();

emitter.pause();
emitter.fire(data1); // Queued
emitter.fire(data2); // Queued
emitter.resume();    // Both fired
```

## Relay

Forward events from one emitter to another:

```typescript
import { Relay } from 'risotron';

const relay = new Relay<Data>();

// Set input
relay.input = sourceEmitter.event;

// Subscribe to relay's output
relay.event(data => {
  // Receives events from source
});

// Change input source
relay.input = anotherEmitter.event;
```

## EventNone

Placeholder for no-op events:

```typescript
import { EventNone } from 'risotron';

class Service {
  // Event that never fires
  readonly onDidChange = EventNone;
}
```
