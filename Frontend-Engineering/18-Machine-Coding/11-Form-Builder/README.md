# Form Builder

**Difficulty:** Hard | **Est. Time:** 60–90 min

---

## Problem Statement

Build a form builder application where users can drag and drop fields from a palette onto a preview canvas, configure each field's properties, preview the form, and export the form configuration as JSON.

---

## Requirements

### Functional
- [ ] Field palette with draggable field types (Text, Number, Email, Textarea, Select, Checkbox, Radio, Date, File)
- [ ] Drop zone / canvas where fields are placed and reordered
- [ ] Drag fields from palette to canvas (or click to add)
- [ ] Reorder fields on the canvas via drag
- [ ] Click a field on canvas to configure its properties
- [ ] Field configuration panel: label, placeholder, required, options (for select/radio), min/max, defaultValue
- [ ] Preview mode: renders the form with live validation
- [ ] Export form configuration as JSON
- [ ] Import form configuration from JSON

### Non-Functional
- [ ] Smooth drag animations (dnd-kit)
- [ ] Real-time preview updates when config changes
- [ ] Generated form should be accessible (labels, error messages)
- [ ] Undo/redo for form changes (optional)

---

## Component Architecture

```
App
├── FormBuilderHeader
│   ├── Title
│   ├── PreviewToggle (Build / Preview)
│   ├── UndoButton / RedoButton
│   ├── ImportButton
│   └── ExportButton
├── FormBuilderBody
│   ├── FieldPalette (sidebar, left)
│   │   └── DraggableFieldType (×N)
│   │       ├── TextFieldType
│   │       ├── NumberFieldType
│   │       ├── SelectFieldType
│   │       └── ... (one per type)
│   ├── DropZone (center, canvas)
│   │   └── FormField (×N) [droppable + sortable]
│   │       ├── FieldDisplay (label + input preview)
│   │       ├── DragHandle
│   │       └── DeleteButton
│   └── FieldConfigPanel (sidebar, right)
│       ├── LabelInput
│       ├── PlaceholderInput
│       ├── RequiredToggle
│       ├── OptionsEditor (for select/radio)
│       ├── ValidationRules (min, max, pattern)
│       └── DeleteFieldButton
└── FormPreview (modal or toggle)
    └── RenderedForm (live, with validation)
```

---

## Field Types Registry

```js
const FIELD_TYPES = {
  text: {
    label: 'Text Input',
    icon: '📝',
    defaultConfig: { label: 'Text Field', placeholder: 'Enter text...', required: false },
    validation: (value, config) => {
      if (config.required && !value) return 'This field is required';
      if (config.minLength && value.length < config.minLength) return `Min ${config.minLength} characters`;
      return null;
    }
  },
  number: {
    label: 'Number',
    icon: '🔢',
    defaultConfig: { label: 'Number', placeholder: '0', required: false, min: null, max: null },
    validation: (value, config) => {
      const num = parseFloat(value);
      if (config.required && isNaN(num)) return 'This field is required';
      if (config.min !== null && num < config.min) return `Minimum value is ${config.min}`;
      if (config.max !== null && num > config.max) return `Maximum value is ${config.max}`;
      return null;
    }
  },
  select: {
    label: 'Select',
    icon: '▼',
    defaultConfig: { label: 'Select', required: false, options: ['Option 1', 'Option 2'], multiple: false },
    validation: (value, config) => {
      if (config.required && !value) return 'Please select an option';
      return null;
    }
  },
  email: { /* similar */ },
  textarea: { /* similar */ },
  checkbox: { /* similar */ },
  radio: { /* similar */ },
  date: { /* similar */ },
  file: { /* similar */ },
};
```

---

## State Management

```js
const [fields, setFields] = useState([]);         // ordered list of field objects
const [selectedFieldId, setSelectedFieldId] = useState(null);
const [isPreviewMode, setIsPreviewMode] = useState(false);
const [formValues, setFormValues] = useState({});  // preview mode values
const [errors, setErrors] = useState({});           // preview mode errors

// History for undo/redo
const [history, setHistory] = useState([[]]);
const [historyIndex, setHistoryIndex] = useState(0);
```

### Field Object Shape

```js
{
  id: 'field_1',
  type: 'text',           // matches FIELD_TYPES key
  config: {
    label: 'Full Name',
    placeholder: 'Enter your name',
    required: true,
    defaultValue: '',
    minLength: 2,
    // type-specific config
  }
}
```

---

## Implementation Steps

1. Build the three-panel layout: Palette (left) | Canvas (center) | Config (right)
2. Create FieldPalette with draggable items for each field type
3. Set up dnd-kit: `DndContext` → `SortableContext` on canvas
4. Implement drop from palette: onDragEnd → if over canvas, add new field
5. Implement reorder on canvas: use dnd-kit Sortable (arrayMove)
6. Build FieldConfigPanel: when a field is selected, show its config form
7. Wire config changes back to field object in state (immutable update)
8. Implement Preview mode: render actual form inputs from fields array
9. Add form validation in preview mode (field type's validate function)
10. Implement Export: JSON.stringify(fields) → download/copy
11. Implement Import: file upload → JSON.parse → validate → setFields
12. Handle empty canvas state ("Drag fields here")
13. Add undo/redo for add, delete, reorder, config changes

---

## Code Snippets

### Drop from Palette to Canvas

```js
function handleDragEnd(event) {
  const { active, over } = event;
  if (!over) return;

  // Adding from palette
  if (active.data.current?.fromPalette) {
    const newField = createField(active.data.current.type);
    const insertIndex = over.data.current?.sortable?.index ?? fields.length;
    const newFields = [...fields];
    newFields.splice(insertIndex, 0, newField);
    setFields(newFields);
    setSelectedFieldId(newField.id);
    return;
  }

  // Reordering on canvas
  if (active.id !== over.id) {
    const oldIndex = fields.findIndex(f => f.id === active.id);
    const newIndex = fields.findIndex(f => f.id === over.id);
    setFields(arrayMove(fields, oldIndex, newIndex));
  }
}
```

### Field Config Update

```js
function updateFieldConfig(fieldId, configChanges) {
  setFields(prev => prev.map(f =>
    f.id === fieldId ? { ...f, config: { ...f.config, ...configChanges } } : f
  ));
}
```

### Form Validation in Preview

```js
function validateForm() {
  const newErrors = {};
  fields.forEach(field => {
    const value = formValues[field.id] ?? field.config.defaultValue ?? '';
    const validateFn = FIELD_TYPES[field.type].validation;
    if (validateFn) {
      const error = validateFn(value, field.config);
      if (error) newErrors[field.id] = error;
    }
  });
  setErrors(newErrors);
  return Object.keys(newErrors).length === 0;
}
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Empty canvas | Show placeholder "Drag fields here" centered in canvas |
| No field selected | Show "Select a field to configure" in config panel |
| Delete selected field | If deleted field was selected, clear selection |
| Duplicate field labels | Append ` (1)`, ` (2)` suffix automatically |
| Import invalid JSON | Show parse error message; do not overwrite current form |
| Very long field list | Make canvas scrollable; virtualize if > 50 fields |
| File field in preview | Handle file input change, show filename after selection |

---

## Bonus Features

- [ ] **Field groups / sections** (collapsible containers)
- [ ] **Conditional logic** (show/hide fields based on other field values)
- [ ] **Custom CSS** per field (inline style config)
- [ ] **Drag field from canvas back to palette** (to remove)
- [ ] **Multi-page forms** (page break field, pagination in preview)
- [ ] **Theme editor** (colors, fonts, spacing)
- [ ] **Form submission simulation** (alert or console.log values)

---

## Common Interview Questions

1. **How do you handle the drop-from-palette vs reorder-on-canvas distinction?** — Use `active.data.current.fromPalette` flag. Palette items have it set to true; canvas items don't. In `onDragEnd`, check this flag to decide between create vs reorder.

2. **How would you implement field validation?** — Each field type has a `validation` function that takes (value, config) and returns an error string or null. The preview mode iterates all fields and collects errors on submit.

3. **How do you export the form configuration?** — Serialize the fields array (including all config) to JSON. Strip runtime-only properties (like validation functions). The JSON can be imported later to reconstruct the exact form.

4. **How to handle complex nested configurations?** — Use a deeply nested `config` object per field type. The config panel renders different inputs based on field type. Update using immutable helpers (e.g., Immer or spread at each level).

---

## Resources

- [dnd-kit Sortable](https://docs.dndkit.com/presets/sortable)
- [JSON Schema](https://json-schema.org/) (for form config validation)
