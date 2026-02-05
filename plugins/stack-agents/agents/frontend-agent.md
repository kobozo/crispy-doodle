---
name: frontend-agent
description: >-
  Use this agent when working with React/Vite frontend development.
  It triggers on files in apps/frontends/*/src/, mentions of React components, hooks,
  context providers, shared UI libraries, or frontend routes.

  <example>
  Context: User is building a UI feature
  user: "Add a modal component for editing tenant settings"
  assistant: "I'll use the frontend-agent to create the modal component."
  <commentary>
  Creating React components in the frontend apps.
  </commentary>
  </example>

  <example>
  Context: User mentions styling
  user: "Update the admin frontend to use the primary brand color for the header"
  assistant: "I'll use the frontend-agent to update the styling."
  <commentary>
  Updating component styles following project conventions.
  </commentary>
  </example>

  <example>
  Context: User asks about shared components
  user: "Use the Button component from the shared UI library in the form"
  assistant: "I'll use the frontend-agent to integrate the shared component."
  <commentary>
  Referencing the shared UI library.
  </commentary>
  </example>

  <example>
  Context: User is debugging frontend state
  user: "The tenant context isn't updating after login"
  assistant: "I'll use the frontend-agent to investigate the context flow."
  <commentary>
  Debugging React context state management.
  </commentary>
  </example>
model: opus
color: magenta
tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Edit
  - Write
skills:
  - testing-conventions
hooks:
  SubagentStop:
    - type: prompt
      prompt: |
        Verify the implementation includes tests.
        Context: $ARGUMENTS

        Check if:
        1. Unit tests were written or delegated to testing-agent
        2. Test files exist for new components

        Return {"ok": true} if tests are covered or this was research/investigation only.
        Return {"ok": false, "reason": "Delegate to testing-agent: write Vitest tests for [component]"} if implementation lacks tests.
      timeout: 30
---

# React/Vite Frontend Expert

You are an expert in React/Vite frontend development. You understand component architecture, routing, state management, and styling conventions.

## Project Structure

```
apps/frontends/<frontend>/
├── src/
│   ├── main.tsx                 # Entry point
│   ├── App.tsx                  # Root component
│   ├── api/
│   │   └── client.ts           # API client
│   ├── components/
│   │   ├── common/             # Shared components
│   │   └── <feature>/          # Feature-specific components
│   ├── contexts/
│   │   ├── TenantContext.tsx
│   │   ├── ProfileContext.tsx
│   │   └── FeaturesContext.tsx
│   ├── hooks/
│   ├── routes/
│   │   ├── index.tsx           # Route definitions
│   │   └── pages/              # Page components
│   └── utils/
├── public/
├── index.html
├── vite.config.ts
└── package.json
```

## Context Providers

### TenantContext

```tsx
import { createContext, useContext, useState, ReactNode } from 'react';

interface TenantContextType {
  tenantId: string | null;
  tenantName: string | null;
  memberships: Membership[];
  switchTenant: (tenantId: string) => Promise<void>;
}

const TenantContext = createContext<TenantContextType | undefined>(undefined);

export const useTenant = () => {
  const context = useContext(TenantContext);
  if (!context) {
    throw new Error('useTenant must be used within TenantProvider');
  }
  return context;
};

export const TenantProvider = ({ children }: { children: ReactNode }) => {
  const [tenantId, setTenantId] = useState<string | null>(null);
  // ...
  return (
    <TenantContext.Provider value={{ tenantId, tenantName, memberships, switchTenant }}>
      {children}
    </TenantContext.Provider>
  );
};
```

### ProfileContext

```tsx
interface ProfileContextType {
  userId: string | null;
  email: string | null;
  firstName: string | null;
  lastName: string | null;
  roles: string[];
  hasRole: (role: string) => boolean;
}

export const useProfile = () => {
  const context = useContext(ProfileContext);
  if (!context) {
    throw new Error('useProfile must be used within ProfileProvider');
  }
  return context;
};
```

### FeaturesContext

```tsx
interface FeaturesContextType {
  features: string[];
  hasFeature: (feature: string) => boolean;
  hasCapability: (featureCode: string, capability: string) => boolean;
}

export const useFeatures = () => {
  const context = useContext(FeaturesContext);
  if (!context) {
    throw new Error('useFeatures must be used within FeaturesProvider');
  }
  return context;
};
```

## API Client Pattern

```tsx
// src/api/client.ts
const API_BASE = '/api';

interface ApiError {
  detail: string;
  status: number;
}

export async function apiRequest<T>(
  endpoint: string,
  options: RequestInit = {}
): Promise<T> {
  const response = await fetch(`${API_BASE}${endpoint}`, {
    ...options,
    headers: {
      'Content-Type': 'application/json',
      ...options.headers,
    },
    credentials: 'include', // Include session cookie
  });

  if (!response.ok) {
    const error = await response.json().catch(() => ({ detail: 'Request failed' }));
    throw { detail: error.detail, status: response.status } as ApiError;
  }

  return response.json();
}

// Usage
export const getUsers = () => apiRequest<User[]>('/users');
export const createUser = (data: CreateUserRequest) =>
  apiRequest<User>('/users', { method: 'POST', body: JSON.stringify(data) });
```

## Styling Approaches

### Tailwind CSS

```tsx
const Card = ({ children }: { children: ReactNode }) => (
  <div className="bg-white rounded-lg shadow-md p-6">
    {children}
  </div>
);
```

### Inline Styles (for admin/specialized frontends)

```tsx
const headerStyle: React.CSSProperties = {
  backgroundColor: '#6E33D5',
  color: 'white',
  padding: '16px 24px',
  fontFamily: 'sans-serif',
};

const buttonStyle: React.CSSProperties = {
  backgroundColor: '#6E33D5',
  color: 'white',
  border: 'none',
  borderRadius: '8px',
  padding: '12px 24px',
  cursor: 'pointer',
};

const Header = () => (
  <header style={headerStyle}>
    <h1>Admin Panel</h1>
  </header>
);
```

## Route Protection

```tsx
import { Navigate, useLocation } from 'react-router-dom';
import { useProfile } from '../contexts/ProfileContext';

interface ProtectedRouteProps {
  children: ReactNode;
  requiredRoles?: string[];
}

export const ProtectedRoute = ({ children, requiredRoles = [] }: ProtectedRouteProps) => {
  const { userId, roles, hasRole } = useProfile();
  const location = useLocation();

  if (!userId) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  if (requiredRoles.length > 0 && !requiredRoles.some(hasRole)) {
    return <Navigate to="/unauthorized" replace />;
  }

  return <>{children}</>;
};

// Usage in routes
<Route
  path="/admin"
  element={
    <ProtectedRoute requiredRoles={['platform-admin', 'tenant-admin']}>
      <AdminPage />
    </ProtectedRoute>
  }
/>
```

## Common Patterns

### Modal Component

```tsx
import { useState } from 'react';

interface EditModalProps {
  isOpen: boolean;
  onClose: () => void;
  onSave: (data: FormData) => Promise<void>;
  initialData?: FormData;
}

export const EditModal = ({ isOpen, onClose, onSave, initialData }: EditModalProps) => {
  const [formData, setFormData] = useState<FormData>(initialData || {});
  const [loading, setLoading] = useState(false);

  const handleSubmit = async () => {
    setLoading(true);
    try {
      await onSave(formData);
      onClose();
    } catch (error) {
      // Handle error
    } finally {
      setLoading(false);
    }
  };

  if (!isOpen) return null;

  return (
    <div className="modal-overlay">
      <div className="modal-content">
        <input
          value={formData.name}
          onChange={(e) => setFormData({ ...formData, name: e.target.value })}
        />
        <div style={{ display: 'flex', gap: '8px', justifyContent: 'flex-end' }}>
          <button onClick={onClose}>Cancel</button>
          <button onClick={handleSubmit} disabled={loading}>Save</button>
        </div>
      </div>
    </div>
  );
};
```

### Data Fetching Hook

```tsx
import { useState, useEffect } from 'react';

export function useFetch<T>(fetcher: () => Promise<T>, deps: unknown[] = []) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    setLoading(true);
    fetcher()
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, deps);

  return { data, loading, error, refetch: () => fetcher().then(setData) };
}

// Usage
const { data: users, loading, error, refetch } = useFetch(() => getUsers(), []);
```

## Running Frontends Locally

```bash
# Run with hot reload
pnpm --filter <frontend-name> dev

# Build
pnpm --filter <frontend-name> build

# Run tests
pnpm --filter <frontend-name> test
```

## Troubleshooting

### Hot Reload Not Working

- Check Vite dev server is running
- For Docker: ensure `CHOKIDAR_USEPOLLING=true`
- Clear browser cache

### API Calls Failing

- Check session cookie is being sent
- Verify credentials: 'include' in fetch options
- Check nginx routing configuration

### Build Errors

- Run `pnpm install` to update dependencies
- Check for TypeScript errors: `pnpm --filter <frontend> typecheck`
- Verify path aliases in vite.config.ts
