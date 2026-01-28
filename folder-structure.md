# Folder Structure & Organization

This file system architecture follows a **Domain-Driven** or **Feature-First** approach. Instead of separating files by technical type (e.g., putting all forms in a `forms/` folder and all schemas in `schemas/`), we group everything related to the "Road Vehicles" feature into a single, self-contained directory.

## 1. The Directory Tree

Based on the provided screenshot, the structure is organized as follows:

```text
road-vehicles/
├── select-vehicle-types/        # 📁 Step 2: UI Component Isolation
│   └── Step.tsx                 #    Specific UI for selecting types
├── vehicle-details/             # 📁 Step 3: UI Component Isolation
│   └── Step.tsx                 #    Specific UI for entering details
├── vehicles-details-review/     # 📁 Step 4: UI Component Isolation
│   └── Step.tsx                 #    Specific UI for reviewing data
├── AddVehiclesForm.tsx          # 🧠 The Orchestrator (Main Component)
├── form-definition.ts           # 📋 The Rules (Schema & Config)
├── page.tsx                     # 🚪 The Entry Point (Route)
├── consts.ts                    # 🧱 Shared Constants
├── types.ts                     # 🏷️ Shared TypeScript Interfaces
└── utils.ts                     # 🛠️ Shared Helper Functions

```

## 2. Component Purpose & Responsibilities

### The Core Pair (`AddVehiclesForm` & `form-definition`)

These two files sit at the root of the feature folder because they govern the entire process.

- **`AddVehiclesForm.tsx`:** The "Brain." It manages the state, handles API submission, and decides which step folder to render.
- **`form-definition.ts`:** The "Law." It defines the shape of the data, validation rules (Zod schemas), and the order of steps. It has no UI logic.

### The Step Directories

Each step (e.g., `select-vehicle-types`) is given its own directory.

- **Purpose:** Encapsulation. If a specific step requires unique sub-components (like a specific "License Plate Input" or a "Fuel Type Dropdown" that isn't used anywhere else), those sub-components can live inside that step's folder.
- **Maintenance:** This prevents the main `road-vehicles` folder from becoming cluttered with dozens of small helper components that only belong to one specific step.
- **Naming Convention:** Typically, the main file inside is named `Step.tsx` or `index.tsx` so the import path looks clean:
  `import VehicleDetails from "./vehicle-details/Step"`

### The Shared Layer (`utils`, `consts`, `types`)

These files handle cross-step logic to ensure DRY (Don't Repeat Yourself) code.

- **`consts.ts`:** Stores static values used across steps, such as Fuel Type ID mappings or Dropdown options.
- **`utils.ts`:** Contains logic that might be needed in both the _Validation_ and the _UI_. For example, a function that formats a license plate string might be used by the `form-definition.ts` (to validate) and the `vehicle-details` step (to display).

### The Entry Point (`page.tsx`)

- **Role:** This is the Next.js (or router) page wrapper. It is responsible for fetching any initial data required before the form can even open (e.g., `tenantData` or `reportingYear`) and rendering the page layout that hosts the `AddVehiclesForm` button.

---

## 3. Architectural Benefits

1. **Portability:** If you need to move the "Road Vehicles" feature to a different part of the app, you simply move the entire `road-vehicles` folder. You don't have to hunt down orphaned schema files or utility functions scattered across the codebase.
2. **Cognitive Load:** When a developer is working on the "Vehicle Details" step, they only need to look at the `vehicle-details` folder. They are not distracted by the logic for the "Review" step.
3. **Scalability:** If a step becomes too complex (e.g., the "Vehicle Details" step needs a modal inside of it), you can expand the `vehicle-details` folder without affecting the architecture of the parent form.
