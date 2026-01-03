# Introduction

Risotron is an abstraction layer over Electron Forge that integrates battle-tested modules from VSCode.

## Why Risotron?

| Electron | Risotron |
|----------|----------|
| Manual lifecycle handling | Structured phases with proper shutdown |
| No built-in service architecture | Pre-wired DI container with services |
| Scattered event listeners | Automatic cleanup via Disposable pattern |
| DIY configuration | Hierarchical settings with schema validation |
| console.log debugging | Professional logging with levels |

## What You Get

**Out of the box:**
- Application bootstrap with lifecycle management
- Dependency Injection container (VSCode-style)
- Configuration service with defaults and change events
- Storage service for persistence
- Command registry and execution
- File system operations
- Structured logging
- Platform/environment detection

## Architecture Overview

```
┌─────────────────────────────────────────┐
│         APPLICATION LAYER               │
│         (Your App Code)                 │
├─────────────────────────────────────────┤
│         UI COMPONENT LAYER              │
│         (Widgets, Layout, Theming)      │
├─────────────────────────────────────────┤
│         ELECTRON ENHANCEMENT LAYER      │
│         (Services, Utilities, DI)       │
│         ← You are here                  │
└─────────────────────────────────────────┘
```

The Electron Enhancement Layer is the foundation. It's always available via boilerplate - you build on top of it.

## Quick Example

```typescript
import { createApplication, BrowserWindow, ILogService } from 'risotron';

const app = createApplication({ name: 'MyApp' });

app.onReady(() => {
  const log = app.getService(ILogService);
  log.info('Application ready');

  new BrowserWindow({ width: 800, height: 600 });
});

app.run();
```

Compare this to 30+ lines of boilerplate in raw Electron.
