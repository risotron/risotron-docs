# Layout Components

Components for creating resizable, split, and grid-based layouts.

---

## Sash

Draggable resize handle between panels.

```typescript
import { Sash, Orientation, SashState, setGlobalSashSize } from 'risotron/renderer';

// Configure global sash size (default: 4px)
setGlobalSashSize(6);

// Create a vertical sash (resizes horizontally)
const sash = new Sash(container, layoutProvider, {
  orientation: Orientation.VERTICAL,
  size: 4
});

// Layout provider tells sash where to position itself
const layoutProvider = {
  getVerticalSashLeft: () => 200,
  getVerticalSashTop: () => 0,
  getVerticalSashHeight: () => container.clientHeight
};

// Events
sash.onDidStart(e => console.log('Drag started at', e.startX, e.startY));
sash.onDidChange(e => console.log('Dragging to', e.currentX, e.currentY));
sash.onDidEnd(() => console.log('Drag ended'));
sash.onDidReset(() => console.log('Double-click reset'));

// Control state
sash.state = SashState.Enabled;    // Normal
sash.state = SashState.AtMinimum;  // At minimum (arrow cursor)
sash.state = SashState.AtMaximum;  // At maximum (arrow cursor)
sash.state = SashState.Disabled;   // Disabled (hidden)
```

### ISashEvent

| Property | Type | Description |
|----------|------|-------------|
| `startX` | `number` | Initial X position |
| `startY` | `number` | Initial Y position |
| `currentX` | `number` | Current X position |
| `currentY` | `number` | Current Y position |
| `altKey` | `boolean` | Alt key pressed |

### Orientation

| Value | Description |
|-------|-------------|
| `Orientation.VERTICAL` | Vertical sash (horizontal resize) |
| `Orientation.HORIZONTAL` | Horizontal sash (vertical resize) |

### SashState

| State | Effect |
|-------|--------|
| `Enabled` | Normal resize cursor |
| `AtMinimum` | Arrow pointing to expand direction |
| `AtMaximum` | Arrow pointing to shrink direction |
| `Disabled` | Hidden, no interaction |

---

## SplitView

Container that splits space between multiple views with resizable sashes.

```typescript
import { SplitView, Orientation, Sizing } from 'risotron/renderer';

const splitView = new SplitView(container, {
  orientation: Orientation.HORIZONTAL,
  proportionalLayout: true
});

// Define views implementing IView
const sidebar: IView = {
  element: document.createElement('div'),
  minimumSize: 100,
  maximumSize: 300,
  onDidChange: Event.None,
  layout: (size) => { /* resize content */ }
};

const main: IView = {
  element: document.createElement('div'),
  minimumSize: 200,
  maximumSize: Number.POSITIVE_INFINITY,
  onDidChange: Event.None,
  layout: (size) => { /* resize content */ }
};

// Add views with sizing strategy
splitView.addView(sidebar, Sizing.Distribute);
splitView.addView(main, Sizing.Distribute);

// Layout
splitView.layout(container.clientWidth);

// Events
splitView.onDidSashChange(() => console.log('Sash moved'));
splitView.onDidSashReset(index => console.log('Sash', index, 'reset'));

// Programmatic control
splitView.resizeView(0, 150);  // Resize first view to 150px
splitView.setViewVisible(0, false);  // Hide first view
splitView.distributeViewSizes();  // Distribute space equally
```

### IView Interface

```typescript
interface IView {
  readonly element: HTMLElement;
  readonly minimumSize: number;
  readonly maximumSize: number;
  readonly onDidChange: Event<number | undefined>;

  layout(size: number, offset: number, context?: LayoutContext): void;
  setVisible?(visible: boolean): void;

  // Optional
  readonly priority?: LayoutPriority;
  readonly snap?: boolean;
  readonly proportionalLayout?: boolean;
}
```

### Sizing Strategies

```typescript
import { Sizing } from 'risotron/renderer';

// Distribute space equally
splitView.addView(view, Sizing.Distribute);

// Split remaining space with next view
splitView.addView(view, Sizing.Split(0));  // Split with view at index 0

// Invisible (for collapsible views)
splitView.addView(view, Sizing.Invisible(-1));

// Auto-size based on content
splitView.addView(view, { type: 'auto', size: 200 });
```

### LayoutPriority

| Priority | Behavior |
|----------|----------|
| `LayoutPriority.Normal` | Default priority |
| `LayoutPriority.High` | Resize first when space changes |
| `LayoutPriority.Low` | Resize last |

---

## PaneView

Collapsible panels with headers (accordion-style).

```typescript
import { PaneView, Pane, unthemedPaneStyles } from 'risotron/renderer';

// Create pane view container
const paneView = new PaneView(container, { orientation: Orientation.VERTICAL });

// Create a pane
class MyPane extends Pane {
  constructor() {
    super({
      expanded: true,
      minimumBodySize: 100,
      maximumBodySize: Number.POSITIVE_INFINITY,
      title: 'My Panel',
      ...unthemedPaneStyles
    });
  }

  protected renderHeader(container: HTMLElement): void {
    container.textContent = 'Panel Header';
  }

  protected renderBody(container: HTMLElement): void {
    container.textContent = 'Panel Content';
  }

  protected layoutBody(height: number, width: number): void {
    // Layout body content
  }
}

const pane = new MyPane();
paneView.addPane(pane);

// Collapse/expand
pane.setExpanded(false);

// Events
pane.onDidChangeExpansionState(expanded => {
  console.log('Expanded:', expanded);
});

// Drag and drop reordering
paneView.onDidDrop(e => {
  console.log('Pane dropped');
});
```

### IPaneOptions

| Option | Type | Description |
|--------|------|-------------|
| `expanded` | `boolean` | Initial expanded state |
| `minimumBodySize` | `number` | Minimum body height |
| `maximumBodySize` | `number` | Maximum body height |
| `title` | `string` | Header title |
| `titleDescription` | `string` | Header subtitle |
| `orientation` | `Orientation` | Layout direction |

### IPaneStyles

| Property | Description |
|----------|-------------|
| `headerBackground` | Header background color |
| `headerForeground` | Header text color |
| `headerBorder` | Header border color |
| `dropBackground` | Background when dragging over |
| `leftBorder` | Left border color |

---

## Grid

Two-dimensional grid of resizable views.

```typescript
import { Grid, Direction } from 'risotron/renderer';

// Create grid with root view
const grid = new Grid(rootView);
container.appendChild(grid.element);

// Add views
grid.addView(newView, Sizing.Distribute, rootView, Direction.Right);
grid.addView(anotherView, Sizing.Distribute, newView, Direction.Down);

// Layout
grid.layout(width, height);

// Navigate
const views = grid.getViews();
const location = grid.getViewLocation(someView);

// Resize
grid.resizeView(view, { width: 300, height: 200 });

// Visibility
grid.setViewVisible(view, false);

// Maximize
grid.maximizeView(view);
grid.exitMaximizedView();
```

### Direction

| Value | Description |
|-------|-------------|
| `Direction.Up` | Add above |
| `Direction.Down` | Add below |
| `Direction.Left` | Add to left |
| `Direction.Right` | Add to right |

### Grid View Interface

```typescript
interface IView {
  readonly element: HTMLElement;
  readonly minimumWidth: number;
  readonly maximumWidth: number;
  readonly minimumHeight: number;
  readonly maximumHeight: number;
  readonly onDidChange: Event<{ width?: number; height?: number } | undefined>;

  layout(width: number, height: number, top: number, left: number): void;

  // Optional preferred sizes
  readonly preferredWidth?: number;
  readonly preferredHeight?: number;
}
```

---

## ResizableHTMLElement

Wrapper that makes any element resizable from edges/corners.

```typescript
import { ResizableHTMLElement } from 'risotron/renderer';

const resizable = new ResizableHTMLElement();
container.appendChild(resizable.domNode);

// Put content inside
resizable.domNode.appendChild(myContent);

// Configure
resizable.minSize = { width: 100, height: 100 };
resizable.maxSize = { width: 800, height: 600 };
resizable.preferredSize = { width: 400, height: 300 };

// Enable specific edges
resizable.enableSashes(true, true, true, true); // north, east, south, west

// Layout
resizable.layout(300, 200);

// Events
resizable.onDidWillResize(() => console.log('About to resize'));
resizable.onDidResize(e => {
  console.log('Size:', e.dimension);
  console.log('Direction:', { north: e.north, east: e.east, south: e.south, west: e.west });
  if (e.done) {
    console.log('Resize complete');
  }
});
```

### IResizeEvent

| Property | Type | Description |
|----------|------|-------------|
| `dimension` | `Dimension` | New width/height |
| `north` | `boolean` | Resizing from top |
| `east` | `boolean` | Resizing from right |
| `south` | `boolean` | Resizing from bottom |
| `west` | `boolean` | Resizing from left |
| `done` | `boolean` | Resize finished |

---

## CenteredViewLayout

Centers content with adjustable margins (like VS Code's centered layout).

```typescript
import { CenteredViewLayout } from 'risotron/renderer';

const centered = new CenteredViewLayout(container, contentView, {
  background: '#1e1e1e'
});

// Activate centered mode
centered.activate(true);

// Check state
if (centered.isActive()) {
  console.log('Centered mode enabled');
}

// Layout
centered.layout(width, height, top, left);

// Configure fixed width
centered.setFixedWidth(true);

// Boundary sashes (for integration with other splitviews)
centered.boundarySashes = { left: leftSash, right: rightSash };
```

### CenteredViewState

```typescript
interface CenteredViewState {
  targetWidth: number;      // Desired content width
  leftMarginRatio: number;  // Left margin as ratio (0-0.5)
  rightMarginRatio: number; // Right margin as ratio (0-0.5)
}
```

---

## Best Practices

### 1. Implement IView Properly

```typescript
class MyView extends Disposable implements IView {
  readonly element = document.createElement('div');

  private _minimumSize = 100;
  private _maximumSize = Number.POSITIVE_INFINITY;

  private readonly _onDidChange = this._register(new Emitter<number>());
  readonly onDidChange = this._onDidChange.event;

  get minimumSize() { return this._minimumSize; }
  get maximumSize() { return this._maximumSize; }

  layout(size: number): void {
    this.element.style.width = `${size}px`;
    // Re-layout children
  }

  // Notify when size constraints change
  updateConstraints(min: number, max: number): void {
    this._minimumSize = min;
    this._maximumSize = max;
    this._onDidChange.fire(undefined);
  }
}
```

### 2. Handle Orientation Changes

```typescript
const splitView = new SplitView(container, {
  orientation: Orientation.HORIZONTAL
});

// Change orientation
splitView.orientation = Orientation.VERTICAL;
splitView.layout(newSize);
```

### 3. Save and Restore Layout

```typescript
// Save proportions before resize/reload
const proportions = splitView.saveProportions();
localStorage.setItem('layout', JSON.stringify(proportions));

// Restore
const saved = JSON.parse(localStorage.getItem('layout'));
// Re-add views with saved sizes
```

### 4. Snap to Close

```typescript
const view: IView = {
  minimumSize: 100,
  snap: true,  // Enable snap-to-close
  // ...
};

// View will collapse when dragged below minimum
```
