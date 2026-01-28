# Form Definition Architecture

This document outlines the architectural pattern used to define the **data structure**, **validation rules**, and **navigation flow** of the Step Form. By extracting this logic into a dedicated definition file, we achieve a separation of concerns where the React component handles the _behavior_ (rendering, transitions) while this file dictates the _rules_ and _content_.

## 1. State Modeling (`FormData`)

This section defines the "shape" of the data as it exists within the application state. It serves as the single source of truth for TypeScript intellisense throughout the form.

**Purpose:**

- **Step-Based Namespace:** Keys in the type often correspond to specific steps (e.g., `vehicleTypes`, `vehicleDetails`).
- **Placeholder States:** Steps that are purely informational (like `initialStep` or `vehicleDetailsReview`) are typed as `null`. This ensures they exist in the form's state registry even if they don't collect user input, preventing "undefined" errors during navigation.

**Example:**

```typescript
export type FormData = {
  initialStep: null // Informational step, no data collected
  vehicleTypes: string[] // Data collected in Step 2
  vehicleDetails: Array<{ ... }> // Data collected in Step 3
  vehicleDetailsReview: null // Review step, no data collected
}

```

## 2. Advanced Collection Validation (`superRefine`)

Standard validation (like "is this field required?") is handled by basic Zod primitives. However, complex logic involving **inter-dependency** or **collection integrity** is handled via `superRefine`.

**Purpose:**

- **Cross-Item Validation:** This is essential for arrays of objects. It allows us to compare items against each other (e.g., checking for duplicate License Plates across the entire list).
- **Deep Error Mapping:** The `ctx.addIssue` method allows us to manually target a specific field within a specific row (e.g., `path: [index, "name"]`), ensuring the red error text appears exactly under the problematic input field in the UI.

**Example:**

```typescript
.superRefine((vehicles, ctx) => {
  const nameSet = new Set()
  vehicles.forEach((vehicle, index) => {
    // 1. Basic field checks within the loop
    if (!vehicle.name) { /* ... error logic ... */ }

    // 2. Cross-item duplicate check
    if (vehicle.name && nameSet.has(vehicle.name.trim())) {
      ctx.addIssue({
        code: "custom",
        message: "License plate can't be duplicated",
        path: [index, "name"], // Targets the specific row and input
      })
    }
    nameSet.add(vehicle.name.trim())
  })
})

```

## 3. Incremental Validation Strategy (`stepSchemas`)

This object is the "Safety Gate" registry. It maps every step ID to a specific validation schema.

**Purpose:**

- **Checkpoint Validation:** Allows the Form Component to validate _only_ the current step's data before letting the user proceed. Without this, the form would try to validate the entire object at step 1 and fail because step 3 data is missing.
- **Bypass Logic:** Informational steps use `z.literal(null)` to effectively say "Always valid, nothing to check here."

**Example:**

```typescript
export const stepSchemas: Record<StepKey, z.ZodSchema> = {
  // No validation needed for intro
  initialStep: z.literal(null),

  // Strict validation for Step 2
  vehicleTypes: vehicleTypesSchema,

  // Complex array validation for Step 3
  vehicleDetails: vehicleDetailsSchema,
};
```

## 4. UI Configuration & Flow Control (`steps` & `stepOrder`)

This section contains the static metadata and linear progression of the wizard.

**Purpose:**

- **Content Separation:** Keeping titles and subtitles here cleans up the React component, making it purely about logic.
- **Linear Flow:** `stepOrder` is a derived array that defines the sequence. Reordering the steps in the UI is as simple as rearranging the objects in the `steps` array; the code automatically recalculates the flow.

**Example:**

```typescript
export const steps: Step[] = [
  {
    id: "initialStep",
    title: "Add Road Vehicles", // Header text
    subtitle: "",
  },
  {
    id: "vehicleTypes",
    title: "What type of vehicle is this?",
    subtitle: "1 of 2 Questions", // Progress indicator text
  },
  // ...
];

// Automatically generates ["initialStep", "vehicleTypes", "vehicleDetails", ...]
export const stepOrder = steps.map((step) => step.id);
```
