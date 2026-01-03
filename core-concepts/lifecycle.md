# Lifecycle

## Application Phases

```
Starting ──► Ready ──► Restored ──► Eventually
    │           │          │            │
    │           │          │            └── Fully idle, background tasks
    │           │          └── State restored (windows, editors)
    │           └── Core services available
    └── Services initializing
```

| Phase | When | Use For |
|-------|------|---------|
| `Starting` | App launching | Core service init |
| `Ready` | Services ready | Create windows, UI |
| `Restored` | State loaded | Restore previous session |
| `Eventually` | App idle | Background tasks, analytics |

## Startup Flow

```typescript
const app = createApplication({ name: 'MyApp' });

// Phase: Starting
// - Services are being initialized
// - Don't access services yet

app.onReady(() => {
  // Phase: Ready
  // - All services available
  // - Create windows here
  const log = app.getService(ILogService);
  log.info('Ready!');
});

app.run();
```

## Waiting for Phases

```typescript
const lifecycle = app.getService(ILifecycleService);

// Wait for specific phase
await lifecycle.when(LifecyclePhase.Restored);

// Check current phase
if (lifecycle.phase >= LifecyclePhase.Ready) {
  // Safe to use services
}
```

## Shutdown Flow

```
onBeforeShutdown ──► onWillShutdown ──► onDidShutdown
       │                   │                  │
       │                   │                  └── Cleanup complete
       │                   └── Perform cleanup (can delay)
       └── Can veto (cancel quit)
```

### Veto Shutdown

```typescript
lifecycle.onBeforeShutdown(event => {
  if (hasUnsavedChanges()) {
    event.veto(true, 'unsaved-changes');
    showSaveDialog();
  }
});
```

### Cleanup on Shutdown

```typescript
lifecycle.onWillShutdown(event => {
  // Join a promise to delay shutdown until complete
  event.join(saveAllData(), 'save-data');
  event.join(closeConnections(), 'close-connections');
});
```

## Shutdown Reasons

| Reason | Description |
|--------|-------------|
| `Close` | Window closed |
| `Quit` | App quit requested |
| `Reload` | App reloading |
| `Kill` | Force terminated |

```typescript
lifecycle.onWillShutdown(event => {
  if (event.reason === ShutdownReason.Reload) {
    // Don't save - just reloading
    return;
  }
  event.join(saveState(), 'save');
});
```
