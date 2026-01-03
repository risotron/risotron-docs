# Color Registry

The color registry manages all theme colors with their default values across different theme types.

---

## Registering Colors

Define new colors with defaults for each theme type:

```typescript
import { registerColor, transparent, darken, lighten } from 'risotron/renderer';

// Simple color with hex defaults
export const myComponentBackground = registerColor(
  'myComponent.background',
  {
    dark: '#252526',
    light: '#F3F3F3',
    hcDark: '#000000',
    hcLight: '#FFFFFF'
  },
  'Background color for my component.'
);

// Color referencing another color
export const myComponentBorder = registerColor(
  'myComponent.border',
  {
    dark: transparent(myComponentBackground, 0.5),
    light: darken(myComponentBackground, 0.1),
    hcDark: '#6FC3DF',
    hcLight: '#0F4A85'
  },
  'Border color for my component.'
);

// Null means "not defined" (transparent/hidden)
export const optionalBorder = registerColor(
  'myComponent.optionalBorder',
  {
    dark: null,
    light: null,
    hcDark: '#FFFFFF',  // Only visible in high contrast
    hcLight: '#000000'
  },
  'Optional border, only visible in high contrast themes.'
);
```

### ColorDefaults

| Property | Description |
|----------|-------------|
| `dark` | Default for dark themes |
| `light` | Default for light themes |
| `hcDark` | Default for high contrast dark |
| `hcLight` | Default for high contrast light |

---

## Color Transforms

Transform existing colors programmatically:

### transparent

Make a color semi-transparent:

```typescript
import { transparent, registerColor } from 'risotron/renderer';

const baseColor = registerColor('base', { dark: '#FF0000', ... });

// 50% transparent
const halfTransparent = registerColor('base.half',
  { dark: transparent(baseColor, 0.5), ... }
);
```

### darken / lighten

Adjust color brightness:

```typescript
import { darken, lighten, registerColor } from 'risotron/renderer';

const primary = registerColor('primary', { dark: '#007ACC', ... });

// 20% darker
const primaryDark = registerColor('primary.dark',
  { dark: darken(primary, 0.2), ... }
);

// 20% lighter
const primaryLight = registerColor('primary.light',
  { dark: lighten(primary, 0.2), ... }
);
```

### opaque

Remove transparency by blending with background:

```typescript
import { opaque, registerColor } from 'risotron/renderer';

const transparentColor = registerColor('overlay', { dark: '#FF000080', ... });
const background = registerColor('background', { dark: '#1E1E1E', ... });

// Blend to remove transparency
const solidColor = registerColor('overlay.solid',
  { dark: opaque(transparentColor, background), ... }
);
```

### lessProminent

Make a color less prominent:

```typescript
import { lessProminent, registerColor } from 'risotron/renderer';

const prominent = registerColor('prominent', { dark: '#FF0000', ... });
const background = registerColor('bg', { dark: '#000000', ... });

// Less prominent version
const subtle = registerColor('subtle',
  { dark: lessProminent(prominent, background, 0.7, 0.4), ... }
);
```

### oneOf

Use first defined color from a list:

```typescript
import { oneOf, registerColor } from 'risotron/renderer';

const preferred = registerColor('preferred', { dark: null, ... });
const fallback1 = registerColor('fallback1', { dark: null, ... });
const fallback2 = registerColor('fallback2', { dark: '#CCCCCC', ... });

// Uses first non-null color
const resolved = registerColor('resolved',
  { dark: oneOf(preferred, fallback1, fallback2), ... }
);
```

### ifDefinedThenElse

Conditional color based on another:

```typescript
import { ifDefinedThenElse, registerColor } from 'risotron/renderer';

const customBorder = registerColor('custom.border', { dark: null, ... });

const effectiveBorder = registerColor('effective.border',
  {
    dark: ifDefinedThenElse(
      customBorder,  // if this is defined
      customBorder,  // use it
      '#CCCCCC'      // else use fallback
    ),
    ...
  }
);
```

---

## Accessing the Registry

```typescript
import { getColorRegistry } from 'risotron/renderer';

const registry = getColorRegistry();

// Get all registered colors
const allColors = registry.getColors();
for (const color of allColors) {
  console.log(color.id, color.description);
}

// Get specific color definition
const colorDef = registry.getColorById('button.background');
if (colorDef) {
  console.log('Defaults:', colorDef.defaults);
  console.log('Description:', colorDef.description);
}

// Resolve default for a theme type
const defaultValue = registry.resolveDefaultColor('button.background', 'dark');

// Listen for schema changes
registry.onDidChangeSchema(() => {
  console.log('Color schema updated');
});
```

### ColorContribution

Structure of a registered color:

```typescript
interface ColorContribution {
  id: string;                    // Color identifier
  description: string;           // Documentation
  defaults: ColorDefaults;       // Theme defaults
  needsTransparency?: boolean;   // Requires alpha support
  deprecationMessage?: string;   // If deprecated
}
```

---

## Deregistering Colors

Remove dynamically registered colors:

```typescript
const registry = getColorRegistry();

// Register
registry.registerColor('my.temporary.color', defaults, description);

// Later, deregister
registry.deregisterColor('my.temporary.color');
```

---

## Resolving Colors

Resolve color values for a specific theme:

```typescript
import { resolveColorValue, IColorTheme } from 'risotron/renderer';

function getEffectiveColor(theme: IColorTheme, colorId: string): Color | undefined {
  // First check theme override
  const themeColor = theme.getColor(colorId);
  if (themeColor) {
    return themeColor;
  }

  // Fall back to registry default
  const registry = getColorRegistry();
  const defaultColor = registry.resolveDefaultColor(colorId, theme.type);
  if (defaultColor) {
    return resolveColorValue(defaultColor, theme);
  }

  return undefined;
}
```

---

## CSS Variable Generation

Generate CSS variables for all colors:

```typescript
import { generateColorCssVariables, injectColorCssVariables } from 'risotron/renderer';

// Generate CSS variable declarations
const cssText = generateColorCssVariables(theme);
// Result: "--vscode-foreground: #CCCCCC; --vscode-button-background: #0E639C; ..."

// Inject into element
injectColorCssVariables(theme, document.documentElement);
```

---

## Best Practices

### 1. Use Meaningful IDs

```typescript
// Good: descriptive, hierarchical
registerColor('sidebar.background', ...);
registerColor('sidebar.foreground', ...);
registerColor('sidebar.border', ...);

// Bad: unclear or flat
registerColor('color1', ...);
registerColor('myBg', ...);
```

### 2. Reference Existing Colors

```typescript
import { foreground, focusBorder } from 'risotron/platform/theme/common/colors';

// Good: reference base colors for consistency
registerColor('myComponent.foreground',
  { dark: foreground, light: foreground, ... }
);

// Good: derive from existing
registerColor('myComponent.border',
  { dark: transparent(focusBorder, 0.5), ... }
);
```

### 3. Provide Good Descriptions

```typescript
registerColor(
  'myComponent.activeBackground',
  defaults,
  'Background color when the component is active or focused. ' +
  'Used to indicate the currently selected item.'
);
```

### 4. Handle High Contrast

```typescript
registerColor('myComponent.border', {
  dark: null,           // No border in dark
  light: null,          // No border in light
  hcDark: '#FFFFFF',    // Visible border in HC dark
  hcLight: '#000000'    // Visible border in HC light
});
```

### 5. Group Related Colors

```typescript
// file: myComponentColors.ts

export const myComponentBackground = registerColor('myComponent.background', ...);
export const myComponentForeground = registerColor('myComponent.foreground', ...);
export const myComponentBorder = registerColor('myComponent.border', ...);
export const myComponentHoverBackground = registerColor('myComponent.hoverBackground', ...);
export const myComponentActiveBackground = registerColor('myComponent.activeBackground', ...);
```
