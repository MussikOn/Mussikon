# 🖥️ **ARQUITECTURA ADMIN - FRONTEND MUSSIKON**

## 📋 **Índice de la Arquitectura Admin**

### **🏗️ Arquitectura General**
- [Patrones de Diseño](./ARQUITECTURA_ADMIN.md#patrones-de-diseño) - Arquitectura de componentes
- [Estructura de Carpetas](./ARQUITECTURA_ADMIN.md#estructura-de-carpetas) - Organización del proyecto
- [Gestión de Estado](./ARQUITECTURA_ADMIN.md#gestión-de-estado) - React Query y Zustand

### **🔧 Implementación Técnica**
- [Navegación](./ARQUITECTURA_ADMIN.md#navegación) - Next.js App Router
- [Comunicación](./ARQUITECTURA_ADMIN.md#comunicación) - API y WebSockets
- [Almacenamiento](./ARQUITECTURA_ADMIN.md#almacenamiento) - Local y remoto

---

## 🎯 **Propósito de la Arquitectura Admin**

### **Objetivo Principal**
Implementar un panel de administración web que permita a administradores y moderadores gestionar usuarios, solicitudes de músicos y monitorear el sistema de manera eficiente, con una interfaz profesional y funcionalidades avanzadas.

### **Principios Clave**
- **Desktop-First**: Diseño optimizado para pantallas grandes
- **Performance**: Aplicación rápida y responsiva
- **Accessibility**: Cumplimiento de estándares WCAG 2.1 AA
- **Responsive**: Adaptación a diferentes tamaños de pantalla
- **Professional**: Interfaz empresarial y confiable

---

## 🏗️ **Patrones de Diseño**

### **🖥️ Arquitectura de Componentes**
```
┌─────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │   Pages     │  │ Components  │  │   Layout            │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    BUSINESS LAYER                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │    Hooks    │  │   Services  │  │   Utils             │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     STATE LAYER                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │ React Query │  │   Zustand   │  │   Local Storage     │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  INFRASTRUCTURE LAYER                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │     API     │  │   WebSocket │  │   Storage           │ │
│  │             │  │             │  │                     │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### **📁 Estructura de Carpetas del Proyecto**
```
MussikOnAdmin/
├── 📁 app/                    # Next.js App Router
│   ├── 📁 (auth)/             # Grupo de rutas de autenticación
│   │   ├── 📁 login/          # Página de login
│   │   │   └── page.tsx
│   │   ├── 📁 register/       # Página de registro
│   │   │   └── page.tsx
│   │   └── 📁 forgot-password/ # Página de recuperación
│   │       └── page.tsx
│   ├── 📁 (dashboard)/        # Grupo de rutas del dashboard
│   │   ├── 📁 dashboard/      # Dashboard principal
│   │   │   └── page.tsx
│   │   ├── 📁 users/          # Gestión de usuarios
│   │   │   ├── page.tsx       # Lista de usuarios
│   │   │   ├── 📁 [id]/       # Usuario específico
│   │   │   │   ├── page.tsx   # Vista de usuario
│   │   │   │   └── 📁 edit/   # Editar usuario
│   │   │   │       └── page.tsx
│   │   │   └── 📁 create/     # Crear usuario
│   │   │       └── page.tsx
│   │   ├── 📁 requests/       # Sistema de solicitudes (CORE)
│   │   │   ├── page.tsx       # Lista de solicitudes
│   │   │   ├── 📁 [id]/       # Solicitud específica
│   │   │   │   ├── page.tsx   # Vista de solicitud
│   │   │   │   └── 📁 edit/   # Editar solicitud
│   │   │   │       └── page.tsx
│   │   │   └── 📁 create/     # Crear solicitud
│   │   │       └── page.tsx
│   │   ├── 📁 analytics/      # Analytics y reportes
│   │   │   ├── page.tsx       # Dashboard de analytics
│   │   │   ├── 📁 users/      # Analytics de usuarios
│   │   │   │   └── page.tsx
│   │   │   ├── 📁 requests/   # Analytics de solicitudes
│   │   │   │   └── page.tsx
│   │   │   └── 📁 revenue/    # Analytics de ingresos
│   │   │       └── page.tsx
│   │   ├── 📁 settings/       # Configuración del sistema
│   │   │   ├── page.tsx       # Configuración general
│   │   │   ├── 📁 security/   # Configuración de seguridad
│   │   │   │   └── page.tsx
│   │   │   ├── 📁 notifications/ # Configuración de notificaciones
│   │   │   │   └── page.tsx
│   │   │   └── 📁 integrations/ # Integraciones externas
│   │   │       └── page.tsx
│   │   └── 📁 reports/        # Reportes y exportaciones
│   │       ├── page.tsx       # Lista de reportes
│   │       ├── 📁 users/      # Reportes de usuarios
│   │       │   └── page.tsx
│   │       └── 📁 requests/   # Reportes de solicitudes
│   │           └── page.tsx
│   ├── 📁 api/                # API Routes de Next.js
│   │   ├── 📁 auth/           # Endpoints de autenticación
│   │   │   ├── login/         # POST /api/auth/login
│   │   │   │   └── route.ts
│   │   │   ├── logout/        # POST /api/auth/logout
│   │   │   │   └── route.ts
│   │   │   └── refresh/       # POST /api/auth/refresh
│   │   │       └── route.ts
│   │   ├── 📁 users/          # Endpoints de usuarios
│   │   │   ├── route.ts       # GET, POST /api/users
│   │   │   └── 📁 [id]/       # GET, PUT, DELETE /api/users/[id]
│   │   │       └── route.ts
│   │   ├── 📁 requests/       # Endpoints de solicitudes
│   │   │   ├── route.ts       # GET, POST /api/requests
│   │   │   └── 📁 [id]/       # GET, PUT, DELETE /api/requests/[id]
│   │   │       └── route.ts
│   │   └── 📁 analytics/      # Endpoints de analytics
│   │       ├── dashboard/     # GET /api/analytics/dashboard
│   │       │   └── route.ts
│   │       ├── users/         # GET /api/analytics/users
│   │       │   └── route.ts
│   │       └── requests/      # GET /api/analytics/requests
│   │           └── route.ts
│   ├── 📁 globals.css         # Estilos globales
│   ├── 📁 layout.tsx          # Layout principal de la aplicación
│   ├── 📁 page.tsx            # Página principal (redirect a dashboard)
│   └── 📁 not-found.tsx       # Página 404 personalizada
├── 📁 src/                    # Código fuente de la aplicación
│   ├── 📁 components/         # Componentes reutilizables
│   │   ├── 📁 ui/             # Componentes de UI base
│   │   │   ├── Button.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Card.tsx
│   │   │   ├── Modal.tsx
│   │   │   ├── Table.tsx
│   │   │   ├── Form.tsx
│   │   │   └── Chart.tsx
│   │   ├── 📁 forms/          # Componentes de formularios
│   │   │   ├── UserForm.tsx
│   │   │   ├── RequestForm.tsx
│   │   │   ├── SettingsForm.tsx
│   │   │   └── SearchForm.tsx
│   │   ├── 📁 business/       # Componentes específicos del negocio
│   │   │   ├── UserCard.tsx
│   │   │   ├── RequestCard.tsx
│   │   │   ├── AnalyticsCard.tsx
│   │   │   └── NotificationCard.tsx
│   │   ├── 📁 layout/         # Componentes de layout
│   │   │   ├── Sidebar.tsx
│   │   │   ├── Header.tsx
│   │   │   ├── Footer.tsx
│   │   │   └── Breadcrumb.tsx
│   │   └── 📁 data/           # Componentes de visualización de datos
│   │       ├── DataTable.tsx
│   │       ├── Chart.tsx
│   │       ├── Stats.tsx
│   │       └── Filter.tsx
│   ├── 📁 hooks/              # Hooks personalizados
│   │   ├── useAuth.ts         # Hook de autenticación
│   │   ├── useUsers.ts        # Hook de gestión de usuarios
│   │   ├── useRequests.ts     # Hook de solicitudes
│   │   ├── useAnalytics.ts    # Hook de analytics
│   │   ├── useNotifications.ts # Hook de notificaciones
│   │   ├── useWebSocket.ts    # Hook de WebSocket
│   │   └── useLocalStorage.ts # Hook de almacenamiento local
│   ├── 📁 services/           # Servicios de la aplicación
│   │   ├── api.ts             # Cliente HTTP configurado
│   │   ├── authService.ts     # Servicio de autenticación
│   │   ├── userService.ts     # Servicio de usuarios
│   │   ├── requestService.ts  # Servicio de solicitudes
│   │   ├── analyticsService.ts # Servicio de analytics
│   │   ├── notificationService.ts # Servicio de notificaciones
│   │   └── storageService.ts  # Servicio de almacenamiento
│   ├── 📁 stores/             # Estado global con Zustand
│   │   ├── authStore.ts       # Estado de autenticación
│   │   ├── uiStore.ts         # Estado de la interfaz
│   │   ├── notificationStore.ts # Estado de notificaciones
│   │   └── settingsStore.ts   # Estado de configuración
│   ├── 📁 utils/              # Utilidades y helpers
│   │   ├── constants.ts       # Constantes de la aplicación
│   │   ├── helpers.ts         # Funciones helper
│   │   ├── validation.ts      # Validaciones
│   │   ├── formatters.ts      # Formateadores de datos
│   │   ├── permissions.ts     # Gestión de permisos
│   │   └── api.ts             # Funciones de API
│   ├── 📁 types/              # Tipos TypeScript
│   │   ├── api.ts             # Tipos de API
│   │   ├── user.ts            # Tipos de usuario
│   │   ├── request.ts         # Tipos de solicitud
│   │   ├── analytics.ts       # Tipos de analytics
│   │   └── common.ts          # Tipos comunes
│   ├── 📁 styles/             # Estilos y CSS
│   │   ├── globals.css        # Estilos globales
│   │   ├── components.css     # Estilos de componentes
│   │   └── variables.css      # Variables CSS
│   └── 📁 lib/                # Librerías y configuraciones
│       ├── auth.ts            # Configuración de autenticación
│       ├── api.ts             # Configuración de API
│       ├── utils.ts           # Utilidades generales
│       └── constants.ts       # Constantes generales
├── 📁 public/                 # Archivos estáticos
│   ├── 📁 images/             # Imágenes
│   ├── 📁 icons/              # Iconos
│   ├── 📁 fonts/              # Fuentes personalizadas
│   └── 📁 favicon.ico         # Favicon
├── 📁 __tests__/              # Tests de la aplicación
│   ├── 📁 components/         # Tests de componentes
│   ├── 📁 pages/              # Tests de páginas
│   ├── 📁 services/           # Tests de servicios
│   └── 📁 utils/              # Tests de utilidades
├── 📁 docs/                   # Documentación del proyecto
├── 📁 .next/                  # Build de Next.js
├── 📁 node_modules/           # Dependencias
├── 📁 package.json            # Dependencias del proyecto
├── 📁 tsconfig.json           # Configuración de TypeScript
├── 📁 tailwind.config.js      # Configuración de Tailwind CSS
├── 📁 next.config.js          # Configuración de Next.js
├── 📁 postcss.config.js       # Configuración de PostCSS
└── 📁 README.md               # Documentación del proyecto
```

---

## 🔧 **Gestión de Estado**

### **📊 Arquitectura de Estado**
- **React Query**: Estado del servidor y cache
- **Zustand**: Estado global de la aplicación
- **Local Storage**: Persistencia de preferencias
- **Session Storage**: Datos temporales de sesión

### **🔄 Flujo de Datos**
```
API Response → React Query → Zustand Store → Component → UI Update
```

### **🖥️ Stores de Zustand**
```typescript
// stores/authStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  token: string | null;
  login: (user: User, token: string) => void;
  logout: () => void;
  updateUser: (user: User) => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      isAuthenticated: false,
      token: null,
      login: (user, token) => set({
        user,
        isAuthenticated: true,
        token,
      }),
      logout: () => set({
        user: null,
        isAuthenticated: false,
        token: null,
      }),
      updateUser: (user) => set({ user }),
    }),
    {
      name: 'auth-storage',
      partialize: (state) => ({ user: state.user, isAuthenticated: state.isAuthenticated }),
    }
  )
);

// stores/uiStore.ts
interface UIState {
  sidebarOpen: boolean;
  theme: 'light' | 'dark';
  sidebarOpen: boolean;
  toggleSidebar: () => void;
  setTheme: (theme: 'light' | 'dark') => void;
}

export const useUIStore = create<UIState>((set) => ({
  sidebarOpen: true,
  theme: 'light',
  toggleSidebar: () => set((state) => ({ sidebarOpen: !state.sidebarOpen })),
  setTheme: (theme) => set({ theme }),
}));
```

---

## 🧭 **Navegación**

### **🖥️ Estructura de Navegación**
```
App Layout
├── Auth Pages (Login, Register, ForgotPassword)
└── Dashboard Layout
    ├── Sidebar Navigation
    │   ├── Dashboard
    │   ├── Users
    │   ├── Requests
    │   ├── Analytics
    │   ├── Settings
    │   └── Reports
    └── Content Area
        ├── Page Header
        ├── Breadcrumbs
        └── Page Content
```

### **🔧 Configuración de Navegación**
```typescript
// components/layout/Sidebar.tsx
const navigation = [
  {
    name: 'Dashboard',
    href: '/dashboard',
    icon: HomeIcon,
    current: pathname === '/dashboard',
  },
  {
    name: 'Users',
    href: '/users',
    icon: UsersIcon,
    current: pathname.startsWith('/users'),
  },
  {
    name: 'Requests',
    href: '/requests',
    icon: DocumentTextIcon,
    current: pathname.startsWith('/requests'),
  },
  {
    name: 'Analytics',
    href: '/analytics',
    icon: ChartBarIcon,
    current: pathname.startsWith('/analytics'),
  },
  {
    name: 'Settings',
    href: '/settings',
    icon: Cog6ToothIcon,
    current: pathname.startsWith('/settings'),
  },
  {
    name: 'Reports',
    href: '/reports',
    icon: DocumentChartBarIcon,
    current: pathname.startsWith('/reports'),
  },
];

export const Sidebar = () => {
  return (
    <nav className="flex flex-1 flex-col">
      <ul role="list" className="flex flex-1 flex-col gap-y-7">
        <li>
          <ul role="list" className="-mx-2 space-y-1">
            {navigation.map((item) => (
              <li key={item.name}>
                <Link
                  href={item.href}
                  className={classNames(
                    item.current
                      ? 'bg-gray-800 text-white'
                      : 'text-gray-400 hover:text-white hover:bg-gray-800',
                    'group flex gap-x-3 rounded-md p-2 text-sm leading-6 font-semibold'
                  )}
                >
                  <item.icon className="h-6 w-6 shrink-0" aria-hidden="true" />
                  {item.name}
                </Link>
              </li>
            ))}
          </ul>
        </li>
      </ul>
    </nav>
  );
};
```

---

## 🌐 **Comunicación**

### **📡 API Client**
```typescript
// services/api.ts
import axios from 'axios';

const api = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Interceptor para agregar token de autenticación
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('authToken');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Interceptor para manejar errores de autenticación
api.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('authToken');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

### **🔌 WebSocket Integration**
```typescript
// services/websocketService.ts
class WebSocketService {
  private ws: WebSocket | null = null;
  private reconnectAttempts = 0;
  private maxReconnectAttempts = 5;

  connect(token: string) {
    this.ws = new WebSocket(`${process.env.NEXT_PUBLIC_WS_URL}?token=${token}`);

    this.ws.onopen = () => {
      console.log('WebSocket connected');
      this.reconnectAttempts = 0;
    };

    this.ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      this.handleMessage(data);
    };

    this.ws.onclose = () => {
      console.log('WebSocket disconnected');
      this.attemptReconnect();
    };

    this.ws.onerror = (error) => {
      console.error('WebSocket error:', error);
    };
  }

  private handleMessage(data: any) {
    switch (data.type) {
      case 'user_update':
        // Actualizar estado de usuario
        break;
      case 'request_update':
        // Actualizar estado de solicitud
        break;
      case 'new_notification':
        // Agregar nueva notificación
        break;
    }
  }

  private attemptReconnect() {
    if (this.reconnectAttempts < this.maxReconnectAttempts) {
      this.reconnectAttempts++;
      setTimeout(() => {
        this.connect(localStorage.getItem('authToken') || '');
      }, 1000 * Math.pow(2, this.reconnectAttempts));
    }
  }

  disconnect() {
    if (this.ws) {
      this.ws.close();
      this.ws = null;
    }
  }

  send(data: any) {
    if (this.ws && this.ws.readyState === WebSocket.OPEN) {
      this.ws.send(JSON.stringify(data));
    }
  }
}

export default new WebSocketService();
```

---

## 💾 **Almacenamiento**

### **🖥️ Estrategia de Almacenamiento**
- **Local Storage**: Preferencias del usuario y datos persistentes
- **Session Storage**: Datos temporales de sesión
- **React Query Cache**: Cache de datos del servidor
- **Zustand Persist**: Persistencia del estado global

### **🔒 Implementación de Persistencia**
```typescript
// stores/notificationStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface NotificationState {
  notifications: Notification[];
  unreadCount: number;
  addNotification: (notification: Notification) => void;
  markAsRead: (id: string) => void;
  markAllAsRead: () => void;
  clearAll: () => void;
}

export const useNotificationStore = create<NotificationState>()(
  persist(
    (set, get) => ({
      notifications: [],
      unreadCount: 0,
      addNotification: (notification) => set((state) => ({
        notifications: [notification, ...state.notifications],
        unreadCount: state.unreadCount + 1,
      })),
      markAsRead: (id) => set((state) => ({
        notifications: state.notifications.map((n) =>
          n.id === id ? { ...n, read: true } : n
        ),
        unreadCount: Math.max(0, state.unreadCount - 1),
      })),
      markAllAsRead: () => set((state) => ({
        notifications: state.notifications.map((n) => ({ ...n, read: true })),
        unreadCount: 0,
      })),
      clearAll: () => set({ notifications: [], unreadCount: 0 }),
    }),
    {
      name: 'notification-storage',
      partialize: (state) => ({ notifications: state.notifications }),
    }
  )
);
```

---

## 🧪 **Testing**

### **📊 Estrategia de Testing**
- **Unit Tests**: Componentes individuales y hooks
- **Integration Tests**: Flujos de usuario completos
- **E2E Tests**: Testing de la aplicación completa
- **Visual Regression Tests**: Capturas de componentes

### **🔧 Herramientas de Testing**
```typescript
// __tests__/components/UserCard.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { UserCard } from '../../src/components/business/UserCard';

describe('UserCard Component', () => {
  const mockUser = {
    id: '1',
    name: 'John Doe',
    email: 'john@example.com',
    role: 'musician',
    status: 'active',
  };

  it('renders user information correctly', () => {
    render(<UserCard user={mockUser} onEdit={() => {}} onDelete={() => {}} />);
    
    expect(screen.getByText('John Doe')).toBeInTheDocument();
    expect(screen.getByText('john@example.com')).toBeInTheDocument();
    expect(screen.getByText('musician')).toBeInTheDocument();
  });

  it('calls onEdit when edit button is clicked', () => {
    const onEditMock = jest.fn();
    render(<UserCard user={mockUser} onEdit={onEditMock} onDelete={() => {}} />);
    
    fireEvent.click(screen.getByText('Edit'));
    expect(onEditMock).toHaveBeenCalledWith(mockUser);
  });
});
```

---

## 🚀 **Performance y Optimización**

### **⚡ Estrategias de Performance**
- **Code Splitting**: División automática del bundle por rutas
- **Image Optimization**: Optimización automática de imágenes con Next.js
- **Lazy Loading**: Carga diferida de componentes
- **Memoization**: React.memo y useMemo para componentes costosos

### **🖥️ Optimizaciones Específicas**
```typescript
// Lazy loading de componentes
const AnalyticsChart = dynamic(() => import('../components/data/AnalyticsChart'), {
  loading: () => <div>Loading chart...</div>,
  ssr: false, // No renderizar en servidor para componentes con Chart.js
});

// Memoización de componentes
const UserTable = React.memo(({ users, onEdit, onDelete }) => {
  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Email</th>
          <th>Role</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        {users.map((user) => (
          <tr key={user.id}>
            <td>{user.name}</td>
            <td>{user.email}</td>
            <td>{user.role}</td>
            <td>
              <button onClick={() => onEdit(user)}>Edit</button>
              <button onClick={() => onDelete(user.id)}>Delete</button>
            </td>
          </tr>
        ))}
      </tbody>
    </table>
  );
});

// Optimización de listas
const UsersList = ({ users }) => {
  const renderUser = useCallback((user) => (
    <UserCard key={user.id} user={user} />
  ), []);

  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      {users.map(renderUser)}
    </div>
  );
};
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [Next.js Documentation](https://nextjs.org/docs)
- [React Documentation](https://react.dev/)
- [Tailwind CSS](https://tailwindcss.com/)
- [Zustand](https://zustand-demo.pmnd.rs/)

### **📖 Documentación Relacionada**
- [Stack Tecnológico Admin](./STACK_TECNOLOGICO_ADMIN.md)
- [Sistema de Solicitudes Admin](./SOLICITUDES_ADMIN.md)
- [Configuración del Entorno Admin](./CONFIGURACION_ADMIN.md)

---

*Esta arquitectura admin proporciona una base sólida para el desarrollo del panel de administración web, siguiendo las mejores prácticas de desarrollo web y patrones de diseño probados.*
