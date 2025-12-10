# Frontend Troubleshooting Rules

### 1. Add Zustand Package

**Error Message:**
```
Failed to resolve import "zustand"
```

**Solution:**
Run `npm install zustand` command

---


### 2. Loading Import Error

**Error Message:**
```
import { Loading } from '../../components/common/Loading';
Uncaught SyntaxError: The requested module does not provide an export named 'Loading'
```

**Solution:**
Fix to `export const Loading: React.FC<LoadingProps> = ({ message = 'Loading...' }) =>`

---


### 3. 500 Error in Local Environment (Port 3000)

**Error Message:**
```
GET http://localhost:3000/orders 500 (Internal Server Error)
```

**Cause:**
```typescript
useEffect(() => {
  fetchOrders();
}, [fetchOrders]);
// When fetchOrders() is called and backend is unavailable, it fails and screen does not display
```

**Solution:**
Handle axios calls as follows so UI displays normally even when backend is unavailable:
```typescript
const fetchOrders = useCallback(async (params?: { page?: number; size?: number }) => {
  setLoading(true);
  clearError();
  try {
    const response = await orderService.getAll(params);
    setOrders(response.content || []);
  } catch (err: any) {
    // Set empty array even when backend connection fails so UI displays normally
    setOrders([]);
  } finally {
    setLoading(false);
  }
}, [setOrders, setLoading, setError, clearError]);
```

---


### 4. Spring Data REST HAL Format Response Parsing Error

**Error Message:**
```
Uncaught ReferenceError: [entityId] is not defined
```
Or no data displayed in table despite successful API response

**Cause:**
Spring Data REST returns HAL format with _embedded wrapper and ID in _links.self.href:
```json
{
  "_embedded": {
    "orders": [{ ...data, "_links": { "self": { "href": "http://localhost:8084/orders/1" }}}]
  },
  "page": {...}
}
```
Frontend expects flat structure with direct ID field like orderId.

**Solution:**
Add HAL parser to service file that extracts ID from _links.self.href and unwraps _embedded data:
```typescript
const extractIdFromHref = (href: string): number => {
  return parseInt(href.split('/').pop()!, 10);
};

const parseHalResponse = (halData: any) => {
  const items = halData._embedded?.orders?.map((item: any) => ({
    ...item,
    orderId: extractIdFromHref(item._links.self.href)
  })) || [];
  return { content: items, page: halData.page };
};

getAll: async (params) => {
  const { data } = await apiClient.get(BASE_PATH, { params });
  return parseHalResponse(data);
}
```

---


### 5. Wireframe-based Command Components Not Connected to Page

**Error Message:**
- Wireframe generation button always disabled
- Modal onSubmit not working
- TypeScript error "Property 'onSubmit' is missing"

**Cause:**
- Command function not imported from hook
- Modal onSubmit prop missing
- Disabled logic lacking in button

**Solution:**
```typescript
const { modifyOrder, fetchOrders } = useOrder();

<Button
  disabled={!selectedOrderLocal}
  onClick={() => setModifyOrderModalOpen(true)}
>
  Modify Order
</Button>

<ModifyOrderModal
  open={modifyOrderModalOpen}
  onClose={() => setModifyOrderModalOpen(false)}
  onSubmit={async (cmd) => {
    await modifyOrder(cmd);
    fetchOrders();
  }}
  orderId={selectedOrderLocal?.orderId}
/>
```

---


### 6. ERP Common Components Not Connected to Pages

**Error Pattern:**
- Export/Import buttons not displayed on page
- AdvancedSearch/FilterBuilder not functioning
- ERP components generated but not actually used

**Cause:**
**Phase 2 (Page Integration)** step from erp-component-prd.md not executed:
- Component files generated but not imported in pages
- Phase 2 skipped after completing Phase 1 (component creation)

**Detection:**
Check connection status with the following commands:
```bash
# Check if each ERP component is imported in pages
grep -r "import.*ExportDialog" src/pages/
grep -r "import.*ImportDialog" src/pages/
grep -r "import.*AdvancedSearch" src/pages/
```
If no results, Phase 2 was not executed.

**Solution:**
**Phase 2 MUST be completed.** Connect to Aggregate list pages using the following pattern:

```typescript
// [Aggregate]ListView.tsx
import { useState } from 'react';
import { ExportDialog } from '../components/common/ExportDialog';
import { ImportDialog } from '../components/common/ImportDialog';
import { AdvancedSearch } from '../components/common/AdvancedSearch';
import { Filter } from '../utils/filterUtils';
import { Download, Upload } from '@mui/icons-material';

const [Aggregate]ListView: React.FC = () => {
  // ERP component state
  const [filters, setFilters] = useState<Filter[]>([]);
  const [showExport, setShowExport] = useState(false);
  const [showImport, setShowImport] = useState(false);

  // Field definitions (same as table columns)
  const fields = [
    { name: 'id', label: 'ID', type: 'number' },
    { name: 'name', label: 'Name', type: 'string' },
    // ... other fields
  ];

  return (
    <Container maxWidth="xl">
      {/* AdvancedSearch - replaces search area */}
      <AdvancedSearch
        filters={filters}
        onFilterChange={setFilters}
        onSearch={handleSearch}
        availableFields={fields}
        placeholder="Enter search term"
      />

      {/* Export/Import buttons - action bar */}
      <Box sx=\{{ display: 'flex', gap: 1, mb: 2 }}>
        <Button startIcon={<Download />} onClick={() => setShowExport(true)}>
          Export
        </Button>
        <Button startIcon={<Upload />} onClick={() => setShowImport(true)}>
          Import
        </Button>
      </Box>

      {/* Table */}
      <Table>{/* ... */}</Table>

      {/* Dialogs */}
      <ExportDialog
        open={showExport}
        onClose={() => setShowExport(false)}
        data={data}
        fields={fields}
        filename="[aggregate]-export"
      />
      <ImportDialog
        open={showImport}
        onClose={() => setShowImport(false)}
        onImport={handleImport}
        targetFields={fields.map(f => ({ ...f, required: false }))}
      />
    </Container>
  );
};
```

**Validation Checklist:**
- [ ] ExportDialog imported in at least 1 page
- [ ] ImportDialog imported in at least 1 page
- [ ] AdvancedSearch used in list pages
- [ ] Component props correctly passed
- [ ] Dialog opens on button click

---


### Best Practices

1. **Always Handle Backend Unavailability** - Set empty array/object in catch block
2. **Parse HAL Responses Correctly** - Extract ID from _links.self.href
3. **Connect Modals Correctly** - Import functions from hooks and pass to onSubmit
4. **Export Components Correctly** - Use named exports with proper TypeScript types
5. **Install Dependencies** - Check package.json and install missing packages
6. **Complete Phase 2 for ERP Components** - After component creation, MUST connect to pages
7. **Verify ERP Component Integration** - Check import status using grep commands
