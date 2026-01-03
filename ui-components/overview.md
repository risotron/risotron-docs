# UI Components Overview

Risotron provides a comprehensive set of UI components ported from VSCode. All components follow consistent patterns for styling, lifecycle management, and accessibility.

## Import

```typescript
// Import from renderer index
import {
  Button,
  Toggle,
  InputBox,
  SplitView,
  Menu,
  // ... more components
} from 'risotron/renderer';
```

---

## Component Categories

| Category | Components |
|----------|------------|
| [Form Controls](./form-controls.md) | Button, Toggle, Checkbox, InputBox, SelectBox, Radio, FindInput |
| [Layout](./layout.md) | SplitView, Grid, Sash, Resizable, CenteredViewLayout, PaneView |
| [Lists & Trees](./lists-trees.md) | List, Table, Tree (IndexTree, DataTree, AsyncDataTree) |
| [Navigation](./navigation.md) | Menu, MenuBar, Toolbar, ActionBar, Breadcrumbs, Dropdown |
| [Overlays](./overlays.md) | ContextView, Dialog, Hover |
| [Feedback & Text](./feedback-text.md) | ProgressBar, CountBadge, HighlightedLabel, IconLabel, KeybindingLabel, SeverityIcon |
| [Scrolling](./scrolling.md) | ScrollableElement, SmoothScrollableElement |

---

## Core Concepts

### Widget Base Class

All UI components extend the `Widget` base class from `Disposable`:

```typescript
import { Widget } from 'risotron/renderer';

class MyWidget extends Widget {
  constructor(container: HTMLElement) {
    super();
    // Use this._register() to auto-cleanup subscriptions
    this._register(someEmitter.event(this.onEvent, this));
  }
}
```

**Widget interfaces:**

| Interface | Methods |
|-----------|---------|
| `IWidget` | `render()` |
| `IFocusableWidget` | `focus()`, `hasFocus()` |
| `IVisibleWidget` | `show()`, `hide()`, `isVisible()` |
| `ILayoutableWidget` | `layout()` |
| `IFullWidget` | All of the above |

### Styling Pattern

Components use a consistent styling pattern with `styles` option and `unthemed*Styles` defaults:

```typescript
import { Button, unthemedButtonStyles } from 'risotron/renderer';

// Use unthemed styles (for quick prototyping)
const button = new Button(container, {
  ...unthemedButtonStyles
});

// Or provide themed styles
const themedButton = new Button(container, {
  buttonBackground: '#007acc',
  buttonForeground: '#ffffff',
  buttonHoverBackground: '#0062a3',
  buttonBorder: 'transparent',
  // ...
});
```

### Disposable Pattern

All components implement `IDisposable`. Always dispose when done:

```typescript
const button = new Button(container, options);

// Later, when removing the component
button.dispose();

// Or use DisposableStore for multiple components
const store = new DisposableStore();
store.add(new Button(container1, options));
store.add(new InputBox(container2, options));

// Dispose all at once
store.dispose();
```

---

## CSS Files

CSS files are copied directly from VSCode and should NOT be modified. Import them in your renderer:

```typescript
// In your renderer entry point
import 'risotron/base/browser/ui/button/button.css';
import 'risotron/base/browser/ui/inputbox/inputBox.css';
// ... etc
```

Or use the bundled CSS (if available).

---

## Accessibility

Components include built-in accessibility support:

```typescript
import { ariaAlert, ariaStatus, setARIAContainer } from 'risotron/renderer';

// Set the container for ARIA announcements
setARIAContainer(document.body);

// Announce important changes
ariaAlert('File saved successfully');

// Status updates (less intrusive)
ariaStatus('3 items selected');
```

---

## Next Steps

- [Form Controls](./form-controls.md) - Buttons, inputs, toggles
- [Layout](./layout.md) - Split views, grids, resizing
- [Lists & Trees](./lists-trees.md) - Virtual scrolling lists and trees
