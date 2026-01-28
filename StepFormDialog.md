# StepFormDialog

A compound dialog component for multi-step form workflows, built on shadcn's Dialog with React Hook Form integration.

## Overview

`StepFormDialog` provides a structured modal interface for complex form workflows that require multiple steps. It manages dialog state, form context, and ensures proper layout with required action buttons.

## Component Structure

### Required Subcomponents

The component uses a compound pattern with four required subcomponents:

| Subcomponent                  | Purpose                                      | Required |
| ----------------------------- | -------------------------------------------- | -------- |
| `StepFormDialogTrigger`       | Element that opens the dialog (e.g., button) | Yes      |
| `StepFormDialogContent`       | Main content area for form steps             | Yes      |
| `StepFormDialogCancelButton`  | Cancel/dismiss action button                 | Yes      |
| `StepFormDialogConfirmButton` | Primary submit/confirmation button           | Yes      |

## Props

### StepFormDialog (Root Component)

| Prop           | Type                      | Default     | Description                                  | Required |
| -------------- | ------------------------- | ----------- | -------------------------------------------- | -------- |
| `children`     | `ReactNode`               | -           | Must include all four required subcomponents | Yes      |
| `title`        | `string`                  | -           | Dialog title displayed in header             | Yes      |
| `subtitle`     | `string`                  | `undefined` | Optional subtitle below title                | No       |
| `defaultOpen`  | `boolean`                 | `false`     | Initial open state for uncontrolled usage    | No       |
| `open`         | `boolean`                 | `undefined` | Controlled open state                        | No       |
| `onOpenChange` | `(open: boolean) => void` | `undefined` | Callback when dialog opens/closes            | No       |
| `form`         | `UseFormReturn<FormData>` | `undefined` | React Hook Form methods object               | No       |

### StepFormDialogTrigger

| Prop       | Type        | Default | Description                        | Required |
| ---------- | ----------- | ------- | ---------------------------------- | -------- |
| `children` | `ReactNode` | -       | Trigger element (usually a button) | Yes      |

### StepFormDialogContent

| Prop       | Type        | Default | Description                      | Required |
| ---------- | ----------- | ------- | -------------------------------- | -------- |
| `children` | `ReactNode` | -       | Form content and step components | Yes      |

### StepFormDialogCancelButton

| Prop       | Type        | Default | Description             | Required |
| ---------- | ----------- | ------- | ----------------------- | -------- |
| `children` | `ReactNode` | -       | Cancel button component | Yes      |

### StepFormDialogConfirmButton

| Prop       | Type        | Default | Description                     | Required |
| ---------- | ----------- | ------- | ------------------------------- | -------- |
| `children` | `ReactNode` | -       | Confirm/Submit button component | Yes      |

## Usage Examples

### Basic Usage

```tsx
import { useForm } from "react-hook-form";

const Example = () => {
  const form = useForm();

  return (
    <StepFormDialog
      title="Create Account"
      subtitle="Complete your registration"
      form={form}
    >
      <StepFormDialogTrigger>
        <Button>Open Form</Button>
      </StepFormDialogTrigger>

      <StepFormDialogContent>
        {/* Your form content here */}
        <div>Form steps go here</div>
      </StepFormDialogContent>

      <StepFormDialogCancelButton>
        <Button variant="secondary">Cancel</Button>
      </StepFormDialogCancelButton>

      <StepFormDialogConfirmButton>
        <Button variant="primary">Submit</Button>
      </StepFormDialogConfirmButton>
    </StepFormDialog>
  );
};
```

### Controlled Dialog State

```tsx
const [isOpen, setIsOpen] = useState(false);

return (
  <StepFormDialog
    title="Edit Profile"
    open={isOpen}
    onOpenChange={setIsOpen}
    form={form}
  >
    {/* Subcomponents remain the same */}
  </StepFormDialog>
);
```

### With Form Validation

```tsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

const Example = () => {
  const form = useForm({
    resolver: zodResolver(schema),
  });

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <StepFormDialog title="Login" form={form}>
      <StepFormDialogTrigger>
        <Button>Login</Button>
      </StepFormDialogTrigger>

      <StepFormDialogContent>
        <form onSubmit={form.handleSubmit(onSubmit)}>{/* Form fields */}</form>
      </StepFormDialogContent>

      <StepFormDialogCancelButton>
        <Button variant="secondary">Cancel</Button>
      </StepFormDialogCancelButton>

      <StepFormDialogConfirmButton>
        <Button type="submit">Login</Button>
      </StepFormDialogConfirmButton>
    </StepFormDialog>
  );
};
```

## Layout & Styling

### Default Layout

The component provides:

- Fixed header with title, subtitle, and close button
- Scrollable main content area
- Fixed footer with action buttons
- Responsive sizing (900px max width on desktop)

### Customization

While the component has a fixed structure, you can customize:

- Button styles within the trigger/cancel/confirm button slots
- Form content layout within the content area
- Dialog sizing via the `DialogContent` className prop

## Form Integration

### React Hook Form

Pass the form object from `useForm()` to enable form context:

```tsx
const form = useForm();
return <StepFormDialog form={form}>{/* ... */}</StepFormDialog>;
```

### Form Submission

Handle form submission via the confirm button:

```tsx
<StepFormDialogConfirmButton>
  <Button onClick={form.handleSubmit(onSubmit)}>Submit</Button>
</StepFormDialogConfirmButton>
```

## Error Handling

### Missing Required Children

If any required subcomponent is missing, you'll see:

```
Error: StepFormDialog is missing required children: StepFormDialogTrigger, StepFormDialogCancelButton, StepFormDialogConfirmButton
```

Ensure all four subcomponents are direct children of `StepFormDialog`.

### Controlled vs Uncontrolled

- **Uncontrolled**: Use `defaultOpen` prop
- **Controlled**: Use `open` and `onOpenChange` props together
- Don't mix both patterns
