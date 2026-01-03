# Built-in Colors

Risotron includes a comprehensive set of pre-registered colors ported from VSCode. These colors are organized by category.

---

## Color Categories

| Category | Import Path | Description |
|----------|-------------|-------------|
| Base Colors | `colors/baseColors` | Foreground, background, borders |
| Input Colors | `colors/inputColors` | Input boxes, dropdowns |
| List Colors | `colors/listColors` | Lists, trees, tables |
| Menu Colors | `colors/menuColors` | Menus, context menus |
| Editor Colors | `colors/editorColors` | Editor and text areas |
| Charts Colors | `colors/chartsColors` | Chart visualizations |
| Minimap Colors | `colors/minimapColors` | Minimap component |
| Quickpick Colors | `colors/quickpickColors` | Quick pick dialogs |
| Search Colors | `colors/searchColors` | Search highlighting |
| Misc Colors | `colors/miscColors` | Miscellaneous |

---

## Base Colors

Core application colors used across components.

```typescript
import {
  foreground,
  disabledForeground,
  errorForeground,
  descriptionForeground,
  iconForeground,
  focusBorder,
  contrastBorder,
  activeContrastBorder,
  selectionBackground,
  textLinkForeground,
  textLinkActiveForeground,
  textSeparatorForeground,
  textPreformatForeground,
  textPreformatBackground,
  textBlockQuoteBackground,
  textBlockQuoteBorder,
  textCodeBlockBackground
} from 'risotron/platform/theme/common/colors';
```

| Color ID | Description |
|----------|-------------|
| `foreground` | Default text color |
| `disabledForeground` | Disabled element text |
| `errorForeground` | Error message text |
| `descriptionForeground` | Secondary/description text |
| `icon.foreground` | Default icon color |
| `focusBorder` | Focus indicator border |
| `contrastBorder` | High contrast border |
| `contrastActiveBorder` | Active high contrast border |
| `selection.background` | Text selection background |
| `textLink.foreground` | Link text color |
| `textLink.activeForeground` | Active link color |

---

## Input Colors

Form input styling.

```typescript
import {
  inputBackground,
  inputForeground,
  inputBorder,
  inputActiveOptionBackground,
  inputActiveOptionBorder,
  inputActiveOptionForeground,
  inputPlaceholderForeground,
  inputValidationInfoBackground,
  inputValidationInfoForeground,
  inputValidationInfoBorder,
  inputValidationWarningBackground,
  inputValidationWarningForeground,
  inputValidationWarningBorder,
  inputValidationErrorBackground,
  inputValidationErrorForeground,
  inputValidationErrorBorder
} from 'risotron/platform/theme/common/colors';
```

| Color ID | Description |
|----------|-------------|
| `input.background` | Input background |
| `input.foreground` | Input text |
| `input.border` | Input border |
| `input.placeholderForeground` | Placeholder text |
| `inputOption.activeBackground` | Active toggle background |
| `inputValidation.infoBackground` | Info message background |
| `inputValidation.warningBackground` | Warning message background |
| `inputValidation.errorBackground` | Error message background |

---

## List Colors

List, tree, and table styling.

```typescript
import {
  listFocusBackground,
  listFocusForeground,
  listFocusOutline,
  listActiveSelectionBackground,
  listActiveSelectionForeground,
  listActiveSelectionIconForeground,
  listInactiveSelectionBackground,
  listInactiveSelectionForeground,
  listInactiveFocusBackground,
  listInactiveFocusOutline,
  listHoverBackground,
  listHoverForeground,
  listDropOverBackground,
  listDropBetweenBackground,
  treeIndentGuidesStroke,
  treeInactiveIndentGuidesStroke,
  tableColumnsBorder,
  tableOddRowsBackgroundColor
} from 'risotron/platform/theme/common/colors';
```

| Color ID | Description |
|----------|-------------|
| `list.focusBackground` | Focused item background |
| `list.focusForeground` | Focused item text |
| `list.focusOutline` | Focused item outline |
| `list.activeSelectionBackground` | Selected item (focused list) |
| `list.inactiveSelectionBackground` | Selected item (unfocused list) |
| `list.hoverBackground` | Hover background |
| `list.dropOverBackground` | Drag-over background |
| `list.dropBetweenBackground` | Drag-between indicator |
| `tree.indentGuidesStroke` | Tree indent line color |
| `tree.tableColumnsBorder` | Table column border |
| `tree.tableOddRowsBackgroundColor` | Alternating row background |

---

## Menu Colors

Menu and dropdown styling.

```typescript
import {
  menuBackground,
  menuForeground,
  menuSelectionBackground,
  menuSelectionForeground,
  menuSelectionBorder,
  menuSeparatorBackground,
  menuBorder
} from 'risotron/platform/theme/common/colors';
```

| Color ID | Description |
|----------|-------------|
| `menu.background` | Menu background |
| `menu.foreground` | Menu text |
| `menu.selectionBackground` | Selected item background |
| `menu.selectionForeground` | Selected item text |
| `menu.separatorBackground` | Separator line |
| `menu.border` | Menu border |

---

## Button Colors

Button component styling.

| Color ID | Description |
|----------|-------------|
| `button.background` | Primary button background |
| `button.foreground` | Primary button text |
| `button.hoverBackground` | Primary button hover |
| `button.border` | Button border |
| `button.secondaryBackground` | Secondary button background |
| `button.secondaryForeground` | Secondary button text |
| `button.secondaryHoverBackground` | Secondary button hover |

---

## Editor Colors

Text editor and code display.

```typescript
import {
  editorBackground,
  editorForeground,
  editorLineHighlightBackground,
  editorLineHighlightBorder,
  editorSelectionBackground,
  editorInactiveSelection,
  editorSelectionHighlightBackground,
  editorFindMatchBackground,
  editorFindMatchHighlightBackground,
  editorWordHighlightBackground,
  editorWordHighlightStrongBackground,
  editorCursorForeground,
  editorCursorBackground,
  editorWhitespaces,
  editorIndentGuides,
  editorActiveIndentGuides,
  editorLineNumbers,
  editorActiveLineNumber,
  editorRuler
} from 'risotron/platform/theme/common/colors';
```

| Color ID | Description |
|----------|-------------|
| `editor.background` | Editor background |
| `editor.foreground` | Editor text |
| `editor.lineHighlightBackground` | Current line highlight |
| `editor.selectionBackground` | Text selection |
| `editor.findMatchBackground` | Search match |
| `editor.findMatchHighlightBackground` | Other search matches |
| `editorCursor.foreground` | Cursor color |
| `editorLineNumber.foreground` | Line number color |
| `editorLineNumber.activeForeground` | Active line number |

---

## Scrollbar Colors

Scrollbar styling.

| Color ID | Description |
|----------|-------------|
| `scrollbar.shadow` | Scrollbar shadow |
| `scrollbarSlider.background` | Slider background |
| `scrollbarSlider.hoverBackground` | Slider hover |
| `scrollbarSlider.activeBackground` | Slider active/dragging |

---

## Widget Colors

General widget styling.

| Color ID | Description |
|----------|-------------|
| `widget.shadow` | Widget drop shadow |
| `widget.border` | Widget border |

---

## Badge Colors

Badge/count styling.

| Color ID | Description |
|----------|-------------|
| `badge.background` | Badge background |
| `badge.foreground` | Badge text |

---

## Progress Bar Colors

Progress indicator styling.

| Color ID | Description |
|----------|-------------|
| `progressBar.background` | Progress bar fill |

---

## Using Colors

### In Components

```typescript
import { unthemedListStyles } from 'risotron/renderer';
import { listActiveSelectionBackground, listActiveSelectionForeground } from 'risotron/platform/theme/common/colors';

// Use with theme service
const styles = {
  listActiveSelectionBackground: theme.getColor(listActiveSelectionBackground.id),
  listActiveSelectionForeground: theme.getColor(listActiveSelectionForeground.id)
};
```

### In CSS

```css
.my-list-item.selected {
  background: var(--vscode-list-activeSelectionBackground);
  color: var(--vscode-list-activeSelectionForeground);
}

.my-input {
  background: var(--vscode-input-background);
  border: 1px solid var(--vscode-input-border);
  color: var(--vscode-input-foreground);
}

.my-input::placeholder {
  color: var(--vscode-input-placeholderForeground);
}
```

---

## CSS Variable Naming

Colors are available as CSS variables with the pattern:

```
--vscode-{colorId with . replaced by -}
```

Examples:
- `foreground` → `--vscode-foreground`
- `button.background` → `--vscode-button-background`
- `list.activeSelectionBackground` → `--vscode-list-activeSelectionBackground`
- `editor.findMatchBackground` → `--vscode-editor-findMatchBackground`

---

## Extending Colors

Register your own colors that integrate with the theming system:

```typescript
import { registerColor, foreground, focusBorder } from 'risotron/platform/theme/common/colors';

// Your custom colors
export const myWidgetBackground = registerColor(
  'myWidget.background',
  { dark: '#2D2D2D', light: '#FFFFFF', hcDark: '#000000', hcLight: '#FFFFFF' },
  'Background color for my custom widget.'
);

export const myWidgetForeground = registerColor(
  'myWidget.foreground',
  { dark: foreground, light: foreground, hcDark: foreground, hcLight: foreground },
  'Foreground color for my custom widget.'
);

export const myWidgetBorder = registerColor(
  'myWidget.border',
  { dark: null, light: null, hcDark: focusBorder, hcLight: focusBorder },
  'Border color for my custom widget (high contrast only).'
);
```
