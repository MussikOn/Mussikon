# 🎯 **SISTEMA DE SOLICITUDES MÓVIL - FRONTEND MUSSIKON**

## 📋 **Índice del Sistema de Solicitudes Móvil**

### **🏗️ Arquitectura del Sistema**
- [Componentes Principales](./SOLICITUDES_MOVIL.md#componentes-principales) - Estructura de componentes
- [Pantallas del Sistema](./SOLICITUDES_MOVIL.md#pantallas-del-sistema) - Flujo de navegación
- [Estado y Gestión](./SOLICITUDES_MOVIL.md#estado-y-gestión) - Redux y Context

### **🔧 Implementación Técnica**
- [Servicios y APIs](./SOLICITUDES_MOVIL.md#servicios-y-apis) - Comunicación con backend
- [Validaciones](./SOLICITUDES_MOVIL.md#validaciones) - Formularios y validaciones
- [Testing](./SOLICITUDES_MOVIL.md#testing) - Estrategia de testing

---

## 🎯 **Propósito del Sistema de Solicitudes Móvil**

### **Objetivo Principal**
Implementar la funcionalidad completa del sistema de solicitudes de músicos en la aplicación móvil React Native, permitiendo a organizadores crear solicitudes y a músicos aplicar a ellas de manera eficiente y intuitiva.

### **Funcionalidades Clave**
- **Creación de Solicitudes**: Formularios intuitivos para organizadores
- **Búsqueda y Filtrado**: Sistema avanzado de búsqueda de solicitudes
- **Aplicaciones**: Proceso simplificado para músicos
- **Comunicación en Tiempo Real**: Chat y notificaciones instantáneas
- **Gestión de Estado**: Control completo del flujo de solicitudes

---

## 🏗️ **Componentes Principales**

### **📱 Componente de Solicitud (RequestCard)**
```typescript
// components/business/RequestCard.tsx
import React from 'react';
import { View, Text, TouchableOpacity, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Ionicons } from '@expo/vector-icons';
import { colors, typography, spacing } from '../../theme';

interface RequestCardProps {
  request: MusicianRequest;
  onPress: (request: MusicianRequest) => void;
  onApply?: (request: MusicianRequest) => void;
  showApplyButton?: boolean;
}

export const RequestCard: React.FC<RequestCardProps> = ({
  request,
  onPress,
  onApply,
  showApplyButton = false,
}) => {
  const getStatusColor = (status: string) => {
    switch (status) {
      case 'open': return colors.success[500];
      case 'in_review': return colors.warning[500];
      case 'assigned': return colors.info[500];
      case 'completed': return colors.success[600];
      case 'cancelled': return colors.error[500];
      default: return colors.gray[500];
    }
  };

  const formatBudget = (min: number, max: number) => {
    return `$${min} - $${max}`;
  };

  const formatDate = (date: string) => {
    return new Date(date).toLocaleDateString('es-ES', {
      day: 'numeric',
      month: 'short',
      year: 'numeric',
    });
  };

  return (
    <TouchableOpacity
      style={styles.container}
      onPress={() => onPress(request)}
      activeOpacity={0.8}
    >
      <LinearGradient
        colors={[colors.white, colors.gray[50]]}
        style={styles.gradient}
        start={{ x: 0, y: 0 }}
        end={{ x: 1, y: 1 }}
      >
        <View style={styles.header}>
          <View style={styles.titleContainer}>
            <Text style={styles.title} numberOfLines={2}>
              {request.title}
            </Text>
            <View style={[styles.statusBadge, { backgroundColor: getStatusColor(request.status) }]}>
              <Text style={styles.statusText}>
                {request.status.toUpperCase()}
              </Text>
            </View>
          </View>
        </View>

        <View style={styles.content}>
          <View style={styles.infoRow}>
            <Ionicons name="musical-notes" size={16} color={colors.gray[600]} />
            <Text style={styles.infoText}>{request.instrument}</Text>
          </View>

          <View style={styles.infoRow}>
            <Ionicons name="calendar" size={16} color={colors.gray[600]} />
            <Text style={styles.infoText}>{formatDate(request.event_date)}</Text>
          </View>

          <View style={styles.infoRow}>
            <Ionicons name="cash" size={16} color={colors.gray[600]} />
            <Text style={styles.infoText}>{formatBudget(request.budget_min, request.budget_max)}</Text>
          </View>

          <View style={styles.infoRow}>
            <Ionicons name="location" size={16} color={colors.gray[600]} />
            <Text style={styles.infoText} numberOfLines={1}>
              {request.location.city}, {request.location.state}
            </Text>
          </View>
        </View>

        {showApplyButton && onApply && (
          <TouchableOpacity
            style={styles.applyButton}
            onPress={() => onApply(request)}
            activeOpacity={0.8}
          >
            <LinearGradient
              colors={[colors.primary[500], colors.primary[600]]}
              style={styles.applyButtonGradient}
            >
              <Text style={styles.applyButtonText}>Aplicar</Text>
              <Ionicons name="arrow-forward" size={16} color={colors.white} />
            </LinearGradient>
          </TouchableOpacity>
        )}
      </LinearGradient>
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  container: {
    marginHorizontal: spacing[4],
    marginVertical: spacing[2],
    borderRadius: spacing[3],
    elevation: 3,
    shadowColor: colors.gray[900],
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
  },
  gradient: {
    borderRadius: spacing[3],
    padding: spacing[4],
  },
  header: {
    marginBottom: spacing[3],
  },
  titleContainer: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'flex-start',
  },
  title: {
    ...typography.h3,
    color: colors.gray[900],
    flex: 1,
    marginRight: spacing[3],
  },
  statusBadge: {
    paddingHorizontal: spacing[2],
    paddingVertical: spacing[1],
    borderRadius: spacing[2],
  },
  statusText: {
    ...typography.caption,
    color: colors.white,
    fontWeight: '600',
  },
  content: {
    marginBottom: spacing[3],
  },
  infoRow: {
    flexDirection: 'row',
    alignItems: 'center',
    marginBottom: spacing[2],
  },
  infoText: {
    ...typography.bodyMedium,
    color: colors.gray[700],
    marginLeft: spacing[2],
    flex: 1,
  },
  applyButton: {
    borderRadius: spacing[2],
    overflow: 'hidden',
  },
  applyButtonGradient: {
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'center',
    paddingVertical: spacing[3],
    paddingHorizontal: spacing[4],
  },
  applyButtonText: {
    ...typography.button,
    color: colors.white,
    marginRight: spacing[2],
  },
});
```

### **📝 Formulario de Creación de Solicitud**
```typescript
// components/forms/CreateRequestForm.tsx
import React, { useState } from 'react';
import { View, Text, ScrollView, StyleSheet, Alert } from 'react-native';
import { useForm, Controller } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import { Button } from '../common/Button';
import { Input } from '../common/Input';
import { Select } from '../common/Select';
import { DatePicker } from '../common/DatePicker';
import { LocationPicker } from '../common/LocationPicker';
import { colors, typography, spacing } from '../../theme';

const createRequestSchema = z.object({
  title: z.string().min(5, 'El título debe tener al menos 5 caracteres'),
  description: z.string().min(20, 'La descripción debe tener al menos 20 caracteres'),
  instrument: z.string().min(1, 'Debe seleccionar un instrumento'),
  genre: z.string().optional(),
  budget_min: z.number().min(1, 'El presupuesto mínimo debe ser mayor a 0'),
  budget_max: z.number().min(1, 'El presupuesto máximo debe ser mayor a 0'),
  event_date: z.date().min(new Date(), 'La fecha del evento debe ser futura'),
  duration_hours: z.number().min(1, 'La duración debe ser al menos 1 hora'),
  location: z.object({
    address: z.string().min(1, 'Debe ingresar una dirección'),
    city: z.string().min(1, 'Debe ingresar una ciudad'),
    state: z.string().min(1, 'Debe ingresar un estado'),
    coordinates: z.object({
      latitude: z.number(),
      longitude: z.number(),
    }),
  }),
  requirements: z.array(z.string()).optional(),
});

type CreateRequestFormData = z.infer<typeof createRequestSchema>;

interface CreateRequestFormProps {
  onSubmit: (data: CreateRequestFormData) => void;
  isLoading?: boolean;
}

export const CreateRequestForm: React.FC<CreateRequestFormProps> = ({
  onSubmit,
  isLoading = false,
}) => {
  const [selectedRequirements, setSelectedRequirements] = useState<string[]>([]);

  const {
    control,
    handleSubmit,
    formState: { errors, isValid },
    watch,
    setValue,
  } = useForm<CreateRequestFormData>({
    resolver: zodResolver(createRequestSchema),
    mode: 'onChange',
  });

  const budgetMin = watch('budget_min');
  const budgetMax = watch('budup_max');

  const validateBudget = () => {
    if (budgetMin && budgetMax && budgetMin > budgetMax) {
      return 'El presupuesto mínimo no puede ser mayor al máximo';
    }
    return true;
  };

  const handleFormSubmit = (data: CreateRequestFormData) => {
    if (validateBudget() !== true) {
      Alert.alert('Error', validateBudget() as string);
      return;
    }

    const formData = {
      ...data,
      requirements: selectedRequirements,
    };

    onSubmit(formData);
  };

  const requirementOptions = [
    'Experiencia mínima de 2 años',
    'Disponibilidad para ensayos',
    'Equipo propio',
    'Transporte propio',
    'Flexibilidad de horarios',
    'Conocimiento de música clásica',
    'Experiencia en eventos corporativos',
  ];

  return (
    <ScrollView style={styles.container} showsVerticalScrollIndicator={false}>
      <Text style={styles.title}>Crear Nueva Solicitud</Text>
      <Text style={styles.subtitle}>
        Completa los detalles para encontrar al músico perfecto
      </Text>

      <View style={styles.form}>
        <Controller
          control={control}
          name="title"
          render={({ field: { onChange, onBlur, value } }) => (
            <Input
              label="Título de la Solicitud"
              placeholder="Ej: Músico para boda en la playa"
              value={value}
              onChangeText={onChange}
              onBlur={onBlur}
              error={errors.title?.message}
              required
            />
          )}
        />

        <Controller
          control={control}
          name="description"
          render={({ field: { onChange, onBlur, value } }) => (
            <Input
              label="Descripción"
              placeholder="Describe detalladamente lo que necesitas..."
              value={value}
              onChangeText={onChange}
              onBlur={onBlur}
              error={errors.description?.message}
              multiline
              numberOfLines={4}
              required
            />
          )}
        />

        <Controller
          control={control}
          name="instrument"
          render={({ field: { onChange, value } }) => (
            <Select
              label="Instrumento Principal"
              placeholder="Selecciona un instrumento"
              value={value}
              onValueChange={onChange}
              error={errors.instrument?.message}
              required
              options={[
                { label: 'Guitarra', value: 'guitar' },
                { label: 'Piano', value: 'piano' },
                { label: 'Violín', value: 'violin' },
                { label: 'Saxofón', value: 'saxophone' },
                { label: 'Batería', value: 'drums' },
                { label: 'Bajo', value: 'bass' },
                { label: 'Voz', value: 'voice' },
                { label: 'Otro', value: 'other' },
              ]}
            />
          )}
        />

        <View style={styles.budgetContainer}>
          <Controller
            control={control}
            name="budget_min"
            render={({ field: { onChange, onBlur, value } }) => (
              <Input
                label="Presupuesto Mínimo ($)"
                placeholder="0"
                value={value?.toString()}
                onChangeText={(text) => onChange(parseFloat(text) || 0)}
                onBlur={onBlur}
                error={errors.budget_min?.message}
                keyboardType="numeric"
                required
                style={styles.budgetInput}
              />
            )}
          />

          <Controller
            control={control}
            name="budget_max"
            render={({ field: { onChange, onBlur, value } }) => (
              <Input
                label="Presupuesto Máximo ($)"
                placeholder="0"
                value={value?.toString()}
                onChangeText={(text) => onChange(parseFloat(text) || 0)}
                onBlur={onBlur}
                error={errors.budget_max?.message}
                keyboardType="numeric"
                required
                style={styles.budgetInput}
              />
            )}
          />
        </View>

        <Controller
          control={control}
          name="event_date"
          render={({ field: { onChange, value } }) => (
            <DatePicker
              label="Fecha del Evento"
              value={value}
              onDateChange={onChange}
              error={errors.event_date?.message}
              minimumDate={new Date()}
              required
            />
          )}
        />

        <Controller
          control={control}
          name="duration_hours"
          render={({ field: { onChange, onBlur, value } }) => (
            <Input
              label="Duración (horas)"
              placeholder="2"
              value={value?.toString()}
              onChangeText={(text) => onChange(parseInt(text) || 1)}
              onBlur={onBlur}
              error={errors.duration_hours?.message}
              keyboardType="numeric"
              required
            />
          )}
        />

        <Controller
          control={control}
          name="location"
          render={({ field: { onChange, value } }) => (
            <LocationPicker
              label="Ubicación del Evento"
              value={value}
              onLocationChange={onChange}
              error={errors.location?.message}
              required
            />
          )}
        />

        <View style={styles.requirementsContainer}>
          <Text style={styles.requirementsLabel}>Requisitos Adicionales</Text>
          <Text style={styles.requirementsSubtitle}>
            Selecciona los requisitos que consideres importantes
          </Text>
          
          <View style={styles.requirementsGrid}>
            {requirementOptions.map((requirement) => (
              <TouchableOpacity
                key={requirement}
                style={[
                  styles.requirementChip,
                  selectedRequirements.includes(requirement) && styles.requirementChipSelected,
                ]}
                onPress={() => {
                  if (selectedRequirements.includes(requirement)) {
                    setSelectedRequirements(prev => 
                      prev.filter(r => r !== requirement)
                    );
                  } else {
                    setSelectedRequirements(prev => [...prev, requirement]);
                  }
                }}
              >
                <Text style={[
                  styles.requirementText,
                  selectedRequirements.includes(requirement) && styles.requirementTextSelected,
                ]}>
                  {requirement}
                </Text>
              </TouchableOpacity>
            ))}
          </View>
        </View>

        <Button
          title="Crear Solicitud"
          onPress={handleSubmit(handleFormSubmit)}
          disabled={!isValid || isLoading}
          loading={isLoading}
          style={styles.submitButton}
        />
      </View>
    </ScrollView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.white,
  },
  title: {
    ...typography.h1,
    color: colors.gray[900],
    textAlign: 'center',
    marginTop: spacing[6],
    marginBottom: spacing[2],
  },
  subtitle: {
    ...typography.bodyLarge,
    color: colors.gray[600],
    textAlign: 'center',
    marginBottom: spacing[6],
    paddingHorizontal: spacing[4],
  },
  form: {
    paddingHorizontal: spacing[4],
    paddingBottom: spacing[8],
  },
  budgetContainer: {
    flexDirection: 'row',
    gap: spacing[3],
  },
  budgetInput: {
    flex: 1,
  },
  requirementsContainer: {
    marginTop: spacing[4],
  },
  requirementsLabel: {
    ...typography.h4,
    color: colors.gray[900],
    marginBottom: spacing[2],
  },
  requirementsSubtitle: {
    ...typography.bodyMedium,
    color: colors.gray[600],
    marginBottom: spacing[3],
  },
  requirementsGrid: {
    flexDirection: 'row',
    flexWrap: 'wrap',
    gap: spacing[2],
  },
  requirementChip: {
    paddingHorizontal: spacing[3],
    paddingVertical: spacing[2],
    borderRadius: spacing[4],
    borderWidth: 1,
    borderColor: colors.gray[300],
    backgroundColor: colors.white,
  },
  requirementChipSelected: {
    backgroundColor: colors.primary[500],
    borderColor: colors.primary[500],
  },
  requirementText: {
    ...typography.bodySmall,
    color: colors.gray[700],
  },
  requirementTextSelected: {
    color: colors.white,
  },
  submitButton: {
    marginTop: spacing[6],
  },
});
```

---

## 📱 **Pantallas del Sistema**

### **🏠 Pantalla Principal de Solicitudes**
```typescript
// screens/requests/RequestsListScreen.tsx
import React, { useState, useEffect, useCallback } from 'react';
import { View, FlatList, StyleSheet, RefreshControl } from 'react-native';
import { useNavigation } from '@react-navigation/native';
import { useRequests } from '../../hooks/useRequests';
import { RequestCard } from '../../components/business/RequestCard';
import { SearchBar } from '../../components/common/SearchBar';
import { FilterModal } from '../../components/common/FilterModal';
import { EmptyState } from '../../components/common/EmptyState';
import { LoadingSpinner } from '../../components/common/LoadingSpinner';
import { colors, spacing } from '../../theme';
import { MusicianRequest } from '../../types/api';

export const RequestsListScreen: React.FC = () => {
  const navigation = useNavigation();
  const [searchQuery, setSearchQuery] = useState('');
  const [filters, setFilters] = useState({
    instrument: '',
    location: '',
    budgetRange: '',
    status: 'open',
  });
  const [showFilters, setShowFilters] = useState(false);

  const {
    requests,
    loading,
    refreshing,
    error,
    fetchRequests,
    refreshRequests,
  } = useRequests();

  useEffect(() => {
    fetchRequests();
  }, [fetchRequests]);

  const handleRequestPress = useCallback((request: MusicianRequest) => {
    navigation.navigate('RequestDetail', { requestId: request.id });
  }, [navigation]);

  const handleApplyPress = useCallback((request: MusicianRequest) => {
    navigation.navigate('ApplyToRequest', { requestId: request.id });
  }, [navigation]);

  const handleSearch = useCallback((query: string) => {
    setSearchQuery(query);
    // Implementar búsqueda en tiempo real
  }, []);

  const handleFilterChange = useCallback((newFilters: any) => {
    setFilters(newFilters);
    setShowFilters(false);
    // Aplicar filtros y recargar datos
  }, []);

  const filteredRequests = requests.filter(request => {
    const matchesSearch = request.title.toLowerCase().includes(searchQuery.toLowerCase()) ||
                         request.description.toLowerCase().includes(searchQuery.toLowerCase());
    
    const matchesInstrument = !filters.instrument || request.instrument === filters.instrument;
    const matchesStatus = !filters.status || request.status === filters.status;
    
    return matchesSearch && matchesInstrument && matchesStatus;
  });

  const renderRequestItem = useCallback(({ item }: { item: MusicianRequest }) => (
    <RequestCard
      request={item}
      onPress={handleRequestPress}
      onApply={handleApplyPress}
      showApplyButton={item.status === 'open'}
    />
  ), [handleRequestPress, handleApplyPress]);

  const renderEmptyState = () => (
    <EmptyState
      icon="search"
      title="No se encontraron solicitudes"
      message="Intenta ajustar tus filtros de búsqueda o crear una nueva solicitud"
      actionButton={{
        title: "Crear Solicitud",
        onPress: () => navigation.navigate('CreateRequest'),
      }}
    />
  );

  if (loading && !refreshing) {
    return <LoadingSpinner />;
  }

  return (
    <View style={styles.container}>
      <SearchBar
        placeholder="Buscar solicitudes..."
        value={searchQuery}
        onChangeText={handleSearch}
        onFilterPress={() => setShowFilters(true)}
        showFilterButton
      />

      <FlatList
        data={filteredRequests}
        renderItem={renderRequestItem}
        keyExtractor={(item) => item.id}
        contentContainerStyle={styles.listContent}
        showsVerticalScrollIndicator={false}
        refreshControl={
          <RefreshControl
            refreshing={refreshing}
            onRefresh={refreshRequests}
            colors={[colors.primary[500]]}
          />
        }
        ListEmptyComponent={renderEmptyState}
      />

      <FilterModal
        visible={showFilters}
        filters={filters}
        onClose={() => setShowFilters(false)}
        onApply={handleFilterChange}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.gray[50],
  },
  listContent: {
    paddingBottom: spacing[8],
  },
});
```

---

## 🔄 **Estado y Gestión**

### **📊 Slice de Redux para Solicitudes**
```typescript
// store/slices/requestsSlice.ts
import { createSlice, createAsyncThunk, PayloadAction } from '@reduxjs/toolkit';
import { MusicianRequest, CreateRequestData } from '../../types/api';
import { requestsService } from '../../services/requestsService';

interface RequestsState {
  requests: MusicianRequest[];
  myRequests: MusicianRequest[];
  currentRequest: MusicianRequest | null;
  loading: boolean;
  error: string | null;
  filters: {
    instrument: string;
    location: string;
    budgetRange: string;
    status: string;
  };
  pagination: {
    page: number;
    limit: number;
    hasMore: boolean;
  };
}

const initialState: RequestsState = {
  requests: [],
  myRequests: [],
  currentRequest: null,
  loading: false,
  error: null,
  filters: {
    instrument: '',
    location: '',
    budgetRange: '',
    status: 'open',
  },
  pagination: {
    page: 1,
    limit: 20,
    hasMore: true,
  },
};

// Async thunks
export const fetchRequests = createAsyncThunk(
  'requests/fetchRequests',
  async (params?: { page?: number; filters?: any }) => {
    const response = await requestsService.getRequests(params);
    return response;
  }
);

export const createRequest = createAsyncThunk(
  'requests/createRequest',
  async (data: CreateRequestData) => {
    const response = await requestsService.createRequest(data);
    return response;
  }
);

export const updateRequest = createAsyncThunk(
  'requests/updateRequest',
  async ({ id, data }: { id: string; data: Partial<CreateRequestData> }) => {
    const response = await requestsService.updateRequest(id, data);
    return response;
  }
);

export const deleteRequest = createAsyncThunk(
  'requests/deleteRequest',
  async (id: string) => {
    await requestsService.deleteRequest(id);
    return id;
  }
);

export const applyToRequest = createAsyncThunk(
  'requests/applyToRequest',
  async ({ requestId, data }: { requestId: string; data: any }) => {
    const response = await requestsService.applyToRequest(requestId, data);
    return response;
  }
);

const requestsSlice = createSlice({
  name: 'requests',
  initialState,
  reducers: {
    setCurrentRequest: (state, action: PayloadAction<MusicianRequest | null>) => {
      state.currentRequest = action.payload;
    },
    setFilters: (state, action: PayloadAction<Partial<RequestsState['filters']>>) => {
      state.filters = { ...state.filters, ...action.payload };
      state.pagination.page = 1; // Reset pagination when filters change
    },
    clearFilters: (state) => {
      state.filters = initialState.filters;
      state.pagination.page = 1;
    },
    resetPagination: (state) => {
      state.pagination = initialState.pagination;
    },
    clearError: (state) => {
      state.error = null;
    },
  },
  extraReducers: (builder) => {
    builder
      // Fetch requests
      .addCase(fetchRequests.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchRequests.fulfilled, (state, action) => {
        state.loading = false;
        const { requests: newRequests, hasMore } = action.payload;
        
        if (action.meta.arg?.page === 1) {
          state.requests = newRequests;
        } else {
          state.requests = [...state.requests, ...newRequests];
        }
        
        state.pagination.hasMore = hasMore;
        if (newRequests.length > 0) {
          state.pagination.page += 1;
        }
      })
      .addCase(fetchRequests.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message || 'Error al cargar las solicitudes';
      })
      
      // Create request
      .addCase(createRequest.fulfilled, (state, action) => {
        const newRequest = action.payload;
        state.myRequests.unshift(newRequest);
        state.requests.unshift(newRequest);
      })
      
      // Update request
      .addCase(updateRequest.fulfilled, (state, action) => {
        const updatedRequest = action.payload;
        const updateInArray = (array: MusicianRequest[]) => {
          const index = array.findIndex(r => r.id === updatedRequest.id);
          if (index !== -1) {
            array[index] = updatedRequest;
          }
        };
        
        updateInArray(state.requests);
        updateInArray(state.myRequests);
        if (state.currentRequest?.id === updatedRequest.id) {
          state.currentRequest = updatedRequest;
        }
      })
      
      // Delete request
      .addCase(deleteRequest.fulfilled, (state, action) => {
        const deletedId = action.payload;
        state.requests = state.requests.filter(r => r.id !== deletedId);
        state.myRequests = state.myRequests.filter(r => r.id !== deletedId);
        if (state.currentRequest?.id === deletedId) {
          state.currentRequest = null;
        }
      })
      
      // Apply to request
      .addCase(applyToRequest.fulfilled, (state, action) => {
        // Handle successful application
        // Could update request status or add application to state
      });
  },
});

export const {
  setCurrentRequest,
  setFilters,
  clearFilters,
  resetPagination,
  clearError,
} = requestsSlice.actions;

export default requestsSlice.reducer;
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [React Native Documentation](https://reactnative.dev/)
- [React Hook Form](https://react-hook-form.com/)
- [Zod Validation](https://zod.dev/)

### **📖 Documentación Relacionada**
- [Arquitectura Móvil](./ARQUITECTURA_MOVIL.md)
- [Stack Tecnológico Móvil](./STACK_TECNOLOGICO_MOVIL.md)
- [Configuración Móvil](./CONFIGURACION_MOVIL.md)

---

*Este sistema de solicitudes móvil proporciona una base sólida para la funcionalidad core de MussikOn, permitiendo una experiencia de usuario fluida y eficiente en dispositivos móviles.*
