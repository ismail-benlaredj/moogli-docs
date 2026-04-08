# Comprehensive Codebase Refactoring Tasks

## 1. Design System Migration (`src/components/ui` → `src/components/ds`)
**Goal**: Completely eliminate `src/components/ui` by migrating all components to `ds` equivalents.

### 1.1 Pre-Migration Enhancements to `ds`
- [ ] **`ds/Button`**: Port `loading` prop logic from `ui/button.tsx`.
- [ ] **`ds/Button`**: Port `ButtonAsync` wrapper from `ui/button.tsx`.
- [ ] **`ds/Button`**: Port `ButtonLink` wrapper from `ui/button.tsx`.
- [ ] **`ds/Tabs`**: Port `localStore` controlled pattern from `ui/Tabs.tsx` to a wrapper component using `ds/Tabs`.
- [ ] **`ds/Chart`**: Create `ds/Chart` folder and migrate `ui/chart.tsx`.
- [ ] **`ds/Chart`**: Migrate `ui/radial-chart-text.tsx` to `ds/Chart`.

### 1.2 Buttons & Navigation Migration
- [ ] Replace `ui/Spinner.tsx` imports with `ds/Spinner.tsx`. Delete `ui/Spinner.tsx`.
- [ ] Replace `ui/button.tsx` imports with `ds/Button`. Delete `ui/button.tsx` and `ui/buttonVariants.tsx`.
- [ ] Migrate `ui/button-group.tsx` to flexbox layouts using `ds/Button`. Delete `ui/button-group.tsx`.
- [ ] Replace `ui/Anchor.tsx` imports with `ds/Typography` links. Delete `ui/Anchor.tsx`.

### 1.3 Forms & Inputs Migration
- [ ] Replace `ui/input.tsx` imports with `ds/Input`. Delete `ui/input.tsx`.
- [ ] Replace `ui/select.tsx` imports with `ds/Select`. Delete `ui/select.tsx`.
- [ ] Consolidate `ui/checkbox.tsx`, `ui/rect-checkbox.tsx`, and `ui/square-checkbox.tsx` into `ds/Checkbox.tsx`. Delete all three `ui` files.
- [ ] Identify `ui/Switch.tsx` usages (used as Tab Switcher) and replace them with `ds/Tabs`. Delete `ui/Switch.tsx`.
- [ ] Replace `ui/progress.tsx` and `ui/form-progress.tsx` with `ds/Progress.tsx`. Delete both `ui` files.

### 1.4 Tables & Data Display Migration
- [ ] Replace `ui/table.tsx` imports with `ds/Table`. Delete `ui/table.tsx`.
- [ ] Move any unique logic from `ui/table-cells.tsx` to `ds/Table/TableCells.tsx`. Delete `ui/table-cells.tsx`.
- [ ] Move any unique logic from `ui/table-rows.tsx` to `ds/Table/`. Delete `ui/table-rows.tsx`.
- [ ] Replace `ui/pagination.tsx` and `ui/pagination-menu.tsx` with `ds/Pagination.tsx`. Delete both `ui` files.
- [ ] Replace `ui/Sections.tsx`, `ui/SectionList.tsx`, and `ui/SectionContentRecords.tsx` with `ds` structural components. Delete the three `ui` files.
- [ ] Replace `ui/card.tsx` with `ds/Card`. Delete `ui/card.tsx`.
- [ ] Replace `ui/calendar.tsx` with `ds/Calendar`. Delete `ui/calendar.tsx`.

### 1.5 Overlays & Feedback Migration
- [ ] Replace `ui/modal.tsx` imports with `ds/Dialog`. Delete `ui/modal.tsx`.
- [ ] Replace `ui/support-modal.tsx` with `ds/Dialog`. Delete `ui/support-modal.tsx`.
- [ ] Replace `ui/popover.tsx` with `ds/Popover`. Delete `ui/popover.tsx`.
- [ ] Replace `ui/tooltip.tsx` with `ds/Tooltip.tsx`. Delete `ui/tooltip.tsx`.
- [ ] Replace `ui/dropdown-menu.tsx` and `ui/menu-tab.tsx` with `ds/Dropdown` or `ds/Select`. Delete both `ui` files.
- [ ] Replace `ui/sheet.tsx` and `ui/sheetContext.tsx` with `ds/Sheet`. Delete both `ui` files.
- [ ] Migrate `ui/toast.tsx` to a global utility or `ds/Toast`. Delete `ui/toast.tsx`.
- [ ] Move `ui/InReviewModeContext.tsx` to `src/context/` or `src/hooks/`. Delete `ui/InReviewModeContext.tsx`.

### 1.6 Final Deletion
- [ ] Verify `src/components/ui` is entirely empty.
- [ ] Delete the `src/components/ui` directory.

---

## 2. Wizard Architecture Consolidation
**Goal**: Resolve the triple-folder redundancy for wizards and establish a single pattern.

### 2.1 Folder Restructuring
- [ ] Rename `src/components/shared/wizard` to `src/components/shared/wizard-layouts` (for dialog containers, full-screen layouts, etc.).
- [ ] Rename `src/components/shared/wizards` to `src/components/shared/wizard-steps` (for step contents like SelectionStep, ReviewStep).
- [ ] Update all project imports pointing to `shared/wizard` and `shared/wizards`.

### 2.2 Component Standardization
- [ ] Refactor `SelectionStep.tsx` (in `wizard-steps`) to use `ds/Dialog` instead of `ui/modal`.
- [ ] Refactor `StepFormDialog.tsx` (in `wizard-layouts`) to use `ds/Dialog` primitives natively.
- [ ] Refactor `WizardModal.tsx` (in `src/components/shared/modals/`) to use `ds/Dialog`.
- [ ] Ensure specific implementations in `src/components/wizards/` (e.g., `AddRefrigerantEquipmentWizard`) import purely from `wizard-layouts` and `wizard-steps`.

### 2.3 Form Page Layout Unification
- [ ] Compare layout structures between `src/components/features/form/FormPage.tsx` and `src/components/shared/wizard-layouts/FormPage.tsx`.
- [ ] Compare `src/components/features/form/FormWProgress.tsx` with `src/components/shared/wizard-layouts/FormWProgress.tsx`.
- [ ] Merge these duplicates into a single "Source of Truth" inside `src/components/shared/wizard-layouts/`.
- [ ] Update all features to use the unified layout components.

---

## 3. Shared Form Components Refactoring
**Goal**: Clean up React Hook Form (RHF) wrappers and remove redundant logic.

### 3.1 Base Elements Cleanup
- [ ] Audit `src/components/shared/forms/elements/` (`label.tsx`, `errorMessage.tsx`, `wrappers.tsx`).
- [ ] Compare these elements with the integrated labels and error states in `ds/Input/Input.tsx`.
- [ ] Consolidate into a single set of form-wrapper utilities shared by both `ds` primitives and `shared/forms`.
- [ ] Delete redundant base elements in `shared/forms/elements/`.

### 3.2 RHF Wrapper Updates
- [ ] Refactor `src/components/shared/forms/Select.tsx` to import its base components strictly from `ds/Select`.
- [ ] Refactor `src/components/shared/forms/switch.tsx` to import its base components strictly from `ds/Switch`.
- [ ] Refactor `src/components/shared/forms/Checkbox.tsx` to import its base components strictly from `ds/Checkbox`.
- [ ] Remove all direct `@radix-ui/react-*` imports from the `src/components/shared/forms/` directory.

---

## 4. Shared Display Components Cleanup
**Goal**: Remove specific duplicated display components in `shared`.

### 4.1 Modals & Cards
- [ ] Merge `src/components/shared/DeleteModal.tsx` and `src/components/ui/DeleteModal.tsx` into a single `DeleteDialog` in `src/components/shared/modals/`.
- [ ] Update `src/components/shared/Card.tsx` (the starred version) to render `ds/Card` primitives instead of generic `div`s.
- [ ] Update `src/components/shared/StatusBadge.tsx` to use `ds/Badge` without referencing legacy `ui` classes.
- [ ] Update `src/components/shared/TablePagination.tsx` to use `ds/Select` instead of `shared/forms/Select.tsx`.

### 4.2 Folder Reorganization
- [ ] Rename `src/components/shared/info-ds` to `src/components/shared/info-blocks`.
- [ ] Move `src/components/shared/typography/HeaderWLine.tsx` to `ds/Typography/` or a general layout folder.

---

## 5. API and Store Cleanup
**Goal**: Remove unused legacy API definitions and enforce organization.

### 5.1 Redux Slice Cleanup
- [ ] Search for all usages of endpoints from `src/store/ResultsAPI.ts`.
- [ ] Migrate those usages to the endpoints inside `src/store/results/resultAPI.ts`.
- [ ] Delete `src/store/ResultsAPI.ts`.

### 5.2 API Folder Consolidation
- [ ] Create `src/store/api/` (or similar feature folders).
- [ ] Move root-level API slices (`fuelAPI.ts`, `LocationsAPI.tsx`, `mobileCombustionAPI.ts`, `companyDetailsAPI.ts`, etc.) into the new folder structure.
- [ ] Update imports across the application.

---

## 6. Utilities, Hooks & Assets Refactoring
**Goal**: Normalize the location of helper functions and assets.

### 6.1 Hooks Relocation
- [ ] Move `src/lib/useCountries.ts` to `src/hooks/useCountries.ts`.
- [ ] Move `src/lib/useCurrencies.ts` to `src/hooks/useCurrencies.ts`.
- [ ] Identify any other custom hooks scattered in `lib/` or `utils/` and move them to `src/hooks/`.

### 6.2 Utilities Consolidation
- [ ] Move `src/utils/authUtils.ts` to `src/lib/authUtils.ts`.
- [ ] Delete the `src/utils/` folder completely.
- [ ] Perform a dead-code audit on `src/lib/utils.ts` and `src/constants/` to remove unused variables and functions.

### 6.3 Assets Modernization
- [ ] Audit all imports from `src/assets/iconsLegacy/`.
- [ ] Replace them with equivalent modern SVGs from `src/assets/icons/`.
- [ ] Delete the `src/assets/iconsLegacy/` directory.

---

## 7. Page-Level Refactoring
**Goal**: Improve readability and maintainability of top-level pages.

### 7.1 Dashboard De-bloat
- [ ] Break down `src/pages/dashboard/page.tsx` into smaller, atomic components inside `src/pages/dashboard/components/`.

### 7.2 Hardcoded Styling Replacements
- [ ] Identify hardcoded typography classes (e.g., `text-[13px]`, `text-gray-500 font-semibold`) across dashboard pages.
- [ ] Replace hardcoded typography with proper `ds/Typography` components or standard tailwind scale equivalents.
