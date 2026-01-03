# Overlay Components

Components that appear above other content: context views, dialogs, and hovers.

---

## ContextView

Positioned overlay container for dropdowns, menus, tooltips, etc.

```typescript
import {
  ContextView,
  ContextViewDOMPosition,
  AnchorAlignment,
  AnchorPosition,
  layoutContextView
} from 'risotron/renderer';

// Create context view
const contextView = new ContextView(document.body, {
  useShadowDOM: false
});

// Show with delegate
contextView.show({
  // Anchor position (where to appear)
  getAnchor: () => ({
    x: buttonElement.getBoundingClientRect().left,
    y: buttonElement.getBoundingClientRect().bottom,
    width: buttonElement.offsetWidth,
    height: buttonElement.offsetHeight
  }),

  // Render content
  render: (container) => {
    container.textContent = 'Context content';
    return { dispose: () => {} };
  },

  // Layout when container resizes
  layout: () => {
    // Re-calculate position if needed
  },

  // Focus handling
  focus: () => {
    // Focus first focusable element
  },

  // Cleanup
  onHide: () => {
    console.log('Context view hidden');
  },

  // Positioning options
  anchorPosition: AnchorPosition.BELOW,
  anchorAlignment: AnchorAlignment.LEFT
});

// Hide
contextView.hide();

// Check visibility
if (contextView.isVisible()) {
  // ...
}

// Layout (call when window resizes)
contextView.layout();

// Cleanup
contextView.dispose();
```

### IDelegate Interface

```typescript
interface IDelegate {
  getAnchor(): IAnchor | HTMLElement;
  render(container: HTMLElement): IDisposable | null;
  layout?(): void;
  focus?(): void;
  onDOMEvent?(e: Event, activeElement: HTMLElement): void;
  onHide?(data?: unknown): void;

  // Positioning
  anchorPosition?: AnchorPosition;
  anchorAlignment?: AnchorAlignment;
  anchorAxisAlignment?: AnchorAxisAlignment;
  canRelayout?: boolean;
  layer?: number;
}
```

### AnchorPosition

| Value | Description |
|-------|-------------|
| `BELOW` | Below the anchor |
| `ABOVE` | Above the anchor |

### AnchorAlignment

| Value | Description |
|-------|-------------|
| `LEFT` | Align left edge |
| `RIGHT` | Align right edge |

### AnchorAxisAlignment

| Value | Description |
|-------|-------------|
| `VERTICAL` | Prefer vertical positioning |
| `HORIZONTAL` | Prefer horizontal positioning |

### Layout Helper

```typescript
import { layoutContextView, LayoutAnchorPosition, LayoutAnchorMode } from 'risotron/renderer';

// Calculate optimal position
const position = layoutContextView({
  anchorPosition: LayoutAnchorPosition.BELOW,
  anchorSize: 100,
  anchorOffset: 10,
  viewportSize: window.innerHeight,
  viewSize: 200,
  anchorMode: LayoutAnchorMode.ALIGN
});
```

---

## Dialog

Modal dialog with buttons, inputs, and checkbox.

```typescript
import { Dialog, DialogContentsAlignment, unthemedDialogStyles } from 'risotron/renderer';

// Simple confirmation dialog
const dialog = new Dialog(
  container,
  'Confirm Delete',                      // Title
  ['Delete', 'Cancel'],                  // Buttons
  {
    detail: 'Are you sure you want to delete this file?',
    type: 'warning',                     // 'info' | 'warning' | 'error' | 'pending'
    cancelId: 1,                         // Index of cancel button
    dialogStyles: unthemedDialogStyles
  }
);

const result = await dialog.show();
console.log('Button clicked:', result.button);  // 0 = Delete, 1 = Cancel

dialog.dispose();
```

### Dialog with Checkbox

```typescript
const dialog = new Dialog(
  container,
  'Delete Files',
  ['Delete', 'Cancel'],
  {
    detail: 'This action cannot be undone.',
    checkboxLabel: "Don't ask again",
    checkboxChecked: false
  }
);

const result = await dialog.show();
if (result.button === 0) {
  if (result.checkboxChecked) {
    // Remember choice
  }
  deleteFiles();
}
```

### Dialog with Input

```typescript
const dialog = new Dialog(
  container,
  'Rename File',
  ['Rename', 'Cancel'],
  {
    inputs: [
      {
        placeholder: 'Enter new name',
        value: 'current-name.txt',
        type: 'text'
      }
    ],
    inputBoxStyles: unthemedInputBoxStyles
  }
);

const result = await dialog.show();
if (result.button === 0 && result.values) {
  const newName = result.values[0];
  renameFile(newName);
}
```

### Custom Dialog Content

```typescript
const dialog = new Dialog(
  container,
  'Custom Dialog',
  ['OK'],
  {
    renderBody: (container) => {
      // Render custom content
      const list = document.createElement('ul');
      items.forEach(item => {
        const li = document.createElement('li');
        li.textContent = item.name;
        list.appendChild(li);
      });
      container.appendChild(list);
    },
    renderFooter: (container) => {
      // Custom footer
      container.textContent = 'Footer content';
    }
  }
);
```

### IDialogOptions

| Option | Type | Description |
|--------|------|-------------|
| `detail` | `string` | Secondary message |
| `type` | `string` | Icon type: 'info', 'warning', 'error', 'pending' |
| `cancelId` | `number` | Index of cancel button |
| `checkboxLabel` | `string` | Checkbox text |
| `checkboxChecked` | `boolean` | Initial checkbox state |
| `inputs` | `IDialogInputOptions[]` | Input fields |
| `disableCloseAction` | `boolean` | Disable Escape to close |
| `disableCloseButton` | `boolean` | Hide X button |
| `disableDefaultAction` | `boolean` | Disable Enter to confirm |
| `alignment` | `DialogContentsAlignment` | Content alignment |
| `renderBody` | `function` | Custom body renderer |
| `renderFooter` | `function` | Custom footer renderer |

### IDialogResult

| Property | Type | Description |
|----------|------|-------------|
| `button` | `number` | Index of clicked button |
| `checkboxChecked` | `boolean` | Checkbox state |
| `values` | `string[]` | Input field values |

### IDialogStyles

| Property | Description |
|----------|-------------|
| `dialogBackground` | Background color |
| `dialogForeground` | Text color |
| `dialogBorder` | Border color |
| `dialogShadow` | Box shadow |
| `infoIconForeground` | Info icon color |
| `warningIconForeground` | Warning icon color |
| `errorIconForeground` | Error icon color |
| `textLinkForeground` | Link color |

---

## Hover

Tooltip and hover content display.

```typescript
import {
  HoverWidget,
  HoverAction,
  HoverPosition,
  getDefaultHoverDelegate
} from 'risotron/renderer';

// Create hover widget
const hover = new HoverWidget();
document.body.appendChild(hover.containerDomNode);

// Set content
hover.contentsDomNode.textContent = 'Hover content here';

// Position
hover.containerDomNode.style.position = 'absolute';
hover.containerDomNode.style.left = '100px';
hover.containerDomNode.style.top = '200px';

// Notify content changed (updates scrollbar)
hover.onContentsChanged();
```

### HoverAction

Clickable action in hover tooltip.

```typescript
const action = new HoverAction(container, {
  label: 'Click me',
  iconClass: 'codicon-run',
  commandId: 'myCommand',
  run: (target) => {
    console.log('Action clicked');
  }
});

// Enable/disable
action.setEnabled(true);

// Render
action.render(container);
```

### HoverPosition

| Value | Description |
|-------|-------------|
| `ABOVE` | Show above target |
| `BELOW` | Show below target |
| `LEFT` | Show left of target |
| `RIGHT` | Show right of target |

### Hover Delegate

Use hover delegates for managed hover behavior.

```typescript
import { getDefaultHoverDelegate, createInstantHoverDelegate } from 'risotron/renderer';

// Standard delay hover
const hoverDelegate = getDefaultHoverDelegate('element');

// Instant hover (no delay)
const instantHover = createInstantHoverDelegate();

// Use with buttons, inputs, etc.
const button = new Button(container, {
  hoverDelegate: hoverDelegate
});
```

### Managed Hover

For components that need managed hover state:

```typescript
import { setBaseLayerHoverDelegate, getBaseLayerHoverDelegate } from 'risotron/renderer';

// Set global base layer delegate
setBaseLayerHoverDelegate({
  showHover: (options) => {
    // Show hover
    return { dispose: () => {} };
  },
  hideHover: () => {
    // Hide hover
  }
});

// Get current delegate
const delegate = getBaseLayerHoverDelegate();
```

### IManagedHoverOptions

```typescript
interface IManagedHoverOptions {
  content: string | HTMLElement | IMarkdownString;
  target: HTMLElement;
  actions?: IHoverAction[];
  position?: HoverPosition;
  showPointer?: boolean;
  skipFadeInAnimation?: boolean;
}
```

---

## ContextView Service Pattern

For application-wide context view management:

```typescript
import { IContextViewService, ContextViewService } from 'risotron/renderer';

// Create service
const contextViewService = new ContextViewService(document.body);

// Show context view
contextViewService.showContextView({
  getAnchor: () => anchorElement,
  render: (container) => {
    // Render content
    return toDisposable(() => { /* cleanup */ });
  }
});

// Hide
contextViewService.hideContextView();

// Layout
contextViewService.layout();
```

---

## Best Practices

### 1. Always Dispose

```typescript
const dialog = new Dialog(container, 'Title', ['OK']);
try {
  const result = await dialog.show();
  // Handle result
} finally {
  dialog.dispose();
}
```

### 2. Focus Management

```typescript
const contextView = new ContextView(container);

contextView.show({
  getAnchor: () => anchor,
  render: (container) => {
    const input = document.createElement('input');
    container.appendChild(input);
    return toDisposable(() => {});
  },
  focus: () => {
    // Focus first element when shown
    container.querySelector('input')?.focus();
  },
  onHide: () => {
    // Return focus when hidden
    previouslyFocused?.focus();
  }
});
```

### 3. Handle Escape Key

```typescript
const dialog = new Dialog(container, 'Title', ['OK', 'Cancel'], {
  cancelId: 1,  // Escape triggers index 1 (Cancel)
  keyEventProcessor: (e) => {
    if (e.key === 'Escape') {
      // Custom escape handling
    }
  }
});
```

### 4. Shadow DOM Isolation

```typescript
// Use Shadow DOM for CSS isolation
const contextView = new ContextView(container, {
  useShadowDOM: true
});
```

### 5. Layer Management

```typescript
// Higher layer appears on top
contextView.show({
  layer: 1,  // Default is 0
  // ...
});

// Show another on higher layer
anotherContextView.show({
  layer: 2,  // Appears above layer 1
  // ...
});
```
