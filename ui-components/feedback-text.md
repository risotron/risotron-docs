# Feedback & Text Components

Components for displaying progress, counts, and formatted text with highlights.

---

## ProgressBar

Horizontal progress indicator with determinate and indeterminate modes.

```typescript
import { ProgressBar, unthemedProgressBarOptions } from 'risotron/renderer';

const progress = new ProgressBar(container, {
  ...unthemedProgressBarOptions
});

// Indeterminate (unknown duration)
progress.infinite();

// Long-running infinite (different animation)
progress.infiniteLongRunning();

// Determinate progress
progress.total(100);       // Set total
progress.worked(25);       // Set progress to 25/100
progress.setWorked(50);    // Set progress directly to 50
progress.done();           // Complete

// Show/hide
progress.show();           // Show immediately
progress.show(500);        // Show after 500ms delay
progress.hide();

// Stop (reset to initial state)
progress.stop();
```

### Methods

| Method | Description |
|--------|-------------|
| `infinite()` | Start indeterminate animation |
| `infiniteLongRunning()` | Slower animation for long tasks |
| `total(n)` | Set total work units |
| `worked(n)` | Add to current progress |
| `setWorked(n)` | Set progress to specific value |
| `done()` | Mark as complete |
| `show(delay?)` | Show progress bar |
| `hide()` | Hide progress bar |
| `stop()` | Stop and reset |

### IProgressBarStyles

| Property | Description |
|----------|-------------|
| `progressBarBackground` | Progress bar fill color |

### Accessibility Signal

For screen reader notifications:

```typescript
import { getProgressAccessibilitySignalScheduler, setProgressAccessibilitySignalScheduler } from 'risotron/renderer';

// Set custom scheduler for accessibility signals
setProgressAccessibilitySignalScheduler({
  scheduleSignal: (signal) => {
    // Announce progress to screen readers
  }
});
```

---

## CountBadge

Numeric badge (e.g., notification count, item count).

```typescript
import { CountBadge, unthemedCountStyles } from 'risotron/renderer';

const badge = new CountBadge(container, {
  count: 5,
  countFormat: '{0}',           // Format string
  titleFormat: '{0} items'      // Tooltip format
}, unthemedCountStyles);

// Update count
badge.setCount(10);

// Update formats
badge.setCountFormat('{0}+');   // Shows "10+"
badge.setTitleFormat('{0} notifications');
```

### ICountBadgeOptions

| Option | Type | Description |
|--------|------|-------------|
| `count` | `number` | Initial count |
| `countFormat` | `string` | Display format (`{0}` = count) |
| `titleFormat` | `string` | Tooltip format |

### ICountBadgeStyles

| Property | Description |
|----------|-------------|
| `badgeBackground` | Background color |
| `badgeForeground` | Text color |
| `badgeBorder` | Border color |

---

## HighlightedLabel

Text label with highlighted portions (for search matches).

```typescript
import { HighlightedLabel } from 'risotron/renderer';

const label = new HighlightedLabel(container, {
  hoverDelegate: getDefaultHoverDelegate()
});

// Simple text
label.set('Hello World');

// With highlights (match positions)
label.set('Hello World', [
  { start: 0, end: 5 },      // "Hello" highlighted
  { start: 6, end: 11 }      // "World" highlighted
]);

// With extra CSS classes for highlights
label.set('Important Message', [
  { start: 0, end: 9, extraClasses: 'warning-highlight' }
]);

// Get DOM element
const element = label.element;
```

### IHighlight

| Property | Type | Description |
|----------|------|-------------|
| `start` | `number` | Start index (inclusive) |
| `end` | `number` | End index (exclusive) |
| `extraClasses` | `string` | Additional CSS classes |

### Example: Search Highlighting

```typescript
function highlightMatches(text: string, query: string): IHighlight[] {
  const highlights: IHighlight[] = [];
  const lowerText = text.toLowerCase();
  const lowerQuery = query.toLowerCase();

  let index = 0;
  while ((index = lowerText.indexOf(lowerQuery, index)) !== -1) {
    highlights.push({
      start: index,
      end: index + query.length
    });
    index += query.length;
  }

  return highlights;
}

const label = new HighlightedLabel(container);
const matches = highlightMatches('TypeScript is great', 'script');
label.set('TypeScript is great', matches);
// "TypeScript" with "Script" portion highlighted
```

---

## IconLabel

Label with optional icon, suffix, and description.

```typescript
import { IconLabel, renderLabelWithIcons } from 'risotron/renderer';

const iconLabel = new IconLabel(container, {
  supportHighlights: true,
  supportDescriptionHighlights: true,
  supportIcons: true,
  hoverDelegate: getDefaultHoverDelegate()
});

// Simple label
iconLabel.setLabel('main.ts');

// Label with icon and options
iconLabel.setLabel('main.ts', {
  title: 'main.ts - TypeScript file',
  extraClasses: ['ts-icon'],
  matches: [{ start: 0, end: 4 }],  // Highlight "main"
  italic: false,
  bold: false,
  strikethrough: false
});

// With description
iconLabel.setLabel('Component', {
  description: 'React component',
  descriptionTitle: 'Component type: React',
  descriptionMatches: [{ start: 0, end: 5 }]  // Highlight "React"
});

// With suffix
iconLabel.setLabel('file.txt', {
  suffix: 'readonly',
  separator: ' - '
});

// Get DOM element
const element = iconLabel.element;
```

### IIconLabelValueOptions

| Option | Type | Description |
|--------|------|-------------|
| `title` | `string` | Tooltip for label |
| `matches` | `IHighlight[]` | Highlight positions |
| `extraClasses` | `string[]` | Additional CSS classes |
| `italic` | `boolean` | Italic text |
| `bold` | `boolean` | Bold text |
| `strikethrough` | `boolean` | Strikethrough text |
| `description` | `string` | Secondary description |
| `descriptionTitle` | `string` | Description tooltip |
| `descriptionMatches` | `IHighlight[]` | Description highlights |
| `suffix` | `string` | Suffix text |
| `separator` | `string` | Separator before suffix |
| `hideIcon` | `boolean` | Hide the icon |
| `iconPath` | `URI \| ThemeIcon` | Custom icon |

### SimpleIconLabel

Simpler version without highlight support:

```typescript
import { SimpleIconLabel } from 'risotron/renderer';

const simpleLabel = new SimpleIconLabel(container);
simpleLabel.setLabel('Simple Label');
```

### Render Icons in Text

```typescript
import { renderLabelWithIcons, renderIcon } from 'risotron/renderer';

// Render text with codicon references
const elements = renderLabelWithIcons('Save $(save) file');
// Returns: [TextNode('Save '), IconElement, TextNode(' file')]

container.append(...elements);

// Render single icon
const icon = renderIcon(Codicon.save);
container.appendChild(icon);
```

---

## KeybindingLabel

Display keyboard shortcuts with platform-appropriate labels.

```typescript
import {
  KeybindingLabel,
  createSimpleKeybinding,
  createChordKeybinding,
  unthemedKeybindingLabelOptions
} from 'risotron/renderer';

const label = new KeybindingLabel(container, {
  ...unthemedKeybindingLabelOptions
});

// Simple keybinding
const binding = createSimpleKeybinding('ctrl+s');
label.set(binding);

// Chord keybinding (Ctrl+K Ctrl+C)
const chord = createChordKeybinding('ctrl+k', 'ctrl+c');
label.set(chord);

// With highlights
label.set(binding, {
  firstPartMatch: { ctrlKey: true },  // Highlight Ctrl
  chordPartMatch: null
});

// Clear
label.set(null);
```

### ResolvedKeybinding

```typescript
interface ResolvedKeybinding {
  getLabel(): string | null;
  getAriaLabel(): string | null;
  getElectronAccelerator(): string | null;
  getUserSettingsLabel(): string | null;
  isWYSIWYG(): boolean;
  hasMultipleChords(): boolean;
  getChords(): ResolvedChord[];
}
```

### IKeybindingLabelStyles

| Property | Description |
|----------|-------------|
| `keybindingLabelBackground` | Key background |
| `keybindingLabelForeground` | Key text color |
| `keybindingLabelBorder` | Key border |
| `keybindingLabelBottomBorder` | 3D bottom border |
| `keybindingLabelShadow` | Key shadow |

---

## SeverityIcon

Icon indicating severity level (info, warning, error).

```typescript
import { SeverityIcon, Severity } from 'risotron/renderer';

// Create icon for warning
const icon = SeverityIcon.create(Severity.Warning);
container.appendChild(icon);

// Get CSS class
const cssClass = SeverityIcon.className(Severity.Error);
element.classList.add(cssClass);
```

### Severity Levels

| Value | Description |
|-------|-------------|
| `Severity.Info` | Information (blue) |
| `Severity.Warning` | Warning (yellow) |
| `Severity.Error` | Error (red) |
| `Severity.Ignore` | No icon |

---

## Best Practices

### 1. Debounce Progress Updates

```typescript
const progress = new ProgressBar(container);
let pending = 0;

function updateProgress() {
  pending++;
  if (pending === 1) {
    requestAnimationFrame(() => {
      progress.setWorked(currentProgress);
      pending = 0;
    });
  }
}
```

### 2. Delayed Progress Display

```typescript
// Don't show progress for quick operations
const progress = new ProgressBar(container);
progress.show(200);  // Only show if operation takes > 200ms

try {
  await quickOperation();
} finally {
  progress.hide();  // Cancels delayed show if not yet visible
}
```

### 3. Efficient Highlight Updates

```typescript
const label = new HighlightedLabel(container);

// Reuse highlight arrays when possible
const cachedHighlights = new Map<string, IHighlight[]>();

function setWithCachedHighlights(text: string, query: string) {
  const key = `${text}:${query}`;
  let highlights = cachedHighlights.get(key);

  if (!highlights) {
    highlights = computeHighlights(text, query);
    cachedHighlights.set(key, highlights);
  }

  label.set(text, highlights);
}
```

### 4. Cleanup

```typescript
class MyView extends Disposable {
  private progress: ProgressBar;
  private badge: CountBadge;
  private label: IconLabel;

  constructor(container: HTMLElement) {
    super();
    this.progress = this._register(new ProgressBar(container));
    this.badge = this._register(new CountBadge(container, {}, styles));
    this.label = this._register(new IconLabel(container, options));
  }
}
```
