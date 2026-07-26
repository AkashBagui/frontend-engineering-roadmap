# Forms

Forms are a critical part of most web applications. React offers two approaches for handling form inputs: **controlled** and **uncontrolled** components.

## Controlled Components

In controlled components, the form data is handled by React state. The input's `value` is set by state and changes are handled by `onChange`.

```jsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log({ email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={e => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="password"
        value={password}
        onChange={e => setPassword(e.target.value)}
        placeholder="Password"
      />
      <button type="submit">Login</button>
    </form>
  );
}
```

### Pros of controlled components
- **Single source of truth** — form state is predictable
- **Instant validation** — validate on every keystroke
- **Conditional UI** — disable buttons, show errors based on state
- **Easy to reset** — just reset the state

### Common input types

```jsx
// Textarea
<textarea value={text} onChange={e => setText(e.target.value)} />

// Select
<select value={selected} onChange={e => setSelected(e.target.value)}>
  <option value="">Choose...</option>
  <option value="a">Option A</option>
</select>

// Checkbox
<input type="checkbox" checked={isChecked} onChange={e => setIsChecked(e.target.checked)} />

// Radio group
<label>
  <input type="radio" name="gender" value="male" checked={gender === 'male'} onChange={e => setGender(e.target.value)} />
  Male
</label>
```

## Uncontrolled Components

Uncontrolled components store form data in the DOM itself, accessed via `ref`.

```jsx
function SimpleForm() {
  const nameRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(nameRef.current.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={nameRef} defaultValue="John" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### When to use uncontrolled

- Simple forms with no validation needed
- Third-party integration that manages its own state
- File inputs (must be uncontrolled)

### File Inputs

File inputs are always uncontrolled because file data is read-only from the DOM:

```jsx
function FileUpload() {
  const fileRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    const file = fileRef.current.files[0];
    // Read file with FileReader or upload via FormData
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" ref={fileRef} accept="image/*" multiple />
      <button type="submit">Upload</button>
    </form>
  );
}
```

## Form Validation

### Inline validation (no library)

```jsx
function ValidatedForm() {
  const [values, setValues] = useState({ email: '', password: '' });
  const [errors, setErrors] = useState({});

  const validate = () => {
    const newErrors = {};
    if (!values.email.includes('@')) newErrors.email = 'Invalid email';
    if (values.password.length < 6) newErrors.password = 'Min 6 characters';
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (validate()) submitData(values);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={values.email} onChange={e => setValues(v => ({ ...v, email: e.target.value }))} />
      {errors.email && <span className="error">{errors.email}</span>}

      <input type="password" value={values.password} onChange={e => setValues(v => ({ ...v, password: e.target.value }))} />
      {errors.password && <span className="error">{errors.password}</span>}

      <button type="submit">Submit</button>
    </form>
  );
}
```

## Form Libraries

### React Hook Form

Best for **performance** — uses uncontrolled components with refs, reducing re-renders.

```jsx
import { useForm } from 'react-hook-form';

function HookForm() {
  const { register, handleSubmit, formState: { errors } } = useForm();
  const onSubmit = data => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email', { required: 'Email is required', pattern: /^\S+@\S+$/i })} />
      {errors.email && <p>{errors.email.message}</p>}

      <input type="password" {...register('password', { required: true, minLength: 6 })} />
      {errors.password && <p>Password must be at least 6 characters</p>}

      <button type="submit">Submit</button>
    </form>
  );
}
```

### Formik

Good for **complex forms** with dynamic fields and step wizards.

```jsx
import { Formik, Field, Form, ErrorMessage } from 'formik';

function FormikForm() {
  return (
    <Formik
      initialValues={{ email: '', password: '' }}
      validate={values => {
        const errors = {};
        if (!values.email) errors.email = 'Required';
        return errors;
      }}
      onSubmit={(values, { setSubmitting }) => {
        console.log(values);
        setSubmitting(false);
      }}
    >
      <Form>
        <Field name="email" type="email" />
        <ErrorMessage name="email" component="div" />
        <Field name="password" type="password" />
        <ErrorMessage name="password" component="div" />
        <button type="submit">Submit</button>
      </Form>
    </Formik>
  );
}
```

## Complex Forms

### Dynamic fields (add/remove)

```jsx
function DynamicForm() {
  const [fields, setFields] = useState([{ name: '', value: '' }]);

  const addField = () => setFields([...fields, { name: '', value: '' }]);
  const removeField = (index) => setFields(fields.filter((_, i) => i !== index));
  const updateField = (index, key, value) => setFields(
    fields.map((f, i) => i === index ? { ...f, [key]: value } : f)
  );

  return (
    <form>
      {fields.map((field, index) => (
        <div key={index}>
          <input value={field.name} onChange={e => updateField(index, 'name', e.target.value)} placeholder="Name" />
          <input value={field.value} onChange={e => updateField(index, 'value', e.target.value)} placeholder="Value" />
          <button type="button" onClick={() => removeField(index)}>✕</button>
        </div>
      ))}
      <button type="button" onClick={addField}>+ Add Field</button>
    </form>
  );
}
```

## Controlled vs Uncontrolled Summary

| Aspect | Controlled | Uncontrolled |
|--------|-----------|-------------|
| State location | React state (useState) | DOM (ref) |
| Value source | State | DOM ref.current.value |
| Updates | onChange handler | ref access on submit |
| Re-renders on input | Yes | No |
| Validation | Instant, per keystroke | On submit |
| Reset | Set state to initial | Reset() form method, or manual |
| File input | Not possible | Required |
| Complexity | More code | Less code |
| Debugging | Easier (state is inspectable) | Harder (state in DOM) |

**Recommendation:** Use controlled components by default. Use uncontrolled for file inputs, performance-critical large forms (with React Hook Form), or when integrating with non-React code.
