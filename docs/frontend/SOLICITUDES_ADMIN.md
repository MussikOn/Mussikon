# 🎯 **SISTEMA DE SOLICITUDES ADMIN - FRONTEND MUSSIKON**

## 📋 **Índice del Sistema de Solicitudes Admin**

### **🏗️ Arquitectura del Sistema**
- [Componentes Principales](./SOLICITUDES_ADMIN.md#componentes-principales) - Estructura de componentes
- [Pantallas del Sistema](./SOLICITUDES_ADMIN.md#pantallas-del-sistema) - Flujo de navegación
- [Estado y Gestión](./SOLICITUDES_ADMIN.md#estado-y-gestión) - Zustand y React Query

### **🔧 Implementación Técnica**
- [Servicios y APIs](./SOLICITUDES_ADMIN.md#servicios-y-apis) - Comunicación con backend
- [Validaciones](./SOLICITUDES_ADMIN.md#validaciones) - Formularios y validaciones
- [Testing](./SOLICITUDES_ADMIN.md#testing) - Estrategia de testing

---

## 🎯 **Propósito del Sistema de Solicitudes Admin**

### **Objetivo Principal**
Implementar la funcionalidad completa del sistema de administración de solicitudes de músicos en el panel de administración Next.js, permitiendo a administradores y moderadores gestionar, revisar y supervisar todas las solicitudes del sistema.

### **Funcionalidades Clave**
- **Dashboard de Solicitudes**: Vista general del estado del sistema
- **Gestión de Solicitudes**: Aprobación, rechazo y moderación
- **Análisis y Reportes**: Métricas y estadísticas del sistema
- **Gestión de Usuarios**: Control de organizadores y músicos
- **Sistema de Moderación**: Herramientas para mantener la calidad

---

## 🏗️ **Componentes Principales**

### **📊 Dashboard de Solicitudes**
```typescript
// components/dashboard/RequestsDashboard.tsx
import React from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';
import { 
  BarChart, 
  Bar, 
  XAxis, 
  YAxis, 
  CartesianGrid, 
  Tooltip, 
  ResponsiveContainer,
  PieChart,
  Pie,
  Cell
} from 'recharts';
import { useRequestsStats } from '@/hooks/useRequestsStats';
import { colors } from '@/lib/constants';

export const RequestsDashboard = () => {
  const { stats, isLoading, error } = useRequestsStats();

  if (isLoading) return <div>Cargando estadísticas...</div>;
  if (error) return <div>Error al cargar estadísticas</div>;

  const statusColors = {
    open: colors.success[500],
    in_review: colors.warning[500],
    assigned: colors.info[500],
    completed: colors.success[600],
    cancelled: colors.error[500],
  };

  const chartData = Object.entries(stats.byStatus).map(([status, count]) => ({
    status: status.charAt(0).toUpperCase() + status.slice(1),
    count,
    color: statusColors[status as keyof typeof statusColors],
  }));

  return (
    <div className="space-y-6">
      {/* Stats Cards */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
        <Card>
          <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
            <CardTitle className="text-sm font-medium">Total Solicitudes</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold">{stats.total}</div>
            <p className="text-xs text-muted-foreground">
              +{stats.newThisWeek} esta semana
            </p>
          </CardContent>
        </Card>

        <Card>
          <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
            <CardTitle className="text-sm font-medium">Pendientes</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold text-warning-600">
              {stats.pending}
            </div>
            <p className="text-xs text-muted-foreground">
              Requieren revisión
            </p>
          </CardContent>
        </Card>

        <Card>
          <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
            <CardTitle className="text-sm font-medium">Completadas</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold text-success-600">
              {stats.completed}
            </div>
            <p className="text-xs text-muted-foreground">
              {stats.completionRate}% tasa de éxito
            </p>
          </CardContent>
        </Card>

        <Card>
          <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
            <CardTitle className="text-sm font-medium">Usuarios Activos</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold">{stats.activeUsers}</div>
            <p className="text-xs text-muted-foreground">
              +{stats.newUsers} nuevos
            </p>
          </CardContent>
        </Card>
      </div>

      {/* Charts */}
      <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <Card>
          <CardHeader>
            <CardTitle>Solicitudes por Estado</CardTitle>
          </CardHeader>
          <CardContent>
            <ResponsiveContainer width="100%" height={300}>
              <BarChart data={chartData}>
                <CartesianGrid strokeDasharray="3 3" />
                <XAxis dataKey="status" />
                <YAxis />
                <Tooltip />
                <Bar dataKey="count" fill={colors.primary[500]} />
              </BarChart>
            </ResponsiveContainer>
          </CardContent>
        </Card>

        <Card>
          <CardHeader>
            <CardTitle>Distribución por Estado</CardTitle>
          </CardHeader>
          <CardContent>
            <ResponsiveContainer width="100%" height={300}>
              <PieChart>
                <Pie
                  data={chartData}
                  cx="50%"
                  cy="50%"
                  labelLine={false}
                  label={({ status, percent }) => 
                    `${status} ${(percent * 100).toFixed(0)}%`
                  }
                  outerRadius={80}
                  fill="#8884d8"
                  dataKey="count"
                >
                  {chartData.map((entry, index) => (
                    <Cell key={`cell-${index}`} fill={entry.color} />
                  ))}
                </Pie>
                <Tooltip />
              </PieChart>
            </ResponsiveContainer>
          </CardContent>
        </Card>
      </div>

      {/* Recent Activity */}
      <Card>
        <CardHeader>
          <CardTitle>Actividad Reciente</CardTitle>
        </CardHeader>
        <CardContent>
          <div className="space-y-4">
            {stats.recentActivity.map((activity, index) => (
              <div key={index} className="flex items-center space-x-4">
                <Badge variant={activity.type === 'created' ? 'default' : 'secondary'}>
                  {activity.type}
                </Badge>
                <div className="flex-1">
                  <p className="text-sm font-medium">{activity.description}</p>
                  <p className="text-xs text-muted-foreground">
                    {new Date(activity.timestamp).toLocaleString()}
                  </p>
                </div>
                <Button variant="ghost" size="sm">
                  Ver
                </Button>
              </div>
            ))}
          </div>
        </CardContent>
      </Card>
    </div>
  );
};
```

### **📋 Tabla de Solicitudes**
```typescript
// components/requests/RequestsTable.tsx
import React, { useState, useMemo } from 'react';
import {
  useReactTable,
  getCoreRowModel,
  getFilteredRowModel,
  getPaginationRowModel,
  getSortedRowModel,
  flexRender,
  createColumnHelper,
} from '@tanstack/react-table';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Badge } from '@/components/ui/badge';
import { DropdownMenu, DropdownMenuContent, DropdownMenuItem, DropdownMenuTrigger } from '@/components/ui/dropdown-menu';
import { MoreHorizontal, Eye, Edit, Trash2, CheckCircle, XCircle } from 'lucide-react';
import { useRequests } from '@/hooks/useRequests';
import { MusicianRequest } from '@/types/api';

const columnHelper = createColumnHelper<MusicianRequest>();

export const RequestsTable = () => {
  const { requests, updateRequest, deleteRequest, isLoading } = useRequests();
  const [globalFilter, setGlobalFilter] = useState('');

  const columns = useMemo(
    () => [
      columnHelper.accessor('title', {
        header: 'Título',
        cell: ({ row }) => (
          <div className="max-w-[200px] truncate" title={row.original.title}>
            {row.original.title}
          </div>
        ),
      }),
      columnHelper.accessor('organizer.name', {
        header: 'Organizador',
        cell: ({ row }) => row.original.organizer?.name || 'N/A',
      }),
      columnHelper.accessor('instrument', {
        header: 'Instrumento',
        cell: ({ row }) => (
          <Badge variant="outline">{row.original.instrument}</Badge>
        ),
      }),
      columnHelper.accessor('budget_min', {
        header: 'Presupuesto',
        cell: ({ row }) => (
          <span>
            ${row.original.budget_min} - ${row.original.budget_max}
          </span>
        ),
      }),
      columnHelper.accessor('status', {
        header: 'Estado',
        cell: ({ row }) => {
          const status = row.original.status;
          const variants = {
            open: 'default',
            in_review: 'secondary',
            assigned: 'outline',
            completed: 'default',
            cancelled: 'destructive',
          };
          return (
            <Badge variant={variants[status as keyof typeof variants]}>
              {status.replace('_', ' ').toUpperCase()}
            </Badge>
          );
        },
      }),
      columnHelper.accessor('created_at', {
        header: 'Fecha',
        cell: ({ row }) => 
          new Date(row.original.created_at).toLocaleDateString('es-ES'),
      }),
      columnHelper.display({
        id: 'actions',
        header: 'Acciones',
        cell: ({ row }) => (
          <DropdownMenu>
            <DropdownMenuTrigger asChild>
              <Button variant="ghost" className="h-8 w-8 p-0">
                <MoreHorizontal className="h-4 w-4" />
              </Button>
            </DropdownMenuTrigger>
            <DropdownMenuContent align="end">
              <DropdownMenuItem onClick={() => handleView(row.original)}>
                <Eye className="mr-2 h-4 w-4" />
                Ver
              </DropdownMenuItem>
              <DropdownMenuItem onClick={() => handleEdit(row.original)}>
                <Edit className="mr-2 h-4 w-4" />
                Editar
              </DropdownMenuItem>
              {row.original.status === 'open' && (
                <DropdownMenuItem onClick={() => handleApprove(row.original)}>
                  <CheckCircle className="mr-2 h-4 w-4" />
                  Aprobar
                </DropdownMenuItem>
              )}
              <DropdownMenuItem onClick={() => handleDelete(row.original)}>
                <Trash2 className="mr-2 h-4 w-4" />
                Eliminar
              </DropdownMenuItem>
            </DropdownMenuContent>
          </DropdownMenu>
        ),
      }),
    ],
    []
  );

  const table = useReactTable({
    data: requests,
    columns,
    getCoreRowModel: getCoreRowModel(),
    getFilteredRowModel: getFilteredRowModel(),
    getPaginationRowModel: getPaginationRowModel(),
    getSortedRowModel: getSortedRowModel(),
    state: {
      globalFilter,
    },
    onGlobalFilterChange: setGlobalFilter,
  });

  const handleView = (request: MusicianRequest) => {
    // Navigate to request detail
  };

  const handleEdit = (request: MusicianRequest) => {
    // Navigate to edit form
  };

  const handleApprove = async (request: MusicianRequest) => {
    try {
      await updateRequest(request.id, { status: 'approved' });
    } catch (error) {
      console.error('Error approving request:', error);
    }
  };

  const handleDelete = async (request: MusicianRequest) => {
    if (confirm('¿Estás seguro de que quieres eliminar esta solicitud?')) {
      try {
        await deleteRequest(request.id);
      } catch (error) {
        console.error('Error deleting request:', error);
      }
    }
  };

  if (isLoading) return <div>Cargando solicitudes...</div>;

  return (
    <div className="space-y-4">
      <div className="flex items-center justify-between">
        <Input
          placeholder="Buscar solicitudes..."
          value={globalFilter ?? ''}
          onChange={(event) => setGlobalFilter(event.target.value)}
          className="max-w-sm"
        />
        <Button>Nueva Solicitud</Button>
      </div>

      <div className="rounded-md border">
        <table className="w-full">
          <thead className="border-b bg-muted/50">
            {table.getHeaderGroups().map((headerGroup) => (
              <tr key={headerGroup.id}>
                {headerGroup.headers.map((header) => (
                  <th key={header.id} className="p-2 text-left">
                    {header.isPlaceholder
                      ? null
                      : flexRender(
                          header.column.columnDef.header,
                          header.getContext()
                        )}
                  </th>
                ))}
              </tr>
            ))}
          </thead>
          <tbody>
            {table.getRowModel().rows.map((row) => (
              <tr key={row.id} className="border-b">
                {row.getVisibleCells().map((cell) => (
                  <td key={cell.id} className="p-2">
                    {flexRender(cell.column.columnDef.cell, cell.getContext())}
                  </td>
                ))}
              </tr>
            ))}
          </tbody>
        </table>
      </div>

      <div className="flex items-center justify-between">
        <div className="text-sm text-muted-foreground">
          {table.getFilteredSelectedRowModel().rows.length} de{' '}
          {table.getFilteredRowModel().rows.length} fila(s) seleccionada(s).
        </div>
        <div className="flex items-center space-x-2">
          <Button
            variant="outline"
            size="sm"
            onClick={() => table.previousPage()}
            disabled={!table.getCanPreviousPage()}
          >
            Anterior
          </Button>
          <Button
            variant="outline"
            size="sm"
            onClick={() => table.nextPage()}
            disabled={!table.getCanNextPage()}
          >
            Siguiente
          </Button>
        </div>
      </div>
    </div>
  );
};
```

---

## 📱 **Pantallas del Sistema**

### **🏠 Pantalla Principal de Administración**
```typescript
// pages/admin/requests/index.tsx
import React from 'react';
import { NextPage } from 'next';
import { AdminLayout } from '@/components/layouts/AdminLayout';
import { RequestsDashboard } from '@/components/dashboard/RequestsDashboard';
import { RequestsTable } from '@/components/requests/RequestsTable';
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs';

const AdminRequestsPage: NextPage = () => {
  return (
    <AdminLayout>
      <div className="space-y-6">
        <div>
          <h1 className="text-3xl font-bold tracking-tight">
            Administración de Solicitudes
          </h1>
          <p className="text-muted-foreground">
            Gestiona y supervisa todas las solicitudes del sistema
          </p>
        </div>

        <Tabs defaultValue="dashboard" className="space-y-4">
          <TabsList>
            <TabsTrigger value="dashboard">Dashboard</TabsTrigger>
            <TabsTrigger value="requests">Solicitudes</TabsTrigger>
            <TabsTrigger value="moderation">Moderación</TabsTrigger>
          </TabsList>

          <TabsContent value="dashboard" className="space-y-4">
            <RequestsDashboard />
          </TabsContent>

          <TabsContent value="requests" className="space-y-4">
            <RequestsTable />
          </TabsContent>

          <TabsContent value="moderation" className="space-y-4">
            <div className="text-center py-8">
              <p className="text-muted-foreground">
                Herramientas de moderación próximamente
              </p>
            </div>
          </TabsContent>
        </Tabs>
      </div>
    </AdminLayout>
  );
};

export default AdminRequestsPage;
```

---

## 🔄 **Estado y Gestión**

### **📊 Store de Zustand para Solicitudes**
```typescript
// stores/requestsStore.ts
import { create } from 'zustand';
import { devtools } from 'zustand/middleware';
import { MusicianRequest, CreateRequestData } from '@/types/api';

interface RequestsState {
  requests: MusicianRequest[];
  currentRequest: MusicianRequest | null;
  filters: {
    status: string;
    instrument: string;
    dateRange: string;
    organizer: string;
  };
  pagination: {
    page: number;
    limit: number;
    total: number;
  };
  loading: boolean;
  error: string | null;
  
  // Actions
  setRequests: (requests: MusicianRequest[]) => void;
  setCurrentRequest: (request: MusicianRequest | null) => void;
  setFilters: (filters: Partial<RequestsState['filters']>) => void;
  setPagination: (pagination: Partial<RequestsState['pagination']>) => void;
  setLoading: (loading: boolean) => void;
  setError: (error: string | null) => void;
  
  // Business Logic
  getFilteredRequests: () => MusicianRequest[];
  getRequestById: (id: string) => MusicianRequest | undefined;
  updateRequestInStore: (id: string, updates: Partial<MusicianRequest>) => void;
  removeRequestFromStore: (id: string) => void;
}

export const useRequestsStore = create<RequestsState>()(
  devtools(
    (set, get) => ({
      // Initial State
      requests: [],
      currentRequest: null,
      filters: {
        status: '',
        instrument: '',
        dateRange: '',
        organizer: '',
      },
      pagination: {
        page: 1,
        limit: 20,
        total: 0,
      },
      loading: false,
      error: null,

      // Actions
      setRequests: (requests) => set({ requests }),
      setCurrentRequest: (request) => set({ currentRequest: request }),
      setFilters: (filters) => set((state) => ({
        filters: { ...state.filters, ...filters },
        pagination: { ...state.pagination, page: 1 }, // Reset pagination
      })),
      setPagination: (pagination) => set((state) => ({
        pagination: { ...state.pagination, ...pagination },
      })),
      setLoading: (loading) => set({ loading }),
      setError: (error) => set({ error }),

      // Business Logic
      getFilteredRequests: () => {
        const { requests, filters } = get();
        return requests.filter(request => {
          if (filters.status && request.status !== filters.status) return false;
          if (filters.instrument && request.instrument !== filters.instrument) return false;
          if (filters.organizer && request.organizer.name !== filters.organizer) return false;
          
          if (filters.dateRange) {
            const requestDate = new Date(request.event_date);
            const now = new Date();
            const daysDiff = Math.ceil((requestDate.getTime() - now.getTime()) / (1000 * 60 * 60 * 24));
            
            switch (filters.dateRange) {
              case 'today':
                return daysDiff === 0;
              case 'week':
                return daysDiff >= 0 && daysDiff <= 7;
              case 'month':
                return daysDiff >= 0 && daysDiff <= 30;
              case 'past':
                return daysDiff < 0;
              default:
                return true;
            }
          }
          
          return true;
        });
      },

      getRequestById: (id) => {
        const { requests } = get();
        return requests.find(request => request.id === id);
      },

      updateRequestInStore: (id, updates) => {
        const { requests } = get();
        const updatedRequests = requests.map(request =>
          request.id === id ? { ...request, ...updates } : request
        );
        set({ requests: updatedRequests });
        
        // Update current request if it's the one being updated
        const { currentRequest } = get();
        if (currentRequest?.id === id) {
          set({ currentRequest: { ...currentRequest, ...updates } });
        }
      },

      removeRequestFromStore: (id) => {
        const { requests, currentRequest } = get();
        const filteredRequests = requests.filter(request => request.id !== id);
        set({ requests: filteredRequests });
        
        // Clear current request if it's the one being removed
        if (currentRequest?.id === id) {
          set({ currentRequest: null });
        }
      },
    }),
    {
      name: 'requests-store',
    }
  )
);
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [Next.js Documentation](https://nextjs.org/docs)
- [TanStack Table](https://tanstack.com/table)
- [Zustand](https://zustand-demo.pmnd.rs/)

### **📖 Documentación Relacionada**
- [Arquitectura Admin](./ARQUITECTURA_ADMIN.md)
- [Stack Tecnológico Admin](./STACK_TECNOLOGICO_ADMIN.md)
- [Configuración Admin](./CONFIGURACION_ADMIN.md)

---

*Este sistema de solicitudes admin proporciona una base sólida para la administración y moderación del sistema MussikOn, permitiendo un control completo y eficiente de todas las solicitudes.*
