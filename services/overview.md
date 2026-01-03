# Services Overview

All services are pre-initialized and available via `app.getService()`.

## Available Services

| Service | Purpose |
|---------|---------|
| `ILogService` | Structured logging |
| `IConfigurationService` | Settings management |
| `IStorageService` | Key-value persistence |
| `ICommandService` | Command execution |
| `IFileService` | File operations |
| `IEnvironmentService` | Platform detection |
| `ILifecycleService` | App phase management |
| `IInstantiationService` | DI container |

---

## ILogService

```typescript
const log = app.getService(ILogService);

log.setLevel(LogLevel.Debug);
log.trace('Verbose details');
log.debug('Debug info', { data });
log.info('Information');
log.warn('Warning');
log.error(new Error('Failed'));
```

**Log Levels:** `Off` → `Trace` → `Debug` → `Info` → `Warning` → `Error`

---

## IConfigurationService

```typescript
// Register defaults (once at startup)
registerConfigurationDefaults({
  properties: {
    'editor.fontSize': { type: 'number', default: 14 },
    'editor.wordWrap': { type: 'string', default: 'off', enum: ['off', 'on'] }
  }
});

const config = app.getService(IConfigurationService);

// Get value
const fontSize = config.getValue<number>('editor.fontSize');

// Update
await config.updateValue('editor.fontSize', 16, ConfigurationTarget.USER);

// Listen to changes
config.onDidChangeConfiguration(e => {
  if (e.affectsConfiguration('editor')) {
    // React to change
  }
});
```

**Targets:** `DEFAULT` → `APPLICATION` → `USER` → `WORKSPACE` → `MEMORY`

---

## IStorageService

```typescript
const storage = app.getService(IStorageService);

// Store
storage.store('key', 'value', StorageScope.APPLICATION, StorageTarget.USER);
storage.store('count', 42, StorageScope.WORKSPACE, StorageTarget.USER);
storage.store('data', { a: 1 }, StorageScope.APPLICATION, StorageTarget.USER);

// Retrieve
const str = storage.get('key', StorageScope.APPLICATION);
const num = storage.getNumber('count', StorageScope.WORKSPACE, 0);
const obj = storage.getObject<MyType>('data', StorageScope.APPLICATION);

// Remove
storage.remove('key', StorageScope.APPLICATION);
```

**Scopes:** `APPLICATION` (global), `WORKSPACE` (per workspace), `PROFILE` (per user)

---

## ICommandService

```typescript
// Register
registerCommand('file.save', (accessor, uri?: URI) => {
  const fileService = accessor.get(IFileService);
  // Save logic
});

// Execute
const commands = app.getService(ICommandService);
await commands.executeCommand('file.save', uri);

// Check existence
if (commands.hasCommand('file.save')) {
  // ...
}
```

---

## IFileService

```typescript
const files = app.getService(IFileService);

// Check existence
const exists = await files.exists(URI.file('/path/to/file'));

// Read
const content = await files.readFileText(URI.file('/path/to/file.json'));

// Write
await files.writeFile(
  URI.file('/path/to/output.txt'),
  VSBuffer.fromString('Hello')
);

// Directory operations
await files.createDirectory(URI.file('/path/to/dir'));
await files.del(URI.file('/path/to/file'), { recursive: true });

// Copy/Move
await files.copy(sourceUri, targetUri, { overwrite: true });
await files.move(sourceUri, targetUri);

// Watch
const watcher = files.watch(URI.file('/path'), { recursive: true });
files.onDidFilesChange(e => {
  // React to file changes
});
watcher.dispose();
```

---

## IEnvironmentService

```typescript
const env = app.getService(IEnvironmentService);

// Platform
env.platform          // Platform.Mac | Platform.Windows | Platform.Linux
env.isWindows         // boolean
env.isMacintosh       // boolean
env.isLinux           // boolean

// Paths
env.appRoot           // Application root
env.userDataPath      // User data directory
env.userHome          // URI to home directory
env.logsHome          // URI to logs directory

// App info
env.appName           // Application name
env.appVersion        // Version string
env.isBuilt           // Is packaged build

// System
env.hostname          // Computer name
env.cpuCount          // Number of CPUs
env.totalMemory       // RAM in bytes

// Command line
env.args              // Raw arguments
env.getArg('--flag')  // Get argument value
env.hasArg('--debug') // Check flag presence
```

---

## ILifecycleService

```typescript
const lifecycle = app.getService(ILifecycleService);

// Current phase
lifecycle.phase  // LifecyclePhase

// Wait for phase
await lifecycle.when(LifecyclePhase.Restored);

// Shutdown events
lifecycle.onBeforeShutdown(e => {
  e.veto(hasUnsavedChanges, 'unsaved');
});

lifecycle.onWillShutdown(e => {
  e.join(cleanup(), 'cleanup');
});

// Trigger shutdown
await lifecycle.shutdown(ShutdownReason.Quit);
```

---

## IInstantiationService

```typescript
const instantiation = app.getService(IInstantiationService);

// Create instance with DI
const feature = instantiation.createInstance(MyFeature, extraArg);

// Invoke function with accessor
const result = instantiation.invokeFunction(accessor => {
  const log = accessor.get(ILogService);
  return log.level;
});

// Check service availability
if (instantiation.hasService(IMyService)) {
  const service = instantiation.getService(IMyService);
}
```
