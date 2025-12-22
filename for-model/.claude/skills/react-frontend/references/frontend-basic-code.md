# React Frontend Basic Structure Code Generation Requirements

## Overview
Basic structure and layout example code to be used in React Frontend Architecture document (`.claude/agents/frontend-basic-structure.md`)

## Core Requirements

### 1. Template Type Classification
1. **projectStructure**: Project folder and file structure
2. **configFiles**: Configuration files (package.json, vite.config.ts, tsconfig.json)
3. **layout**: Main layout structure (Full Window Layout)
4. **sidebar**: Sidebar component (fixed and responsive)
5. **routing**: Routing and navigation configuration

### 2. Placeholder Substitution
Replace the following placeholders with actual values:
- `[projectName]`: Project name (e.g., erpmodel, dashboard)
- `[port]`: Development server port (default: 3000)
- `[bcName]`: Bounded Context name (e.g., finance, inventory)

## Generated File Structure

```
/frontend
  /public
    favicon.ico
  /src
    /components
      /common
        Layout.tsx
        Loading.tsx
        Sidebar.tsx
    /pages
      HomePage.tsx
    /hooks
    /services
      /api
        client.ts
        interceptors.ts
        types.ts
    /stores
    /types
      common.types.ts
      api.types.ts
    /utils
      constants.ts
    /theme
      index.ts
    App.tsx
    main.tsx
    index.css
    vite-env.d.ts
  package.json
  vite.config.ts
  tsconfig.json
  .gitignore
```

## Actual Code Generation Examples

### Example 1: Project Structure
Location: `frontend/` (root directory)

**Required Directories:**
- `/public` - Static files
- `/src/components/common` - Common components
- `/src/pages` - Page components
- `/src/services/api` - API related
- `/src/stores` - State management
- `/src/types` - TypeScript types
- `/src/utils` - Utilities
- `/src/theme` - Material-UI theme

### Example 2: package.json
Location: `frontend/package.json`

```json
{
  "name": "[projectName]-frontend",
  "private": true,
  "version": "0.0.1",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx"
  },
  "dependencies": {
    "react": "^18.3.1",
    "react-dom": "^18.3.1",
    "react-router-dom": "^6.22.0",
    "@mui/material": "^5.15.10",
    "@mui/icons-material": "^5.15.10",
    "@emotion/react": "^11.11.3",
    "@emotion/styled": "^11.11.0",
    "axios": "^1.6.7",
    "zustand": "^4.5.0"
  },
  "devDependencies": {
    "@types/react": "^18.3.3",
    "@types/react-dom": "^18.3.0",
    "@vitejs/plugin-react": "^4.3.1",
    "typescript": "^5.5.3",
    "vite": "^5.3.4"
  }
}
```

### Example 3: vite.config.ts
Location: `frontend/vite.config.ts`

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000,
    host: true
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src')
    }
  }
})
```

### Example 4: tsconfig.json
Location: `frontend/tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### Example 5: Full Window Layout
Location: `frontend/src/pages/HomePage.tsx`

```typescript
import React from 'react';
import { Box, Container } from '@mui/material';
import Sidebar from '@/components/common/Sidebar';

const HomePage: React.FC = () => {
  return (
    <Box sx=\{{ display: 'flex', minHeight: '100vh', width: '100vw', m: 0, p: 0 }}>
      <Sidebar />
      <Box sx=\{{ flex: 1, p: 3 }}>
        <Container maxWidth="xl">
          {/* Main Content */}
        </Container>
      </Box>
    </Box>
  );
};

export default HomePage;
```

### Example 6: Sidebar Component (Desktop Fixed)
Location: `frontend/src/components/common/Sidebar.tsx`

```typescript
import React from 'react';
import { useNavigate, useLocation } from 'react-router-dom';
import {
  Drawer,
  Box,
  List,
  ListItem,
  ListItemIcon,
  ListItemText,
  Typography
} from '@mui/material';
import { Dashboard } from '@mui/icons-material';

const Sidebar: React.FC = () => {
  const navigate = useNavigate();
  const location = useLocation();

  return (
    <Drawer
      variant="permanent"
      sx=\{{
        width: 240,
        flexShrink: 0,
        '& .MuiDrawer-paper': {
          width: 240,
          boxSizing: 'border-box'
        }
      }}
    >
      <Box
        sx=\{{
          p: 2,
          cursor: 'pointer',
          '&:hover': {
            backgroundColor: 'rgba(0, 0, 0, 0.04)'
          },
          borderRadius: 1,
          mx: 1
        }}
        onClick={() => navigate('/')}
      >
        <Typography variant="h6" color="primary">
          [projectName]
        </Typography>
      </Box>
      <List>
        <ListItem
          button
          selected={location.pathname === '/dashboard'}
          onClick={() => navigate('/dashboard')}
        >
          <ListItemIcon>
            <Dashboard />
          </ListItemIcon>
          <ListItemText primary="Dashboard" />
        </ListItem>
      </List>
    </Drawer>
  );
};

export default Sidebar;
```

### Example 7: Responsive Sidebar (Mobile + Desktop)
Location: `frontend/src/components/common/Sidebar.tsx` (responsive version)

```typescript
import React, { useState } from 'react';
import { useNavigate, useLocation } from 'react-router-dom';
import {
  Drawer,
  Box,
  List,
  ListItem,
  ListItemIcon,
  ListItemText,
  Typography,
  IconButton,
  useMediaQuery,
  useTheme
} from '@mui/material';
import { Dashboard, Menu } from '@mui/icons-material';

const Sidebar: React.FC = () => {
  const [mobileOpen, setMobileOpen] = useState(false);
  const navigate = useNavigate();
  const location = useLocation();
  const theme = useTheme();
  const isMobile = useMediaQuery(theme.breakpoints.down('md'));

  const handleDrawerToggle = () => {
    setMobileOpen(!mobileOpen);
  };

  const drawerContent = (
    <>
      <Box
        sx=\{{
          p: 2,
          cursor: 'pointer',
          '&:hover': { backgroundColor: 'rgba(0, 0, 0, 0.04)' },
          borderRadius: 1,
          mx: 1
        }}
        onClick={() => navigate('/')}
      >
        <Typography variant="h6" color="primary">
          [projectName]
        </Typography>
      </Box>
      <List>
        <ListItem
          button
          selected={location.pathname === '/dashboard'}
          onClick={() => navigate('/dashboard')}
        >
          <ListItemIcon>
            <Dashboard />
          </ListItemIcon>
          <ListItemText primary="Dashboard" />
        </ListItem>
      </List>
    </>
  );

  return (
    <>
      {isMobile && (
        <IconButton
          color="inherit"
          edge="start"
          onClick={handleDrawerToggle}
          sx=\{{ position: 'fixed', top: 16, left: 16, zIndex: 1201 }}
        >
          <Menu />
        </IconButton>
      )}

      {/* Mobile Drawer */}
      <Drawer
        variant="temporary"
        open={mobileOpen}
        onClose={handleDrawerToggle}
        ModalProps=\{{ keepMounted: true }}
        sx=\{{
          display: { xs: 'block', md: 'none' },
          '& .MuiDrawer-paper': { width: 240 }
        }}
      >
        {drawerContent}
      </Drawer>

      {/* Desktop Drawer */}
      <Drawer
        variant="permanent"
        sx=\{{
          display: { xs: 'none', md: 'block' },
          width: 240,
          '& .MuiDrawer-paper': { width: 240, boxSizing: 'border-box' }
        }}
      >
        {drawerContent}
      </Drawer>
    </>
  );
};

export default Sidebar;
```

### Example 8: App.tsx (React Router Setup)
Location: `frontend/src/App.tsx`

```typescript
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import { ThemeProvider } from '@mui/material/styles';
import CssBaseline from '@mui/material/CssBaseline';
import theme from '@/theme';
import HomePage from '@/pages/HomePage';

const App: React.FC = () => {
  return (
    <ThemeProvider theme={theme}>
      <CssBaseline />
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/dashboard" element={<HomePage />} />
        </Routes>
      </BrowserRouter>
    </ThemeProvider>
  );
};

export default App;
```

### Example 9: Material-UI Theme Setup
Location: `frontend/src/theme/index.ts`

```typescript
import { createTheme } from '@mui/material/styles';

const theme = createTheme({
  palette: {
    primary: {
      main: '#1976d2'
    },
    secondary: {
      main: '#dc004e'
    }
  },
  typography: {
    fontFamily: [
      '-apple-system',
      'BlinkMacSystemFont',
      '"Segoe UI"',
      'Roboto',
      '"Helvetica Neue"',
      'Arial',
      'sans-serif'
    ].join(',')
  },
  components: {
    MuiButton: {
      styleOverrides: {
        root: {
          textTransform: 'none'
        }
      }
    }
  }
});

export default theme;
```

### Example 10: main.tsx (Entry Point)
Location: `frontend/src/main.tsx`

```typescript
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

### Example 11: index.css (Global Styles)
Location: `frontend/src/index.css`

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html,
body,
#root {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto',
    'Oxygen', 'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans',
    'Helvetica Neue', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

### Example 12: Route Card System
Location: `frontend/src/pages/HomePage.tsx` (card-based navigation)

```typescript
import React from 'react';
import { useNavigate } from 'react-router-dom';
import {
  Box,
  Container,
  Grid,
  Card,
  CardContent,
  CardActions,
  Typography,
  Button
} from '@mui/material';
import Sidebar from '@/components/common/Sidebar';

interface RouteCard {
  title: string;
  description: string;
  path: string;
  icon: React.ReactNode;
}

const HomePage: React.FC = () => {
  const navigate = useNavigate();

  const routes: RouteCard[] = [
    {
      title: 'Dashboard',
      description: 'View analytics and reports',
      path: '/dashboard',
      icon: null
    }
  ];

  return (
    <Box sx=\{{ display: 'flex', minHeight: '100vh', width: '100vw' }}>
      <Sidebar />
      <Box sx=\{{ flex: 1, p: 3 }}>
        <Container maxWidth="xl">
          <Typography variant="h4" sx=\{{ mb: 4 }}>
            Welcome to [projectName]
          </Typography>
          <Grid container spacing={3}>
            {routes.map((route) => (
              <Grid item xs={12} sm={6} md={4} lg={3} key={route.path}>
                <Card
                  sx=\{{
                    height: '100%',
                    display: 'flex',
                    flexDirection: 'column',
                    cursor: 'pointer',
                    '&:hover': {
                      boxShadow: 6
                    }
                  }}
                  onClick={() => navigate(route.path)}
                >
                  <CardContent sx=\{{ flexGrow: 1 }}>
                    <Typography variant="h6" gutterBottom>
                      {route.title}
                    </Typography>
                    <Typography variant="body2" color="text.secondary">
                      {route.description}
                    </Typography>
                  </CardContent>
                  <CardActions>
                    <Button size="small">Open</Button>
                  </CardActions>
                </Card>
              </Grid>
            ))}
          </Grid>
        </Container>
      </Box>
    </Box>
  );
};

export default HomePage;
```

## Usage Example

```bash
# Create project
npm create vite@latest frontend -- --template react-ts

# Install dependencies
cd frontend
npm install

# Run development server
npm run dev
```

