## Documentation

**Getting Started**
- [Introduction](getting-started/introduction.md)
- [Installation](getting-started/installation.md)
- [File Structure](getting-started/file-structure.md)
- [Quick Start](getting-started/quick-start.md)

**Core Concepts**
- [Architecture](core-concepts/architecture.md)
- [Lifecycle](core-concepts/lifecycle.md)
- [Dependency Injection](core-concepts/dependency-injection.md)

**Services**
- [Overview](services/overview.md)

**UI Components**
- [Overview](ui-components/overview.md)
- [Form Controls](ui-components/form-controls.md) - Button, Toggle, InputBox, SelectBox, Radio
- [Layout](ui-components/layout.md) - SplitView, Grid, Sash, Resizable
- [Lists & Trees](ui-components/lists-trees.md) - List, Table, Tree
- [Navigation](ui-components/navigation.md) - Menu, Toolbar, ActionBar, Breadcrumbs
- [Overlays](ui-components/overlays.md) - ContextView, Dialog, Hover
- [Feedback & Text](ui-components/feedback-text.md) - ProgressBar, CountBadge, Labels

**Theming**
- [Overview](theming/overview.md) - ThemeService, CSS variables
- [Color Registry](theming/color-registry.md) - Registering and using colors
- [Built-in Colors](theming/built-in-colors.md) - Available color definitions

**Utilities**
- [Disposable](utilities/disposable.md)
- [Events](utilities/events.md)
- [Async](utilities/async.md)
- [URI & Buffer](utilities/uri-buffer.md)

---

## Appendix: Module Categories

```
risotron/
│
├── Layer 1: CORE
│   │
│   ├── Core Utilities (12 files)
│   │   ├── Primitives
│   │   ├── Functional
│   │   ├── Lifecycle
│   │   ├── Events
│   │   ├── Async
│   │   ├── Data Structures (URI, Buffer, Diff)
│   │   ├── Icons
│   │   ├── IPC
│   │   └── Aggregated
│   │
│   ├── DOM & Browser (3 files)
│   │   ├── DOM Utilities
│   │   ├── Keyboard & Mouse
│   │   └── Aggregated
│   │
│   ├── UI Components (34 files)
│   │   ├── Form Controls (Button, Toggle, InputBox, SelectBox, Radio, FindInput, ThemeToggle)
│   │   ├── Layout (Grid, SplitView, Sash, Scrollbar, Resizable, Centered)
│   │   ├── Data Display (List, Table, Tree, IconLabel, KeybindingLabel, HighlightedLabel)
│   │   ├── Navigation (Menu, Toolbar, ActionBar, Breadcrumbs, Dropdown)
│   │   ├── Overlays (ContextView, Dialog, Hover)
│   │   ├── Feedback (ProgressBar, CountBadge, SeverityIcon)
│   │   ├── Accessibility (Aria)
│   │   ├── Misc (DnD, MouseCursor)
│   │   └── Aggregated
│   │
│   ├── Application Services (11 files)
│   │   ├── Dependency Injection
│   │   ├── Commands
│   │   ├── Configuration
│   │   ├── Storage
│   │   ├── Lifecycle
│   │   ├── Logging
│   │   ├── IPC
│   │   ├── Window
│   │   └── Aggregated
│   │
│   ├── UI Services (21 files)
│   │   ├── Notifications
│   │   ├── Dialogs
│   │   ├── Quick Input
│   │   ├── Progress
│   │   ├── Hover
│   │   ├── Status Bar
│   │   ├── Context View
│   │   ├── Menubar
│   │   ├── Theming
│   │   └── Theme Colors
│   │
│   ├── System Services (5 files)
│   │   ├── Clipboard
│   │   ├── Files
│   │   └── Environment
│   │
│   ├── Editor Services (8 files)
│   │   ├── Context Keys
│   │   ├── Keybindings
│   │   └── Undo/Redo
│   │
│   ├── Workspace (2 files)
│   │   └── Workspace Core
│   │
│   └── Editor (1 file)
│       └── Model
│
├── Layer 2: EXTENSION SYSTEM
│   │
│   └── Extensions (15 files)
│       ├── Extension Types
│       ├── Extension Management
│       │   ├── Common
│       │   └── Browser
│       ├── Extension Runtime
│       ├── Extension Host
│       ├── Extension Browser Host
│       ├── Extension API
│       │   ├── Common
│       │   └── Worker
│       └── Extension UI
│           ├── Types
│           └── Components
│
├── Layer 3: PARTS LIBRARY
│   │
│   └── Parts (3 files)
│       ├── Notifications
│       ├── Progress
│       └── Aggregated
│
├── Layer 4+: WORKBENCH SHELL
│   │
│   └── Workbench (4 files)
│       ├── Root
│       ├── Common
│       ├── Browser
│       └── Services
│
└── Other
    │
    ├── Entry Points (2 files)
    │   ├── Main
    │   └── Renderer
    │
    └── Sample Extensions (1 file)
        └── Test Data
```
