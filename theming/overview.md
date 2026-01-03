# Theming Overview

Risotron includes a comprehensive theming system ported from VSCode, supporting color themes, icon themes, and dynamic CSS variable injection.

---

## Theme Service

The `IThemeService` provides access to current themes and theme change events.

```typescript
import { IThemeService, StandaloneThemeService, SimpleColorTheme } from 'risotron/renderer';

// Create standalone theme service
const themeService = new StandaloneThemeService();

// Get current color theme
const colorTheme = themeService.getColorTheme();

// Get a color from the theme
const foreground = colorTheme.getColor('foreground');
if (foreground) {
  element.style.color = foreground.toString();
}

// Check if color is defined
if (colorTheme.defines('button.background')) {
  // Color is explicitly set
}

// Listen for theme changes
themeService.onDidColorThemeChange(theme => {
  console.log('Theme changed:', theme.label);
  updateStyles(theme);
});
```

### Theme Types

| Type | Description |
|------|-------------|
| `'light'` | Light theme (bright background) |
| `'dark'` | Dark theme (dark background) |
| `'hcLight'` | High contrast light |
| `'hcDark'` | High contrast dark |

### SimpleColorTheme

Create and customize a color theme:

```typescript
import { SimpleColorTheme, Color } from 'risotron/renderer';

// Create a dark theme
const myTheme = new SimpleColorTheme('My Dark Theme', 'dark');

// Override specific colors
myTheme.setColor('foreground', Color.fromHex('#CCCCCC'));
myTheme.setColor('editor.background', Color.fromHex('#1E1E1E'));
myTheme.setColor('button.background', Color.fromHex('#0E639C'));

// Apply to service
themeService.setColorTheme(myTheme);

// Remove a color override (falls back to default)
myTheme.removeColor('button.background');
```

---

## Using Colors in Components

### Themable Base Class

Extend `Themable` for automatic theme updates:

```typescript
import { Themable, IThemeService } from 'risotron/renderer';

class MyComponent extends Themable {
  constructor(
    @IThemeService themeService: IThemeService
  ) {
    super(themeService);
    this.updateStyles();
  }

  protected updateStyles(): void {
    const foreground = this.getColor('foreground');
    const background = this.getColor('editor.background');

    this.element.style.color = foreground?.toString() ?? '';
    this.element.style.backgroundColor = background?.toString() ?? '';
  }
}
```

### Direct Color Access

```typescript
import { IColorTheme } from 'risotron/renderer';

function applyTheme(theme: IColorTheme) {
  const button = theme.getColor('button.background');
  const buttonHover = theme.getColor('button.hoverBackground');
  const text = theme.getColor('button.foreground');

  return {
    buttonBackground: button?.toString(),
    buttonHoverBackground: buttonHover?.toString(),
    buttonForeground: text?.toString()
  };
}
```

---

## CSS Variables

### Automatic Injection

Inject all registered colors as CSS variables:

```typescript
import { injectColorCssVariables, getColorRegistry } from 'risotron/renderer';

// Inject variables into document
injectColorCssVariables(themeService.getColorTheme(), document.documentElement);

// Variables are named: --vscode-{colorId}
// e.g., --vscode-foreground, --vscode-button-background
```

### Using CSS Variables

```css
.my-component {
  color: var(--vscode-foreground);
  background: var(--vscode-editor-background);
  border: 1px solid var(--vscode-focusBorder);
}

.my-button {
  background: var(--vscode-button-background);
  color: var(--vscode-button-foreground);
}

.my-button:hover {
  background: var(--vscode-button-hoverBackground);
}
```

### Get Variable Name

```typescript
import { asCssVariableName, asCssVariable, asCssVariableWithDefault } from 'risotron/renderer';

// Get variable name
const varName = asCssVariableName('foreground');
// Result: '--vscode-foreground'

// Get var() reference
const cssVar = asCssVariable('foreground');
// Result: 'var(--vscode-foreground)'

// With fallback
const cssVarWithDefault = asCssVariableWithDefault('foreground', '#CCCCCC');
// Result: 'var(--vscode-foreground, #CCCCCC)'
```

---

## Theming Participants

Register callbacks that run when theme changes:

```typescript
import { registerThemingParticipant, getThemingRegistry } from 'risotron/renderer';

// Register participant
const disposable = registerThemingParticipant((theme, collector) => {
  const background = theme.getColor('editor.background');
  if (background) {
    collector.addRule(`.my-editor { background: ${background}; }`);
  }

  const border = theme.getColor('focusBorder');
  if (border) {
    collector.addRule(`.my-editor:focus { border-color: ${border}; }`);
  }
});

// Cleanup
disposable.dispose();
```

### ICssStyleCollector

```typescript
interface ICssStyleCollector {
  addRule(rule: string): void;
}
```

---

## File Icon Theme

```typescript
const fileIconTheme = themeService.getFileIconTheme();

// Check capabilities
if (fileIconTheme.hasFileIcons) {
  // Theme provides file icons
}

if (fileIconTheme.hasFolderIcons) {
  // Theme provides folder icons
}

// Listen for changes
themeService.onDidFileIconThemeChange(theme => {
  refreshFileIcons();
});
```

---

## Product Icon Theme

For workbench icons (activity bar, status bar, etc.):

```typescript
const productIconTheme = themeService.getProductIconTheme();

// Get an icon
const icon = productIconTheme.getIcon('debug-start');
if (icon) {
  element.classList.add(icon.className);
}

// Listen for changes
themeService.onDidProductIconThemeChange(theme => {
  refreshProductIcons();
});
```

---

## Theme Icon References

Use theme icons in your components:

```typescript
import { themeColorFromId, FileThemeIcon, FolderThemeIcon } from 'risotron/renderer';

// Reference a color by ID
const colorRef = themeColorFromId('button.background');

// Standard file/folder icons
const fileIcon = FileThemeIcon;
const folderIcon = FolderThemeIcon;
```

---

## Best Practices

### 1. Use CSS Variables When Possible

```css
/* Preferred: Uses CSS variables */
.component {
  color: var(--vscode-foreground);
  background: var(--vscode-editor-background);
}
```

```typescript
// Avoid: Manual color application
// Only use when CSS variables aren't sufficient
element.style.color = theme.getColor('foreground')?.toString();
```

### 2. Handle Missing Colors

```typescript
const color = theme.getColor('myCustomColor');
if (color) {
  element.style.background = color.toString();
} else {
  element.style.background = '#default';
}
```

### 3. Register for Theme Changes

```typescript
class ThemedComponent extends Disposable {
  constructor(themeService: IThemeService) {
    super();

    // Update on theme change
    this._register(themeService.onDidColorThemeChange(() => {
      this.updateColors();
    }));

    // Initial update
    this.updateColors();
  }
}
```

### 4. Use Unthemed Styles for Development

```typescript
import { unthemedButtonStyles, unthemedInputBoxStyles } from 'risotron/renderer';

// Quick prototyping without full theme setup
const button = new Button(container, unthemedButtonStyles);
```

---

## Next Steps

- [Color Registry](./color-registry.md) - Registering and using colors
- [Built-in Colors](./built-in-colors.md) - Available color definitions
