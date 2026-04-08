# GenericDataTable Component Documentation

The `GenericDataTable` is a flexible, type-safe React component designed to render data in a tabular format with support for loading states, empty states, sticky headers, and custom action cells. It is built on top of the project's design system table components.

## Location
- Component: `src/components/shared/GenericDataTable.tsx`

---

## Component API

### `GenericDataTable<T>`
The main component for rendering data tables.

#### Props
| Prop | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `data` | `T[]` | Yes | - | Array of data items to display. `T` must extend `{ id: string \| number }`. |
| `headers` | `HeaderConfig[]` | Yes | - | Configuration for table columns. |
| `renderRow` | `(item: T, refetch: () => void) => ReactNode` | Yes | - | Function that returns a `TableRow` for each data item. |
| `refetch` | `() => void` | Yes | - | Function to trigger a data refresh. |
| `isLoading` | `boolean` | Yes | - | Controls the display of the loading skeleton. |
| `loadingRowCount` | `number` | No | `10` | Number of rows to show in the loading skeleton. |
| `emptyStateTitle` | `string` | No | `"No Data Found"` | Title shown when `data` is empty. |
| `emptyStateDescription` | `string` | No | `"Add an item to get started."` | Description shown when `data` is empty. |
| `headerActions` | `ReactNode` | No | - | Actions (e.g., "Add" button) displayed above the table. |
| `className` | `string` | No | - | Additional CSS classes for the container. |
| `stickyActions` | `boolean` | No | `true` | If true, the last column (usually actions) remains sticky when scrolling horizontally. |

---

## Supporting Types & Sub-components

### `HeaderConfig`
Defines the structure of a column header.
```typescript
export interface HeaderConfig {
  label: ReactNode;
  description?: string | ReactNode; // Optional tooltip content
}
```

### `TableCellStatus`
A specialized cell for displaying status badges with an icon.
- **Props**: `status` ("neutral" | "warning" | "positive"), `label`, `subtitle`?, `icon`?

### `TableActionsCell`
A specialized cell for the last column, typically containing action buttons.
- **Props**: `children`, `className`?
- **Behavior**: Sticky by default to ensure actions are always visible.

---

## Utility Functions

- `formatValue(value: number, unit: ReactNode)`: Formats numeric values to 2 decimal places and appends a unit.
- `generateStatus(months: number, threshold?: number)`: Returns "Pending", "In Progress", or "Completed" based on progress.
- `formatDateRange(startDate, endDate, ...)`: Formats a date range string.
- `PROGRESS_STATUS_MAP`: A mapping of status strings to color variants ("neutral", "warning", "positive").

---

## Usage Examples

### Basic Usage with Inline Row Rendering
As seen in `ReportingYearsListCard.tsx`:

```tsx
const headers: HeaderConfig[] = [
  { label: "Year", description: "The reporting period" },
  { label: "Status" },
  { label: "", description: undefined } // Action column
];

const renderRow = (item: ReportingYear) => (
  <TableRow key={item.id}>
    <TableCell>{item.name}</TableCell>
    <TableCellStatus 
      status={item.isLocked ? "positive" : "warning"} 
      label={item.isLocked ? "Locked" : "Open"} 
    />
    <TableActionsCell>
      <Button onClick={() => handleEdit(item)}>Edit</Button>
    </TableActionsCell>
  </TableRow>
);

<GenericDataTable
  data={reportingYears}
  headers={headers}
  renderRow={renderRow}
  isLoading={isLoading}
  refetch={refetch}
  headerActions={<Button>Add Year</Button>}
/>
```

### Advanced Usage with Component-based Rows
As seen in `MobileCombustionTables.tsx`:

```tsx
const vehicleRow = useCallback(
  (vehicle: EnhancedVehicleData, refetch: () => void) => (
    <VehicleRow key={vehicle.id} rowData={vehicle} refetch={refetch} />
  ),
  []
);

<GenericDataTable
  data={vehicles}
  headers={VEHICLE_HEADERS}
  renderRow={vehicleRow}
  refetch={handleVehicleChange}
  isLoading={isVehicleLoading}
  emptyStateTitle="No Vehicles Found"
/>
```
