Here is a detailed architectural documentation for the Step Form Dialog pattern, based on the `AddVehiclesForm` component.

---

# Step Form Dialog Architecture

This document outlines the architectural pattern used to build multi-step forms within a modal dialog. This component (`AddVehiclesForm`) serves as the canonical template.

The architecture is designed to decouple **navigation logic**, **state management**, **validation**, and **rendering**, allowing developers to easily create new wizards by swapping out the step components and schema definitions.

## 1. Step Content Registry (`getStepComponent`)

This section acts as the internal router for the dialog. Instead of rendering all steps and hiding them with CSS, we dynamically mount only the component required for the current step.

**Purpose:**

- Maintains a clean render tree by only mounting relevant DOM elements.
- Maps a string identifier (the `StepKey`) to a specific React Component.
- Injects necessary props (like `Icon`, `purposeLabel`, etc.) into generic wrapper components like `FormStarterStep`.

**Example:**

```tsx
const getStepComponent = (step: StepKey) => {
  switch (step) {
    case "initialStep":
      return (
        <FormStarterStep
        /* configuration props */
        />
      );
    case "vehicleTypes":
      return <DetailedVehicleTypes />;
    // ... additional cases
    default:
      return null;
  }
};
```

## 2. Data Transformation Layer

Forms are often designed for User Experience (UX), while APIs are designed for Database architecture. These two structures rarely match perfectly. This layer bridges that gap.

**Purpose:**

- **Decoupling:** Prevents the UI structure from being dictated by the backend API model.
- **Sanitization:** Handles data formatting (e.g., converting strings to numbers, splitting ID strings, setting default dates) before the data leaves the client.
- **Bulk Handling:** Converts a flat form object into arrays required by bulk-upload endpoints.

**Example:**

```tsx
// Maps UI-friendly "FormData" to Backend-friendly "ICreateRoadVehicleRequest"
const mapFormValuesToApiRequest = (values: FormData, tenantData?: any) => {
  const vehicleDetails = values.vehicleDetails || [];

  return vehicleDetails.map((vehicle) => {
    // Logic to split strings, parse numbers, and format dates happens here
    return {
      licensePlate: vehicle.name,
      fuelTypeId: Number(vehicle.fuelType),
      // ...
    };
  });
};
```

## 3. Centralized Form State

We use `react-hook-form` at the **parent** level (the Dialog level) rather than inside individual steps.

**Purpose:**

- **Data Persistence:** Because steps are unmounted when navigating away (see Section 1), local state inside a step would be lost. Holding state in the parent ensures data is preserved when the user clicks "Back" and "Next".
- **Single Source of Truth:** The `form` object contains the snapshot of the entire wizard, not just the current page.

**Example:**

```tsx
const form = useForm({
  defaultValues: {
    initialStep: null, // Specific object structure for step 1
    vehicleTypes: [], // Specific array for step 2
    // ...
  },
});
```

## 4. Step-Level Validation Strategy

Validation is handled incrementally. Instead of validating the entire schema at the end, we validate the current "chunk" of data before allowing navigation to the next step.

**Purpose:**

- **`checkStepValidity`:** A "silent" check used to disable/enable the "Next" button in real-time without showing red error messages immediately.
- **`validateCurrentStep`:** A "noisy" check triggered on button click. If invalid, it explicitly sets focus and displays error messages on the fields.
- **Schema Isolation:** Uses `stepSchemas[currentStep]` to ensure we only validate fields relevant to the user's current view.

**Example:**

```tsx
// Returns boolean, does NOT trigger UI errors (for button state)
const checkStepValidity = (): boolean => {
  const currentValues = form.getValues(currentStep)
  const result = stepSchema.safeParse(currentValues)
  return result.success
}

// Returns boolean, TRIGGERS UI errors (for navigation gates)
const validateCurrentStep = (): boolean => {
   // ... validation logic
   if (zodError) {
     form.setError(...) // Manually set errors to show in UI
   }
   return false
}

```

## 5. Navigation Controller

The navigation logic acts as a finite state machine, determining whether to advance the index or submit the data.

**Purpose:**

- **Linear Progression:** Calculates the current index based on `stepOrder` array.
- **Submission Gate:** If the user is on the last step, it swaps the "Next" behavior for "Submit" behavior.
- **API Integration:** Handles the async submission, showing loading states, and triggering Toast notifications upon success/failure.

**Example:**

```tsx
const handleNext = () => {
  const currentStepIndex = stepOrder.indexOf(currentStep);

  if (validateCurrentStep()) {
    if (currentStepIndex < stepOrder.length - 1) {
      // Move to next UI step
      setCurrentStep(stepOrder[currentStepIndex + 1]);
    } else {
      // We are at the end, trigger API submission
      handleFormSubmit();
    }
  }
};
```

## 6. Dynamic UI Context (Titles & Buttons)

The dialog shell (Header and Footer) needs to adapt based on the internal state.

**Purpose:**

- **Contextual Feedback:** Updates the Modal Title and Subtitle to guide the user on what is expected in the specific step.
- **Button Logic:**
- _First Step:_ Shows "Get Started" / "Cancel".
- _Middle Steps:_ Shows "Continue" / "Go Back".
- _Last Step:_ Shows "Submit" (or "Submitting...") / "Go Back".

**Example:**

```tsx
const nextButtonText = () => {
  // Logic to determine if we show "Get Started", "Continue", or "Submit"
  // based on currentStep index and isCreating loading state.
};

const StepTitle = useMemo(() => {
  // Fetches title from definition file based on current step ID
  return steps.find((step) => step.id === currentStep)?.title ?? "";
}, [currentStep, steps]);
```

## 7. Compositional Layout

The component uses a Compound Component pattern (`StepFormDialog`, `Trigger`, `Content`, `Footer`) to render the final UI.

**Purpose:**

- **Standardization:** Ensures all wizards in the application have the same padding, width, and animation behavior.
- **Slot Pattern:** `StepFormDialogContent` acts as a generic slot where `getStepComponent(currentStep)` injects the variable content.

**Example:**

```tsx
return (
  <StepFormDialog ... >
    {/* The Button that opens the modal */}
    <StepFormDialogTrigger> ... </StepFormDialogTrigger>

    {/* The Dynamic Content Area */}
    <StepFormDialogContent>
      <div>{getStepComponent(currentStep)}</div>
    </StepFormDialogContent>

    {/* The Navigation Footer */}
    <StepFormDialogCancelButton> ... </StepFormDialogCancelButton>
    <StepFormDialogConfirmButton> ... </StepFormDialogConfirmButton>
  </StepFormDialog>
)

```
