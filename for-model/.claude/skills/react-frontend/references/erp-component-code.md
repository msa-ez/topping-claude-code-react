# ERP Common Component Code Generation Requirements

## Overview
Common module example code to be used in ERP Frontend Architecture document (`.claude/agents/erp-component-generator.md`)

## Core Requirements

### 1. Template Type Classification
1. **exportImport**: Export/Import utilities and dialog components
2. **searchFilter**: Search and filter components
3. **activities**: Activity management components
4. **reports**: Report generation components
5. **dashboard**: Dashboard widget components
6. **pageTemplates**: Aggregate and Main page templates

### 2. Placeholder Substitution
Replace the following placeholders with actual values:
- `[ModelName]`: Model/Entity name (e.g., Invoice, Expense, Order)
- `[modelName]`: Lowercase model name (e.g., invoice, expense, order)
- `[aggregateName]`: Aggregate plural form (e.g., invoices, expenses, orders)

## Generated File Structure

```
src/
  /utils/
    exportUtils.ts
    importUtils.ts
  /components/
    /common/
      ExportDialog.tsx
      ImportDialog.tsx
      AdvancedSearch.tsx
    /Activities/
      ActivitiesDashboard.tsx
    /Reports/
      ReportBuilder.tsx
    /Dashboard/
      Dashboard.tsx
      DashboardWidget.tsx
  /pages/
    [ModelName]ListPage.tsx
    HomePage.tsx
```

## Actual Code Generation Examples

### Example 1: Export Utilities
Location: `src/utils/exportUtils.ts`

```typescript
import * as XLSX from 'xlsx';

/**
 * Export data to CSV format
 */
export const exportToCSV = (
  data: any[],
  fields: string[],
  filename?: string
): void => {
  // CSV content generation
  const csvContent = [
    fields.join(','),
    ...data.map(row => fields.map(field => row[field] ?? '').join(','))
  ].join('\n');
  
  // Download via Blob
  const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
  const link = document.createElement('a');
  link.href = URL.createObjectURL(blob);
  link.download = filename || `export_${Date.now()}.csv`;
  link.click();
  URL.revokeObjectURL(link.href);
};

/**
 * Export data to Excel format
 */
export const exportToExcel = (
  data: any[],
  fields: string[],
  filename?: string
): void => {
  const filteredData = data.map(row =>
    fields.reduce((acc, field) => {
      acc[field] = row[field];
      return acc;
    }, {} as any)
  );
  
  const worksheet = XLSX.utils.json_to_sheet(filteredData);
  const workbook = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(workbook, worksheet, 'Sheet1');
  XLSX.writeFile(workbook, filename || `export_${Date.now()}.xlsx`);
};

/**
 * Export data to XML format
 */
export const exportToXML = (
  data: any[],
  fields: string[],
  filename?: string
): void => {
  const xmlContent = [
    '<?xml version="1.0" encoding="UTF-8"?>',
    '<root>',
    ...data.map(row => {
      const items = fields.map(field => `    <${field}>${row[field] ?? ''}</${field}>`).join('\n');
      return `  <item>\n${items}\n  </item>`;
    }),
    '</root>'
  ].join('\n');
  
  const blob = new Blob([xmlContent], { type: 'application/xml' });
  const link = document.createElement('a');
  link.href = URL.createObjectURL(blob);
  link.download = filename || `export_${Date.now()}.xml`;
  link.click();
  URL.revokeObjectURL(link.href);
};
```

### Example 2: Import Utilities
Location: `src/utils/importUtils.ts`

```typescript
import * as XLSX from 'xlsx';

export interface ImportResult<T> {
  success: boolean;
  data: T[];
  errors: Array<{ row: number; message: string }>;
}

/**
 * Import data from CSV file
 */
export const importFromCSV = <T>(
  file: File,
  validator?: (row: any) => string | null
): Promise<ImportResult<T>> => {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = (e) => {
      const text = e.target?.result as string;
      const lines = text.split('\n');
      const headers = lines[0].split(',');
      
      const data: T[] = [];
      const errors: Array<{ row: number; message: string }> = [];
      
      // Parse each line and validate
      for (let i = 1; i < lines.length; i++) {
        // Parse and validate logic
      }
      
      resolve({ success: errors.length === 0, data, errors });
    };
    reader.readAsText(file);
  });
};

/**
 * Import data from Excel file
 */
export const importFromExcel = <T>(
  file: File,
  validator?: (row: any) => string | null
): Promise<ImportResult<T>> => {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = (e) => {
      const data = new Uint8Array(e.target?.result as ArrayBuffer);
      const workbook = XLSX.read(data, { type: 'array' });
      const worksheet = workbook.Sheets[workbook.SheetNames[0]];
      const jsonData = XLSX.utils.sheet_to_json(worksheet);
      
      // Validate and collect results
      const results: T[] = [];
      const errors: Array<{ row: number; message: string }> = [];
      
      resolve({ success: errors.length === 0, data: results, errors });
    };
    reader.readAsArrayBuffer(file);
  });
};
```

### Example 3: Export/Import Dialog Components
Location: `src/components/common/ExportDialog.tsx`, `ImportDialog.tsx`

```typescript
// ExportDialog.tsx
interface ExportDialogProps {
  open: boolean;
  onClose: () => void;
  data: any[];
  availableFields: Array<{ name: string; label: string }>;
  filename?: string;
}

const ExportDialog: React.FC<ExportDialogProps> = ({ ... }) => {
  const [format, setFormat] = useState<'csv' | 'excel' | 'xml'>('excel');
  const [selectedFields, setSelectedFields] = useState<string[]>([]);
  
  return (
    <Dialog open={open} onClose={onClose}>
      <DialogTitle>Export Data</DialogTitle>
      <DialogContent>
        {/* Format selection: Radio buttons for CSV/Excel/XML */}
        {/* Field selection: Checkboxes for columns */}
        {/* Total records display */}
      </DialogContent>
      <DialogActions>
        <Button onClick={onClose}>Cancel</Button>
        <Button onClick={handleExport}>Export</Button>
      </DialogActions>
    </Dialog>
  );
};

// ImportDialog.tsx
interface ImportDialogProps {
  open: boolean;
  onClose: () => void;
  onImport: (data: any[]) => void;
  availableFields: Array<{ name: string; label: string }>;
  validator?: (row: any) => string | null;
}

const ImportDialog: React.FC<ImportDialogProps> = ({ ... }) => {
  const [file, setFile] = useState<File | null>(null);
  const [result, setResult] = useState<ImportResult<any> | null>(null);
  
  return (
    <Dialog open={open} onClose={onClose}>
      <DialogTitle>Import Data</DialogTitle>
      <DialogContent>
        {/* File upload button */}
        {/* Progress indicator */}
        {/* Success/Error display with error list */}
      </DialogContent>
      <DialogActions>
        <Button onClick={onClose}>Cancel</Button>
        <Button onClick={handleImport}>Import</Button>
      </DialogActions>
    </Dialog>
  );
};
```

### Example 4: Advanced Search Component
Location: `src/components/common/AdvancedSearch.tsx`

```typescript
export interface Filter {
  field: string;
  operator: 'equals' | 'contains' | 'greaterThan' | 'lessThan' | 'between';
  value: any;
}

export interface GroupBy {
  field: string;
  order: 'asc' | 'desc';
}

interface AdvancedSearchProps {
  filters: Filter[];
  groupBy: GroupBy[];
  onFilterChange: (filters: Filter[]) => void;
  onGroupByChange: (groupBy: GroupBy[]) => void;
  availableFields: Array<{ name: string; label: string; type: string }>;
}

const AdvancedSearch: React.FC<AdvancedSearchProps> = ({ ... }) => {
  return (
    <Box sx=\{{ mb: 3 }}>
      <Stack direction="row" spacing={2}>
        {/* Autocomplete: Field selection */}
        {/* TextField: Search value input */}
        {/* Button: Add Filter */}
        {/* Button: Clear All */}
      </Stack>
      <Stack direction="row" spacing={1}>
        {/* Chip: Display active filters */}
      </Stack>
    </Box>
  );
};
```

### Example 5: Activities Dashboard Component
Location: `src/components/Activities/ActivitiesDashboard.tsx`

```typescript
export interface Activity {
  id: string;
  title: string;
  description?: string;
  status: 'pending' | 'completed' | 'scheduled';
  dueDate?: Date;
  createdAt: Date;
  userId: string;
}

interface ActivitiesDashboardProps {
  userId: string;
  activities: Activity[];
  onActivityClick?: (activity: Activity) => void;
}

const ActivitiesDashboard: React.FC<ActivitiesDashboardProps> = ({ ... }) => {
  return (
    <Card>
      <CardContent>
        <Typography variant="h6">Activities</Typography>
        <List>
          {/* ListItem: Activity with icon, title, status chip */}
        </List>
      </CardContent>
    </Card>
  );
};
```

### Example 6: Report Builder Component
Location: `src/components/Reports/ReportBuilder.tsx`

```typescript
export interface ReportConfig {
  chartType: 'bar' | 'pie' | 'line';
  groupByField: string;
  aggregateField: string;
  aggregateFunction: 'sum' | 'avg' | 'count';
}

interface ReportBuilderProps {
  model: string;
  availableFields: Array<{ name: string; label: string; type: string }>;
  onSave?: (config: ReportConfig) => void;
}

// Chart.js Registration Required
import { Chart as ChartJS, ArcElement, CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend } from 'chart.js';
ChartJS.register(ArcElement, CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend);

const ReportBuilder: React.FC<ReportBuilderProps> = ({ ... }) => {
  return (
    <Card>
      <CardContent>
        <Typography variant="h6">Report Builder</Typography>
        <Stack spacing={3}>
          {/* Select: Chart Type */}
          {/* Select: Group By Field */}
          {/* Select: Aggregate Field */}
          {/* Select: Aggregate Function */}
          {/* Button: Generate Report */}
        </Stack>
      </CardContent>
    </Card>
  );
};
```

### Example 7: Dashboard Widget Components
Location: `src/components/Dashboard/DashboardWidget.tsx`, `Dashboard.tsx`

```typescript
// DashboardWidget.tsx
export interface WidgetProps {
  id: string;
  type: 'kpi' | 'chart' | 'list' | 'gauge';
  title: string;
  config: any;
  onConfigure?: (id: string) => void;
  onRemove?: (id: string) => void;
}

const DashboardWidget: React.FC<WidgetProps> = ({ type, config, ... }) => {
  const renderContent = () => {
    switch (type) {
      case 'kpi': return <Typography variant="h3">{config.value}</Typography>;
      case 'chart': return <Box>Chart Placeholder</Box>;
      case 'list': return <List>{/* items */}</List>;
      case 'gauge': return <Typography>{config.percentage}%</Typography>;
    }
  };
  
  return (
    <Card>
      <CardHeader title={title} action={/* Settings/Close buttons */} />
      <CardContent>{renderContent()}</CardContent>
    </Card>
  );
};

// Dashboard.tsx with react-grid-layout
import GridLayout from 'react-grid-layout';
import 'react-grid-layout/css/styles.css';
import 'react-resizable/css/styles.css';

interface DashboardProps {
  initialWidgets?: WidgetProps[];
  onLayoutChange?: (layout: any) => void;
}

const Dashboard: React.FC<DashboardProps> = ({ initialWidgets, onLayoutChange }) => {
  const layout = widgets.map((widget, index) => ({
    i: widget.id,
    x: (index % 3) * 4,
    y: Math.floor(index / 3) * 2,
    w: 4,
    h: 2
  }));
  
  return (
    <Box>
      <Button onClick={handleAddWidget}>Add Widget</Button>
      <GridLayout
        layout={layout}
        cols={12}
        rowHeight={150}
        width={1200}
        onLayoutChange={onLayoutChange}
      >
        {widgets.map(widget => (
          <div key={widget.id}>
            <DashboardWidget {...widget} />
          </div>
        ))}
      </GridLayout>
    </Box>
  );
};
```

### Example 8: Aggregate List Page Template
Location: `src/pages/[ModelName]ListPage.tsx`

```typescript
import Sidebar from '@/components/common/Sidebar';
import AdvancedSearch, { Filter, GroupBy } from '@/components/common/AdvancedSearch';
import ExportDialog from '@/components/common/ExportDialog';
import ImportDialog from '@/components/common/ImportDialog';

interface [ModelName] {
  id: string;
  // Add model fields
}

const [ModelName]ListPage: React.FC = () => {
  const [data, setData] = useState<[ModelName][]>([]);
  const [filters, setFilters] = useState<Filter[]>([]);
  const [exportDialogOpen, setExportDialogOpen] = useState(false);
  const [importDialogOpen, setImportDialogOpen] = useState(false);
  
  const availableFields = [
    { name: 'id', label: 'ID', type: 'string' },
    // Add fields
  ];
  
  return (
    <Box sx=\{{ display: 'flex', minHeight: '100vh', width: '100vw' }}>
      <Sidebar />
      <Box sx=\{{ flex: 1, p: 3 }}>
        <Container maxWidth="xl">
          <Typography variant="h4">[ModelName] Management</Typography>
          
          {/* Advanced Search */}
          <AdvancedSearch
            filters={filters}
            onFilterChange={setFilters}
            availableFields={availableFields}
          />
          
          {/* Action Bar */}
          <Toolbar>
            <Button startIcon={<Add />} onClick={handleCreate}>Create New</Button>
            <Button startIcon={<FileDownload />} onClick={() => setExportDialogOpen(true)}>Export</Button>
            <Button startIcon={<FileUpload />} onClick={() => setImportDialogOpen(true)}>Import</Button>
          </Toolbar>
          
          {/* Data Table */}
          <TableContainer component={Paper}>
            <Table>
              <TableHead>{/* Column headers */}</TableHead>
              <TableBody>{/* Data rows with Edit/Delete buttons */}</TableBody>
            </Table>
          </TableContainer>
          
          {/* Dialogs */}
          <ExportDialog open={exportDialogOpen} onClose={...} data={data} availableFields={availableFields} />
          <ImportDialog open={importDialogOpen} onClose={...} onImport={...} availableFields={availableFields} />
        </Container>
      </Box>
    </Box>
  );
};
```

### Example 9: Main Dashboard Page Template
Location: `src/pages/HomePage.tsx`

```typescript
import Sidebar from '@/components/common/Sidebar';
import Dashboard from '@/components/Dashboard/Dashboard';
import DashboardWidget, { WidgetProps } from '@/components/Dashboard/DashboardWidget';
import ReportBuilder, { ReportConfig } from '@/components/Reports/ReportBuilder';

const HomePage: React.FC = () => {
  const kpiWidgets: WidgetProps[] = [
    { id: 'kpi-1', type: 'kpi', title: 'Total Revenue', config: { value: '₩12,500,000' } },
    // Add KPI widgets
  ];
  
  const dashboardWidgets: WidgetProps[] = [
    { id: 'widget-1', type: 'chart', title: 'Sales Overview', config: {} },
    // Add widgets
  ];
  
  const reportFields = [
    { name: 'category', label: 'Category', type: 'string' },
    { name: 'amount', label: 'Amount', type: 'number' },
  ];
  
  return (
    <Box sx=\{{ display: 'flex', minHeight: '100vh', width: '100vw' }}>
      <Sidebar />
      <Box sx=\{{ flex: 1, p: 3 }}>
        <Container maxWidth="xl">
          <Typography variant="h4">Dashboard</Typography>
          
          {/* KPI Section */}
          <Grid container spacing={3}>
            {kpiWidgets.map(widget => (
              <Grid item xs={12} sm={6} md={4} key={widget.id}>
                <DashboardWidget {...widget} />
              </Grid>
            ))}
          </Grid>
          
          <Divider sx=\{{ my: 4 }} />
          
          {/* Dashboard Widgets (Draggable) */}
          <Typography variant="h5">Widgets</Typography>
          <Dashboard initialWidgets={dashboardWidgets} onLayoutChange={...} />
          
          <Divider sx=\{{ my: 4 }} />
          
          {/* Reports Section */}
          <Typography variant="h5">Reports</Typography>
          <Grid container spacing={3}>
            <Grid item xs={12} md={6}>
              <ReportBuilder model="Sales" availableFields={reportFields} onSave={...} />
            </Grid>
            <Grid item xs={12} md={6}>
              <Card>{/* Recent Reports */}</Card>
            </Grid>
          </Grid>
        </Container>
      </Box>
    </Box>
  );
};
```

## Required Dependencies

```json
{
  "dependencies": {
    "xlsx": "^0.18.5",
    "chart.js": "^4.4.0",
    "react-chartjs-2": "^5.2.0",
    "react-grid-layout": "^1.4.4"
  },
  "devDependencies": {
    "@types/react-grid-layout": "^1.3.5"
  }
}
```

## Usage Example

```bash
# Install required dependencies
npm install xlsx chart.js react-chartjs-2 react-grid-layout
npm install -D @types/react-grid-layout
```
