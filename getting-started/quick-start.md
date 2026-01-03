# Quick Start

## Minimal Application

```typescript
import { createApplication, BrowserWindow } from './risotron';

const app = createApplication({ name: 'MyApp' });

app.onReady(() => {
  new BrowserWindow({ width: 800, height: 600 });
});

app.run();
```

## With Configuration

```typescript
import {
  createApplication,
  BrowserWindow,
  IConfigurationService,
  registerConfigurationDefaults
} from './risotron';

// Define settings schema
registerConfigurationDefaults({
  properties: {
    'window.width': { type: 'number', default: 1024 },
    'window.height': { type: 'number', default: 768 }
  }
});

const app = createApplication({ name: 'MyApp' });

app.onReady(() => {
  const config = app.getService(IConfigurationService);

  new BrowserWindow({
    width: config.getValue('window.width'),
    height: config.getValue('window.height')
  });
});

app.run();
```

## With Commands

```typescript
import {
  createApplication,
  registerCommand,
  ILogService
} from './risotron';

// Register command
registerCommand('app.sayHello', (accessor, name: string) => {
  const log = accessor.get(ILogService);
  log.info(`Hello, ${name}!`);
});

const app = createApplication({ name: 'MyApp' });

app.onReady(async () => {
  // Execute command
  await app.executeCommand('app.sayHello', 'World');
});

app.run();
```

## With Storage

```typescript
import {
  createApplication,
  IStorageService,
  StorageScope,
  StorageTarget
} from './risotron';

const app = createApplication({ name: 'MyApp' });

app.onReady(() => {
  const storage = app.getService(IStorageService);

  // Save
  storage.store('lastOpened', Date.now(), StorageScope.APPLICATION, StorageTarget.USER);

  // Load
  const lastOpened = storage.getNumber('lastOpened', StorageScope.APPLICATION);
});

app.run();
```

## Lifecycle Events

```typescript
const app = createApplication({ name: 'MyApp' });

app.onReady(() => {
  console.log('Ready - create windows here');
});

app.onBeforeQuit(() => {
  console.log('Saving state before quit...');
});

app.onWindowAllClosed(() => {
  // On macOS, keep app running
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

app.run();
```
