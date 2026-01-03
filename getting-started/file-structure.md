# File Structure

## Overview

```
src/
├── main.ts                 # Application entry point
├── preload.ts              # Electron preload script
├── renderer.ts             # Renderer process
└── risotron/
    ├── index.ts            # Public API exports
    ├── main/               # Application bootstrap
    ├── base/common/        # Base utilities
    └── platform/           # Platform services
```

## Risotron Core

```
risotron/
├── index.ts                # Exports everything
│
├── main/
│   ├── application.ts      # RisotronApplication class
│   └── window.ts           # BrowserWindow wrapper
│
├── base/common/
│   ├── _primitives/        # Type checking, symbols
│   │   ├── types.ts
│   │   └── symbols.ts
│   │
│   ├── _functional/        # Pure utility functions
│   │   ├── arrays.ts
│   │   ├── strings.ts
│   │   └── objects.ts
│   │
│   ├── lifecycle/          # Disposable pattern
│   │   ├── disposable.ts
│   │   ├── store.ts
│   │   └── mutable.ts
│   │
│   ├── event/              # Event system
│   │   └── emitter.ts
│   │
│   ├── async/              # Async utilities
│   │   ├── cancellation.ts
│   │   └── timeout.ts
│   │
│   ├── uri/                # URI handling
│   │   └── uri.ts
│   │
│   └── buffer/             # Binary data
│       └── buffer.ts
│
└── platform/
    ├── instantiation/      # DI container
    │   ├── decorator.ts
    │   └── container.ts
    │
    ├── commands/           # Command system
    │   ├── registry.ts
    │   └── service.ts
    │
    ├── configuration/      # Settings
    │   ├── model.ts
    │   └── service.ts
    │
    ├── storage/            # Persistence
    │   ├── keys.ts
    │   └── service.ts
    │
    ├── lifecycle/          # App lifecycle
    │   └── service.ts
    │
    ├── log/                # Logging
    │   └── common.ts
    │
    ├── environment/        # Platform detection
    │   └── common.ts
    │
    └── files/              # File operations
        ├── files.ts
        ├── fileService.ts
        └── diskFileSystemProvider.ts
```

## Layer Dependencies

| Layer | Module | Can Import From |
|-------|--------|-----------------|
| 0 | _primitives | - |
| 1 | _functional | Layer 0 |
| 2 | lifecycle | Layers 0-1 |
| 3 | event | Layers 0-2 |
| 4 | async | Layers 0-3 |
| 5 | instantiation | Layers 0-4 |
| 6+ | platform/* | Previous layers |

This layered structure prevents circular dependencies.
