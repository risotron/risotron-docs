# Lists & Trees

Virtualized list and tree components with selection, keyboard navigation, and drag-drop support.

---

## List

High-performance virtualized list with custom item rendering.

```typescript
import { List, unthemedListStyles } from 'risotron/renderer';

interface MyItem {
  id: string;
  name: string;
  icon: string;
}

// Define how to render items
const renderer: IListRenderer<MyItem, HTMLElement> = {
  templateId: 'my-item',
  renderTemplate: (container) => {
    return container; // Return template data
  },
  renderElement: (element, index, templateData) => {
    templateData.textContent = element.name;
  },
  disposeTemplate: (templateData) => {
    // Cleanup
  }
};

// Define item heights
const delegate: IListVirtualDelegate<MyItem> = {
  getHeight: (element) => 22,
  getTemplateId: (element) => 'my-item'
};

// Create list
const list = new List<MyItem>(
  'my-list',
  container,
  delegate,
  [renderer],
  {
    ...unthemedListStyles,
    keyboardSupport: true,
    mouseSupport: true,
    multipleSelectionSupport: true
  }
);

// Add data
const items: MyItem[] = [
  { id: '1', name: 'Item 1', icon: 'file' },
  { id: '2', name: 'Item 2', icon: 'folder' }
];
list.splice(0, 0, items);

// Events
list.onDidChangeFocus(e => console.log('Focus:', e.indexes));
list.onDidChangeSelection(e => console.log('Selection:', e.indexes));
list.onMouseClick(e => console.log('Clicked:', e.element));
list.onContextMenu(e => console.log('Context menu at:', e.index));

// Navigation
list.setFocus([0]);
list.focusNext();
list.focusPrevious();
list.focusFirst();
list.focusLast();

// Selection
list.setSelection([0, 1]);
const selected = list.getSelectedElements();

// Scrolling
list.reveal(10);  // Reveal item at index 10
list.scrollTop = 100;
```

### IListVirtualDelegate

```typescript
interface IListVirtualDelegate<T> {
  getHeight(element: T): number;
  getTemplateId(element: T): string;
  hasDynamicHeight?(element: T): boolean;
  setDynamicHeight?(element: T, height: number): void;
}
```

### IListRenderer

```typescript
interface IListRenderer<T, TTemplateData> {
  readonly templateId: string;
  renderTemplate(container: HTMLElement): TTemplateData;
  renderElement(element: T, index: number, templateData: TTemplateData, height?: number): void;
  disposeElement?(element: T, index: number, templateData: TTemplateData, height?: number): void;
  disposeTemplate(templateData: TTemplateData): void;
}
```

### IListOptions

| Option | Type | Description |
|--------|------|-------------|
| `keyboardSupport` | `boolean` | Enable keyboard navigation |
| `mouseSupport` | `boolean` | Enable mouse selection |
| `multipleSelectionSupport` | `boolean` | Allow multi-select |
| `horizontalScrolling` | `boolean` | Enable horizontal scroll |
| `smoothScrolling` | `boolean` | Smooth scroll animation |
| `accessibilityProvider` | `IListAccessibilityProvider` | ARIA support |
| `identityProvider` | `IIdentityProvider` | Item identity for diffing |
| `dnd` | `IListDragAndDrop` | Drag and drop handler |

### List Events

| Event | Description |
|-------|-------------|
| `onDidChangeFocus` | Focus changed |
| `onDidChangeSelection` | Selection changed |
| `onMouseClick` | Item clicked |
| `onMouseDblClick` | Item double-clicked |
| `onContextMenu` | Right-click/context menu |
| `onDidScroll` | Scroll position changed |
| `onKeyDown` | Key pressed |

---

## Table

Multi-column table with resizable columns.

```typescript
import { Table, unthemedTableStyles } from 'risotron/renderer';

interface Person {
  name: string;
  age: number;
  city: string;
}

// Define columns
const columns: ITableColumn<Person, string>[] = [
  {
    label: 'Name',
    weight: 2,
    templateId: 'name',
    renderer: {
      templateId: 'name',
      renderTemplate: (c) => c,
      renderElement: (person, i, td) => { td.textContent = person.name; },
      disposeTemplate: () => {}
    }
  },
  {
    label: 'Age',
    weight: 1,
    minimumWidth: 50,
    templateId: 'age',
    renderer: {
      templateId: 'age',
      renderTemplate: (c) => c,
      renderElement: (person, i, td) => { td.textContent = String(person.age); },
      disposeTemplate: () => {}
    }
  },
  {
    label: 'City',
    weight: 2,
    templateId: 'city',
    renderer: {
      templateId: 'city',
      renderTemplate: (c) => c,
      renderElement: (person, i, td) => { td.textContent = person.city; },
      disposeTemplate: () => {}
    }
  }
];

// Virtual delegate
const delegate: ITableVirtualDelegate<Person> = {
  getHeight: () => 24
};

// Create table
const table = new Table<Person>(
  'people-table',
  container,
  delegate,
  columns,
  [/* renderers from columns */],
  {
    ...unthemedTableStyles
  }
);

// Add rows
table.splice(0, 0, [
  { name: 'Alice', age: 30, city: 'New York' },
  { name: 'Bob', age: 25, city: 'London' }
]);

// Resize column
table.resizeColumn(0, 200);  // Column index, new width

// Layout
table.layout(800, 400);
```

### ITableColumn

| Property | Type | Description |
|----------|------|-------------|
| `label` | `string` | Column header text |
| `weight` | `number` | Relative column width |
| `minimumWidth` | `number` | Minimum column width |
| `maximumWidth` | `number` | Maximum column width |
| `templateId` | `string` | Renderer template ID |
| `renderer` | `ITableRenderer` | Cell renderer |

---

## Tree

Hierarchical tree with expand/collapse, filtering, and async loading.

### IndexTree

Low-level tree with location-based access.

```typescript
import { IndexTree } from 'risotron/renderer';

interface MyNode {
  name: string;
  children?: MyNode[];
}

const tree = new IndexTree<MyNode>(
  'my-tree',
  container,
  delegate,
  [renderer],
  options
);

// Set children at location
tree.setChildren(null, [
  { element: { name: 'Root 1' }, children: [
    { element: { name: 'Child 1' } },
    { element: { name: 'Child 2' } }
  ]},
  { element: { name: 'Root 2' } }
]);
```

### DataTree

Tree with identity provider for efficient updates.

```typescript
import { DataTree } from 'risotron/renderer';

const tree = new DataTree<MyNode, void>(
  'data-tree',
  container,
  delegate,
  [renderer],
  {
    identityProvider: {
      getId: (node) => node.id
    },
    accessibilityProvider: {
      getAriaLabel: (node) => node.name
    }
  }
);

// Provide children getter
tree.setInput(rootNode, {
  getChildren: (node) => node.children || []
});

// Update specific node
tree.updateChildren(someNode);
```

### AsyncDataTree

Tree with lazy-loading children.

```typescript
import { AsyncDataTree } from 'risotron/renderer';

interface FileNode {
  path: string;
  name: string;
  isDirectory: boolean;
}

const tree = new AsyncDataTree<FileNode, FileNode>(
  'file-tree',
  container,
  delegate,
  [renderer],
  {
    // Async data source
    getChildren: async (node) => {
      if (!node) {
        return [{ path: '/', name: '/', isDirectory: true }];
      }
      if (node.isDirectory) {
        return await fetchChildren(node.path);
      }
      return [];
    },
    hasChildren: (node) => node.isDirectory,
    identityProvider: {
      getId: (node) => node.path
    }
  }
);

// Set root
await tree.setInput(null);

// Expand node
await tree.expand(node);

// Collapse all
tree.collapseAll();

// Refresh
await tree.updateChildren(node);
```

### CompressibleObjectTree

Tree with compressed nodes (like VS Code's file explorer).

```typescript
import { CompressibleObjectTree } from 'risotron/renderer';

// Compresses single-child paths: a/b/c instead of a > b > c
const tree = new CompressibleObjectTree<FileNode, void>(
  'compressed-tree',
  container,
  delegate,
  [renderer],
  options
);
```

### Tree Renderer

```typescript
interface ITreeRenderer<T, TFilterData, TTemplateData> extends IListRenderer<ITreeNode<T, TFilterData>, TTemplateData> {
  renderTwistie?(element: T, twistieElement: HTMLElement): void;
}

// ITreeNode wraps your element
interface ITreeNode<T, TFilterData = void> {
  readonly element: T;
  readonly parent: ITreeNode<T, TFilterData> | undefined;
  readonly children: ITreeNode<T, TFilterData>[];
  readonly depth: number;
  readonly collapsible: boolean;
  readonly collapsed: boolean;
  readonly visible: boolean;
  readonly visibleChildrenCount: number;
}
```

### Tree Options

| Option | Type | Description |
|--------|------|-------------|
| `collapseByDefault` | `boolean` | Start collapsed |
| `expandOnDoubleClick` | `boolean` | Double-click to expand |
| `expandOnlyOnTwistieClick` | `boolean` | Only twistie expands |
| `filter` | `ITreeFilter` | Filter nodes |
| `dnd` | `ITreeDragAndDrop` | Drag and drop |
| `identityProvider` | `IIdentityProvider` | Node identity |
| `keyboardNavigationLabelProvider` | Provider | Type navigation |

### Tree Methods

```typescript
// Expand/Collapse
tree.expand(node);
tree.collapse(node);
tree.toggleCollapsed(node);
tree.expandAll();
tree.collapseAll();
tree.expandTo(node);  // Expand parents to reveal

// Query
tree.isCollapsed(node);
tree.isCollapsible(node);
tree.getNode(node);

// Navigation
const nav = tree.navigate(startNode);
nav.next();
nav.previous();
nav.first();
nav.last();

// Filter
tree.refilter();

// View state (save/restore)
const state = tree.getViewState();
// Later: restore with options.viewState
```

### Tree Filtering

```typescript
const options = {
  filter: {
    filter: (element, parentVisibility) => {
      if (matchesSearch(element.name)) {
        return TreeVisibility.Visible;
      }
      return TreeVisibility.Recurse; // Check children
    }
  }
};

// Update filter
tree.refilter();
```

---

## Drag and Drop

### List DnD

```typescript
import { ListDragAndDrop } from 'risotron/renderer';

const dnd: IListDragAndDrop<MyItem> = {
  getDragURI: (element) => element.id,
  getDragLabel: (elements) => elements.map(e => e.name).join(', '),
  onDragStart: (data, originalEvent) => { /* setup */ },
  onDragOver: (data, targetElement, targetIndex, isSameList, originalEvent) => {
    return { accept: true, effect: { type: ListDragOverEffectType.Move } };
  },
  drop: (data, targetElement, targetIndex, isSameList, originalEvent) => {
    // Handle drop
  }
};
```

### Tree DnD

```typescript
const dnd: ITreeDragAndDrop<MyNode> = {
  getDragURI: (node) => node.id,
  getDragLabel: (nodes) => nodes[0].name,
  onDragOver: (data, target, targetIndex, targetSector, originalEvent) => {
    return { accept: true, effect: { type: 'move' }, feedback: [targetIndex] };
  },
  drop: async (data, target, targetIndex, targetSector, originalEvent) => {
    // Handle drop - can be async
  }
};
```

---

## Accessibility

```typescript
const accessibilityProvider: IListAccessibilityProvider<MyItem> = {
  getAriaLabel: (element) => element.name,
  getWidgetAriaLabel: () => 'My List',
  getWidgetRole: () => 'listbox',
  getAriaLevel: (element) => element.depth  // For trees
};

const list = new List('list', container, delegate, [renderer], {
  accessibilityProvider
});
```

---

## Best Practices

### 1. Efficient Rendering

```typescript
// Cache expensive computations
class MyRenderer implements IListRenderer<Item, TemplateData> {
  renderElement(element, index, templateData) {
    // Reuse DOM elements, just update content
    templateData.label.textContent = element.name;
    templateData.icon.className = `icon-${element.type}`;
  }
}
```

### 2. Identity Provider for Updates

```typescript
// Without identity: full re-render on splice
// With identity: efficient diff and update

const list = new List('list', container, delegate, [renderer], {
  identityProvider: {
    getId: (item) => item.id
  }
});
```

### 3. Dynamic Heights

```typescript
const delegate: IListVirtualDelegate<Item> = {
  getHeight: (item) => item.expanded ? 100 : 22,
  hasDynamicHeight: (item) => true,
  setDynamicHeight: (item, height) => {
    item.calculatedHeight = height;
  }
};
```

### 4. Keyboard Navigation Labels

```typescript
// Enable type-to-navigate
const list = new List('list', container, delegate, [renderer], {
  keyboardNavigationLabelProvider: {
    getKeyboardNavigationLabel: (item) => item.name
  }
});

// User types "abc" -> focuses first item starting with "abc"
```
