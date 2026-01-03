# Navigation Components

Components for menus, toolbars, action bars, and breadcrumb navigation.

---

## ActionBar

Horizontal or vertical bar of action items (buttons, toggles).

```typescript
import { ActionBar, ActionsOrientation, ActionViewItem } from 'risotron/renderer';

const actionBar = new ActionBar(container, {
  orientation: ActionsOrientation.HORIZONTAL,
  ariaLabel: 'Editor Actions'
});

// Define actions
const saveAction = {
  id: 'save',
  label: 'Save',
  tooltip: 'Save File (Ctrl+S)',
  class: 'codicon-save',
  enabled: true,
  run: () => saveFile()
};

const deleteAction = {
  id: 'delete',
  label: 'Delete',
  tooltip: 'Delete',
  class: 'codicon-trash',
  enabled: true,
  run: () => deleteItem()
};

// Add actions
actionBar.push([saveAction, deleteAction]);

// Events
actionBar.onDidRun(e => console.log('Action ran:', e.action.id));
actionBar.onWillRun(e => console.log('About to run:', e.action.id));
actionBar.onDidBlur(() => console.log('Lost focus'));

// Focus control
actionBar.focus();
actionBar.focusNext();
actionBar.focusPrevious();

// Update context
actionBar.context = { file: currentFile };

// Remove action
actionBar.pull(0);  // Remove first action

// Clear all
actionBar.clear();
```

### ActionsOrientation

| Value | Description |
|-------|-------------|
| `HORIZONTAL` | Left-to-right layout |
| `HORIZONTAL_REVERSE` | Right-to-left layout |
| `VERTICAL` | Top-to-bottom layout |
| `VERTICAL_REVERSE` | Bottom-to-top layout |

### IActionBarOptions

| Option | Type | Description |
|--------|------|-------------|
| `orientation` | `ActionsOrientation` | Layout direction |
| `ariaLabel` | `string` | Accessibility label |
| `ariaRole` | `string` | ARIA role (default: 'toolbar') |
| `actionRunner` | `IActionRunner` | Custom action executor |
| `actionViewItemProvider` | `function` | Custom view item factory |
| `context` | `any` | Context passed to actions |
| `focusOnlyEnabledItems` | `boolean` | Skip disabled in navigation |
| `hoverDelegate` | `IHoverDelegate` | Hover behavior |
| `triggerKeys` | `ActionTrigger` | Keyboard triggers |

### Custom Action View Items

```typescript
import { BaseActionViewItem, ActionViewItem } from 'risotron/renderer';

const actionBar = new ActionBar(container, {
  actionViewItemProvider: (action, options) => {
    if (action.id === 'special') {
      return new MyCustomViewItem(action, options);
    }
    return new ActionViewItem(action, action, options);
  }
});
```

---

## Menu

Dropdown menu with items, separators, and submenus.

```typescript
import { Menu, unthemedMenuStyles, HorizontalDirection, VerticalDirection } from 'risotron/renderer';

const actions = [
  { id: 'new', label: 'New File', run: () => createFile() },
  { id: 'open', label: 'Open...', run: () => openFile() },
  { class: 'separator' },  // Separator
  { id: 'save', label: 'Save', run: () => saveFile() },
  {
    id: 'export',
    label: 'Export',
    submenu: [  // Submenu
      { id: 'export-pdf', label: 'Export as PDF', run: () => {} },
      { id: 'export-html', label: 'Export as HTML', run: () => {} }
    ]
  }
];

const menu = new Menu(container, actions, {
  ...unthemedMenuStyles,
  ariaLabel: 'File Menu',
  enableMnemonics: true,
  expandDirection: {
    horizontal: HorizontalDirection.Right,
    vertical: VerticalDirection.Below
  },
  getKeyBinding: (action) => {
    if (action.id === 'save') return 'Ctrl+S';
    return undefined;
  }
});

// Cleanup
menu.dispose();
```

### IMenuOptions

| Option | Type | Description |
|--------|------|-------------|
| `ariaLabel` | `string` | Menu accessibility label |
| `enableMnemonics` | `boolean` | Enable Alt+letter navigation |
| `expandDirection` | `IMenuDirection` | Submenu expand direction |
| `getKeyBinding` | `function` | Keybinding label provider |
| `actionRunner` | `IActionRunner` | Custom action executor |
| `actionViewItemProvider` | `function` | Custom item renderer |
| `submenuIds` | `Set<string>` | Track nested submenus |

### IMenuStyles

| Property | Description |
|----------|-------------|
| `backgroundColor` | Menu background |
| `foregroundColor` | Text color |
| `selectionBackgroundColor` | Highlighted item background |
| `selectionForegroundColor` | Highlighted item text |
| `selectionBorderColor` | Highlighted item border |
| `separatorColor` | Separator line color |
| `borderColor` | Menu border |
| `shadowColor` | Drop shadow |
| `scrollbarSlider*` | Scrollbar colors |

### MenuBar

Application menu bar (File, Edit, View, etc.).

```typescript
import { MenuBar } from 'risotron/renderer';

const menubar = new MenuBar(container, {
  enableMnemonics: true,
  overflowBehavior: { maxItems: 10 }
});

// Add menus
menubar.setMenus([
  {
    label: 'File',
    actions: [
      { id: 'new', label: 'New', run: () => {} },
      { id: 'open', label: 'Open', run: () => {} }
    ]
  },
  {
    label: 'Edit',
    actions: [
      { id: 'undo', label: 'Undo', run: () => {} },
      { id: 'redo', label: 'Redo', run: () => {} }
    ]
  }
]);

// Update specific menu
menubar.updateMenu({ label: 'File', actions: newActions });
```

---

## ToolBar

Toolbar with overflow menu for actions that don't fit.

```typescript
import { ToolBar, unthemedToolBarStyles } from 'risotron/renderer';

const toolbar = new ToolBar(container, contextMenuService, {
  ...unthemedToolBarStyles,
  orientation: ActionsOrientation.HORIZONTAL,
  ariaLabel: 'Editor Toolbar',
  getKeyBinding: (action) => keybindingService.lookupKeybinding(action.id),
  renderDropdownAsChildElement: true
});

// Primary actions (always visible)
const primaryActions = [
  { id: 'run', label: 'Run', icon: Codicon.play, run: () => {} },
  { id: 'debug', label: 'Debug', icon: Codicon.bug, run: () => {} }
];

// Secondary actions (in overflow menu)
const secondaryActions = [
  { id: 'settings', label: 'Settings', run: () => {} },
  { id: 'help', label: 'Help', run: () => {} }
];

toolbar.setActions(primaryActions, secondaryActions);

// Events
toolbar.onDidChangeDropdownVisibility(visible => {
  console.log('Dropdown', visible ? 'opened' : 'closed');
});

// Focus
toolbar.focus();

// Clear
toolbar.clear();
```

### IToolBarOptions

| Option | Type | Description |
|--------|------|-------------|
| `orientation` | `ActionsOrientation` | Layout direction |
| `ariaLabel` | `string` | Accessibility label |
| `icon` | `boolean` | Show icons |
| `label` | `boolean` | Show labels |
| `moreIcon` | `ThemeIcon` | Overflow button icon |
| `toggleMenuTitle` | `string` | Overflow button tooltip |
| `getKeyBinding` | `function` | Keybinding provider |
| `actionViewItemProvider` | `function` | Custom item factory |
| `allowContextMenu` | `boolean` | Enable right-click menu |
| `renderDropdownAsChildElement` | `boolean` | Render dropdown in toolbar DOM |
| `highlightToggledItems` | `boolean` | Highlight active toggles |

### IToolBarStyles

| Property | Description |
|----------|-------------|
| `toolbarBackground` | Toolbar background |
| `toolbarForeground` | Icon/text color |
| `toolbarBorder` | Toolbar border |

---

## Breadcrumbs

Hierarchical path navigation.

```typescript
import { BreadcrumbsWidget, BreadcrumbsItem, unthemedBreadcrumbsStyles } from 'risotron/renderer';

// Create widget
const breadcrumbs = new BreadcrumbsWidget(container, {
  scrollbarShadow: '#000',
  separatorIcon: Codicon.chevronRight
});

breadcrumbs.style(unthemedBreadcrumbsStyles);

// Create items
class FileBreadcrumbItem extends BreadcrumbsItem {
  constructor(public readonly file: FileInfo) {
    super();
  }

  equals(other: BreadcrumbsItem): boolean {
    return other instanceof FileBreadcrumbItem && other.file.path === this.file.path;
  }

  render(container: HTMLElement): void {
    container.textContent = this.file.name;
    container.title = this.file.path;
  }
}

// Set items
const items = [
  new FileBreadcrumbItem({ name: 'src', path: '/src' }),
  new FileBreadcrumbItem({ name: 'components', path: '/src/components' }),
  new FileBreadcrumbItem({ name: 'Button.tsx', path: '/src/components/Button.tsx' })
];

breadcrumbs.setItems(items);

// Events
breadcrumbs.onDidSelectItem(e => {
  console.log('Selected:', e.item);
  navigateTo(e.item.file.path);
});

breadcrumbs.onDidFocusItem(e => {
  console.log('Focused:', e.item);
});

// Navigation
breadcrumbs.focusNext();
breadcrumbs.focusPrev();
breadcrumbs.setSelection(items[1]);
breadcrumbs.reveal(items[2]);

// Layout
breadcrumbs.layout({ width: 400, height: 22 });
```

### IBreadcrumbsWidgetStyles

| Property | Description |
|----------|-------------|
| `breadcrumbsBackground` | Widget background |
| `breadcrumbsForeground` | Text color |
| `breadcrumbsHoverForeground` | Text color on hover |
| `breadcrumbsFocusForeground` | Text color when focused |
| `breadcrumbsFocusAndSelectionForeground` | Text color when focused and selected |

### BreadcrumbsItem

Abstract base class for items:

```typescript
abstract class BreadcrumbsItem {
  abstract equals(other: BreadcrumbsItem): boolean;
  abstract render(container: HTMLElement): void;
  dispose(): void { /* cleanup */ }
}
```

---

## Dropdown

Dropdown button with menu.

```typescript
import { BaseDropdown, DropdownMenu, DropdownMenuActionViewItem } from 'risotron/renderer';

// Simple dropdown menu
const dropdown = new DropdownMenu(container, {
  contextMenuProvider,
  actions: [
    { id: 'option1', label: 'Option 1', run: () => {} },
    { id: 'option2', label: 'Option 2', run: () => {} }
  ]
});

// Or as action view item (for use in ActionBar/ToolBar)
const dropdownItem = new DropdownMenuActionViewItem(
  baseAction,
  { getActions: () => menuActions },
  contextMenuProvider,
  {
    classNames: ['my-dropdown'],
    menuAsChild: true
  }
);
```

### ActionWithDropdownActionViewItem

Split button with main action and dropdown.

```typescript
import { ActionWithDropdownActionViewItem } from 'risotron/renderer';

const splitButton = new ActionWithDropdownActionViewItem(
  mainAction,
  dropdownActions,
  contextMenuProvider,
  {
    classNames: ['split-button'],
    menuAsChild: true
  }
);

// Main button triggers mainAction
// Arrow triggers dropdown menu
```

---

## Best Practices

### 1. Use IAction Interface

```typescript
interface IAction {
  id: string;
  label: string;
  tooltip?: string;
  class?: string;  // CSS class for icon
  enabled: boolean;
  checked?: boolean;  // For toggles
  run(context?: any): Promise<void> | void;
}
```

### 2. Provide Keybindings

```typescript
const toolbar = new ToolBar(container, contextMenu, {
  getKeyBinding: (action) => {
    const binding = keybindingService.lookupKeybinding(action.id);
    return binding?.getLabel();
  }
});
```

### 3. Enable Mnemonics

```typescript
// Use & in label to mark mnemonic
const actions = [
  { id: 'new', label: '&New File', run: () => {} },  // Alt+N
  { id: 'open', label: '&Open', run: () => {} },     // Alt+O
  { id: 'save', label: '&Save', run: () => {} }      // Alt+S
];

const menu = new Menu(container, actions, {
  enableMnemonics: true
});
```

### 4. Context for Actions

```typescript
const actionBar = new ActionBar(container, {
  context: { editor: activeEditor }
});

// Actions receive context
const action = {
  id: 'format',
  run: (context) => {
    context.editor.formatDocument();
  }
};
```

### 5. Cleanup

```typescript
class MyToolbarView extends Disposable {
  private toolbar: ToolBar;

  constructor(container: HTMLElement) {
    super();
    this.toolbar = this._register(new ToolBar(container, contextMenu, options));
  }
}
```
