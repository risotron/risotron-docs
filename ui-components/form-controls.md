# Form Controls

Form controls for user input: buttons, toggles, text inputs, select boxes, and radio groups.

---

## Button

Clickable button with optional icon and secondary style.

```typescript
import { Button, unthemedButtonStyles } from 'risotron/renderer';

const button = new Button(container, {
  ...unthemedButtonStyles,
  secondary: false,
  supportIcons: true
});

button.label = 'Click Me';
button.icon = Codicon.add;
button.enabled = true;

button.onDidClick(() => {
  console.log('Button clicked!');
});

// Cleanup
button.dispose();
```

### IButtonOptions

| Option | Type | Description |
|--------|------|-------------|
| `secondary` | `boolean` | Use secondary (less prominent) style |
| `supportIcons` | `boolean` | Enable icon support in label |
| `supportShortLabel` | `boolean` | Enable short label for narrow widths |
| `title` | `string` | Tooltip text |
| `ariaLabel` | `string` | Accessibility label |
| `disabled` | `boolean` | Initial disabled state |
| `hoverDelegate` | `IHoverDelegate` | Custom hover behavior |

### IButtonStyles

| Property | Description |
|----------|-------------|
| `buttonBackground` | Background color |
| `buttonForeground` | Text color |
| `buttonHoverBackground` | Background on hover |
| `buttonBorder` | Border color |
| `buttonSecondaryBackground` | Secondary button background |
| `buttonSecondaryForeground` | Secondary button text |
| `buttonSecondaryHoverBackground` | Secondary hover background |
| `buttonSeparator` | Separator color (for dropdown buttons) |

### Button Variants

```typescript
// ButtonBar - horizontal group of buttons
const bar = new ButtonBar(container, { orientation: 'horizontal' });
bar.addButton({ label: 'OK', ...unthemedButtonStyles });
bar.addButton({ label: 'Cancel', ...unthemedButtonStyles, secondary: true });

// ButtonWithDropdown - button with dropdown menu
const dropdown = new ButtonWithDropdown(container, {
  ...unthemedButtonStyles,
  actions: [action1, action2],
  contextMenuProvider
});
```

---

## Toggle

On/off toggle switch, typically used in toolbars.

```typescript
import { Toggle, unthemedToggleStyles } from 'risotron/renderer';

const toggle = new Toggle({
  title: 'Enable Feature',
  icon: Codicon.eye,
  isChecked: false,
  ...unthemedToggleStyles
});

container.appendChild(toggle.domNode);

toggle.onChange(checked => {
  console.log('Toggle:', checked);
});

// Programmatic control
toggle.checked = true;
toggle.enable();
toggle.disable();
```

### IToggleOpts

| Option | Type | Description |
|--------|------|-------------|
| `title` | `string` | Tooltip/label |
| `icon` | `ThemeIcon` | Icon to display |
| `isChecked` | `boolean` | Initial state |
| `notFocusable` | `boolean` | Remove from tab order |
| `actionClassName` | `string` | Additional CSS class |

### IToggleStyles

| Property | Description |
|----------|-------------|
| `inputActiveOptionBackground` | Background when active |
| `inputActiveOptionForeground` | Text color when active |
| `inputActiveOptionBorder` | Border when active |

---

## Checkbox

Standard checkbox with tri-state support.

```typescript
import { Checkbox, unthemedCheckboxStyles } from 'risotron/renderer';

const checkbox = new Checkbox('Enable notifications', false, unthemedCheckboxStyles);
container.appendChild(checkbox.domNode);

checkbox.onChange(checked => {
  console.log('Checked:', checked);
});

// Get/set state
checkbox.checked = true;
const isChecked = checkbox.checked;
```

### TriStateCheckbox

For indeterminate state (e.g., "select all" with partial selection):

```typescript
import { TriStateCheckbox } from 'risotron/renderer';

const tri = new TriStateCheckbox('Select All', 'indeterminate', styles);
// States: true, false, 'indeterminate'
```

---

## InputBox

Text input with validation and message display.

```typescript
import { InputBox, MessageType, unthemedInputBoxStyles } from 'risotron/renderer';

const input = new InputBox(container, contextViewProvider, {
  placeholder: 'Enter your name',
  ariaLabel: 'Name input',
  validationOptions: {
    validation: (value) => {
      if (!value) {
        return { type: MessageType.ERROR, content: 'Name is required' };
      }
      if (value.length < 3) {
        return { type: MessageType.WARNING, content: 'Name is too short' };
      }
      return null; // Valid
    }
  },
  inputBoxStyles: unthemedInputBoxStyles
});

// Events
input.onDidChange(value => {
  console.log('Value:', value);
});

// Methods
input.value = 'Initial value';
input.focus();
input.select(); // Select all text
input.validate(); // Trigger validation

// Show message manually
input.showMessage({ type: MessageType.INFO, content: 'Hint: Enter full name' });
input.hideMessage();
```

### IInputOptions

| Option | Type | Description |
|--------|------|-------------|
| `placeholder` | `string` | Placeholder text |
| `ariaLabel` | `string` | Accessibility label |
| `tooltip` | `string` | Tooltip text |
| `type` | `string` | Input type (text, password, number) |
| `flexibleHeight` | `boolean` | Auto-expand height |
| `flexibleMaxHeight` | `number` | Max height when flexible |
| `flexibleWidth` | `boolean` | Auto-expand width |
| `validationOptions` | `IInputValidationOptions` | Validation config |
| `actions` | `IAction[]` | Action buttons in input |
| `history` | `string[]` | History for up/down navigation |

### MessageType

| Type | Usage |
|------|-------|
| `MessageType.INFO` | Informational hint |
| `MessageType.WARNING` | Warning (yellow) |
| `MessageType.ERROR` | Error (red) |

### HistoryInputBox

Input with history navigation (up/down arrows):

```typescript
import { HistoryInputBox } from 'risotron/renderer';

const historyInput = new HistoryInputBox(container, contextViewProvider, {
  history: ['previous', 'search', 'terms'],
  ...options
});

historyInput.addToHistory('new term');
```

---

## SelectBox

Dropdown select with native or custom rendering.

```typescript
import { SelectBox, unthemedSelectBoxStyles } from 'risotron/renderer';

const options = [
  { text: 'Option 1' },
  { text: 'Option 2', description: 'With description' },
  { text: 'Option 3', isDisabled: true }
];

const select = new SelectBox(options, 0, contextViewProvider, unthemedSelectBoxStyles, {
  ariaLabel: 'Choose option'
});

select.render(container);

select.onDidSelect(e => {
  console.log('Selected:', e.selected, 'Index:', e.index);
});

// Programmatic control
select.select(1); // Select by index
select.setOptions(newOptions); // Update options
select.setEnabled(false);
```

### ISelectOptionItem

| Property | Type | Description |
|----------|------|-------------|
| `text` | `string` | Display text |
| `description` | `string` | Secondary text |
| `detail` | `string` | Additional detail |
| `decoratorRight` | `string` | Right-side decorator |
| `isDisabled` | `boolean` | Disable this option |

### Separator

```typescript
import { SeparatorSelectOption } from 'risotron/renderer';

const options = [
  { text: 'Recent' },
  SeparatorSelectOption, // Visual separator
  { text: 'All Files' }
];
```

---

## Radio

Radio button group for mutually exclusive options.

```typescript
import { Radio } from 'risotron/renderer';

const radio = new Radio({
  items: [
    { text: 'Small', isActive: true },
    { text: 'Medium' },
    { text: 'Large' },
    { text: 'Disabled', disabled: true }
  ]
});

container.appendChild(radio.domNode);

radio.onDidSelect(item => {
  console.log('Selected:', item.text);
});

// Programmatic control
radio.setActiveItem(1); // By index
radio.setEnabled(false); // Disable all
radio.setItems(newItems); // Replace items
```

### IRadioOptionItem

| Property | Type | Description |
|----------|------|-------------|
| `text` | `string` | Display text |
| `tooltip` | `string` | Tooltip text |
| `isActive` | `boolean` | Initially selected |
| `disabled` | `boolean` | Disable this option |

### IRadioStyles

| Property | Description |
|----------|-------------|
| `activeForeground` | Text color when selected |
| `activeBackground` | Background when selected |
| `activeBorder` | Border when selected |
| `inactiveForeground` | Text color when not selected |
| `inactiveBackground` | Background when not selected |
| `inactiveBorder` | Border when not selected |
| `inactiveHoverBackground` | Background on hover (inactive) |

---

## FindInput

Specialized input for search with toggles (case sensitive, whole word, regex).

```typescript
import {
  FindInput,
  CaseSensitiveToggle,
  WholeWordsToggle,
  RegexToggle
} from 'risotron/renderer';

const findInput = new FindInput(container, contextViewProvider, {
  appendCaseSensitiveLabel: 'Case Sensitive (Alt+C)',
  appendWholeWordsLabel: 'Whole Word (Alt+W)',
  appendRegexLabel: 'Regex (Alt+R)',
  ...styles
});

// Events
findInput.onDidChange(value => console.log('Search:', value));
findInput.onCaseSensitiveKeyDown(e => { /* toggle case sensitive */ });
```

### ReplaceInput

Extended FindInput with replace functionality:

```typescript
import { ReplaceInput } from 'risotron/renderer';

const replaceInput = new ReplaceInput(container, contextViewProvider, {
  label: 'Replace',
  ...styles
});
```

---

## Best Practices

### 1. Always Dispose

```typescript
class MyView extends Disposable {
  private button: Button;

  constructor(container: HTMLElement) {
    super();
    this.button = this._register(new Button(container, options));
  }
}
```

### 2. Use ContextViewProvider for Overlays

InputBox and SelectBox need a context view provider for messages/dropdowns:

```typescript
import { ContextViewService } from 'risotron/renderer';

const contextViewService = new ContextViewService(document.body);
const input = new InputBox(container, contextViewService, options);
```

### 3. Consistent Styling

Use themed styles from your theme service or unthemed defaults:

```typescript
// From theme service
const styles = {
  buttonBackground: themeService.getColor('button.background'),
  // ...
};

// Or unthemed for prototyping
import { unthemedButtonStyles } from 'risotron/renderer';
```
