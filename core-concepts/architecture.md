# Architecture

## Three-Layer Design

```
┌─────────────────────────────────────────────────────┐
│         APPLICATION LAYER                           │
│         (Features & Business Logic)                 │
│  • Your application code                            │
│  • Depends on: Everything below                     │
└────────────────────────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────┐
│         UI COMPONENT LAYER                          │
│         (Presentation & User Interface)             │
│  • Widgets, Layout, Theming                         │
│  • Depends on: Electron Enhancement                 │
└────────────────────────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────┐
│         ELECTRON ENHANCEMENT LAYER                  │
│         (Platform Abstraction & Core Services)      │
│  • Services, Utilities, DI Container                │
│  • Depends on: NOTHING (independent)                │
└─────────────────────────────────────────────────────┘
```

## Electron Enhancement Layer

This layer provides everything you need to build a robust Electron app:

| Component | Purpose |
|-----------|---------|
| `base/common` | Pure utilities, no Electron dependency |
| `platform/*` | Services with Electron integration |
| `main/*` | Application bootstrap |

## Internal Layer System

Within the Electron Enhancement Layer, modules are organized by dependency order:

```
Layer 0:  _primitives     (types, symbols)
Layer 1:  _functional     (arrays, strings, objects)
Layer 2:  lifecycle       (disposable)
Layer 3:  event           (emitter)
Layer 4:  async           (cancellation, timeout)
Layer 5:  instantiation   (DI container)
Layer 6:  commands        (command registry)
Layer 7:  configuration   (settings)
Layer 8:  storage         (persistence)
Layer 9:  lifecycle       (app phases)
Layer 10: log             (logging)
Layer 11: uri, buffer     (data types)
Layer 12: environment     (platform detection)
Layer 14: files           (file system)
```

**Rule:** Layer N can only import from Layers 0 to N-1.

## Service-Oriented Architecture

All functionality is exposed through services:

```typescript
// Services are accessed via identifiers
const log = app.getService(ILogService);
const config = app.getService(IConfigurationService);
const storage = app.getService(IStorageService);

// Services can depend on other services (via DI)
class MyService {
  constructor(
    @ILogService private log: ILogService,
    @IConfigurationService private config: IConfigurationService
  ) {}
}
```

## VSCode Patterns

Risotron follows VSCode's architectural patterns:

1. **Dependency Injection** - Constructor injection with decorators
2. **Disposable Pattern** - Automatic resource cleanup
3. **Event System** - Type-safe event emitters
4. **Service Identifiers** - `createDecorator<T>()` pattern
5. **Lifecycle Phases** - Ordered startup/shutdown
