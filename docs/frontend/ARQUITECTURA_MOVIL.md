# 📱 **ARQUITECTURA MÓVIL - FRONTEND MUSSIKON**

## 📋 **Índice de la Arquitectura Móvil**

### **🏗️ Arquitectura General**
- [Patrones de Diseño](./ARQUITECTURA_MOVIL.md#patrones-de-diseño) - Arquitectura de componentes
- [Estructura de Carpetas](./ARQUITECTURA_MOVIL.md#estructura-de-carpetas) - Organización del proyecto
- [Gestión de Estado](./ARQUITECTURA_MOVIL.md#gestión-de-estado) - Redux y Context API

### **🔧 Implementación Técnica**
- [Navegación](./ARQUITECTURA_MOVIL.md#navegación) - React Navigation y routing
- [Comunicación](./ARQUITECTURA_MOVIL.md#comunicación) - API y SignalR
- [Almacenamiento](./ARQUITECTURA_MOVIL.md#almacenamiento) - Local y remoto

---

## 🎯 **Propósito de la Arquitectura Móvil**

### **Objetivo Principal**
Implementar una aplicación móvil nativa que permita a músicos y organizadores gestionar solicitudes de músicos de manera eficiente, con una experiencia de usuario optimizada y funcionalidades offline.

### **Principios Clave**
- **Mobile-First**: Diseño optimizado para dispositivos móviles
- **Offline-First**: Funcionalidades disponibles sin conexión
- **Performance**: Aplicación rápida y responsiva
- **Accessibility**: Cumplimiento de estándares WCAG 2.1
- **Cross-Platform**: Código compartido entre iOS y Android

---

## 🏗️ **Patrones de Diseño**

### **📱 Arquitectura de Componentes**
```
┌─────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │   Screens   │  │ Components  │  │   Navigation        │ │
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
│  │    Redux    │  │   Context   │  │   Local Storage     │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  INFRASTRUCTURE LAYER                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │     API     │  │   Socket    │  │   Storage           │ │
│  │             │  │             │  │                     │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### **📁 Estructura de Carpetas del Proyecto**
```
MussikOnMobile/
├── 📁 App.tsx                 # Componente raíz de la aplicación
├── 📁 src/
│   ├── 📁 app/                # Configuración de la aplicación
│   │   ├── App.tsx
│   │   ├── AppNavigator.tsx
│   │   └── AppProvider.tsx
│   ├── 📁 components/         # Componentes reutilizables
│   │   ├── 📁 common/         # Componentes genéricos
│   │   │   ├── Button.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Card.tsx
│   │   │   └── Modal.tsx
│   │   ├── 📁 forms/          # Componentes de formularios
│   │   │   ├── MusicianRequestForm.tsx
│   │   │   ├── ProfileForm.tsx
│   │   │   └── SearchForm.tsx
│   │   └── 📁 business/       # Componentes específicos del negocio
│   │       ├── MusicianCard.tsx
│   │       ├── RequestCard.tsx
│   │       └── ChatBubble.tsx
│   ├── 📁 screens/            # Pantallas de la aplicación
│   │   ├── 📁 auth/           # Autenticación
│   │   │   ├── LoginScreen.tsx
│   │   │   ├── RegisterScreen.tsx
│   │   │   └── ForgotPasswordScreen.tsx
│   │   ├── 📁 main/           # Pantallas principales
│   │   │   ├── HomeScreen.tsx
│   │   │   ├── ProfileScreen.tsx
│   │   │   └── SettingsScreen.tsx
│   │   ├── 📁 requests/       # Sistema de solicitudes (CORE)
│   │   │   ├── RequestsListScreen.tsx
│   │   │   ├── CreateRequestScreen.tsx
│   │   │   ├── RequestDetailScreen.tsx
│   │   │   └── MyRequestsScreen.tsx
│   │   ├── 📁 chat/           # Sistema de chat
│   │   │   ├── ChatListScreen.tsx
│   │   │   ├── ChatScreen.tsx
│   │   │   └── ChatDetailScreen.tsx
│   │   └── 📁 notifications/  # Notificaciones
│   │       ├── NotificationsScreen.tsx
│   │       └── NotificationDetailScreen.tsx
│   ├── 📁 navigation/         # Configuración de navegación
│   │   ├── MainTabs.tsx       # Navegación por tabs
│   │   ├── AuthStack.tsx      # Stack de autenticación
│   │   ├── MainStack.tsx      # Stack principal
│   │   └── RequestStack.tsx   # Stack de solicitudes
│   ├── 📁 store/              # Gestión de estado global
│   │   ├── store.ts           # Configuración de Redux
│   │   ├── 📁 slices/         # Slices de Redux Toolkit
│   │   │   ├── authSlice.ts
│   │   │   ├── requestsSlice.ts
│   │   │   ├── chatSlice.ts
│   │   │   └── notificationsSlice.ts
│   │   └── 📁 middleware/     # Middleware personalizado
│   │       ├── logger.ts
│   │       └── offline.ts
│   ├── 📁 services/           # Servicios de la aplicación
│   │   ├── api.ts             # Cliente HTTP configurado
│   │   ├── authService.ts     # Servicio de autenticación
│   │   ├── requestService.ts  # Servicio de solicitudes
│   │   ├── chatService.ts     # Servicio de chat
│   │   ├── notificationService.ts # Servicio de notificaciones
│   │   └── storageService.ts  # Servicio de almacenamiento
│   ├── 📁 hooks/              # Hooks personalizados
│   │   ├── useAuth.ts         # Hook de autenticación
│   │   ├── useRequests.ts     # Hook de solicitudes
│   │   ├── useChat.ts         # Hook de chat
│   │   ├── useNotifications.ts # Hook de notificaciones
│   │   ├── useOffline.ts      # Hook de estado offline
│   │   └── useSocket.ts       # Hook de Socket.IO
│   ├── 📁 contexts/           # Contextos de React
│   │   ├── AuthContext.tsx    # Contexto de autenticación
│   │   ├── SocketContext.tsx  # Contexto de Socket.IO
│   │   └── ThemeContext.tsx   # Contexto de tema
│   ├── 📁 utils/              # Utilidades y helpers
│   │   ├── constants.ts       # Constantes de la aplicación
│   │   ├── helpers.ts         # Funciones helper
│   │   ├── validation.ts      # Validaciones
│   │   ├── formatters.ts      # Formateadores de datos
│   │   └── permissions.ts     # Gestión de permisos
│   ├── 📁 types/              # Tipos TypeScript
│   │   ├── api.ts             # Tipos de API
│   │   ├── navigation.ts      # Tipos de navegación
│   │   ├── store.ts           # Tipos de Redux
│   │   └── common.ts          # Tipos comunes
│   ├── 📁 theme/              # Sistema de diseño
│   │   ├── colors.ts          # Paleta de colores
│   │   ├── typography.ts      # Tipografía
│   │   ├── spacing.ts         # Espaciado
│   │   └── index.ts           # Exportaciones del tema
│   └── 📁 assets/             # Recursos estáticos
│       ├── 📁 images/         # Imágenes
│       ├── 📁 icons/          # Iconos
│       └── 📁 fonts/          # Fuentes personalizadas
├── 📁 __tests__/              # Tests de la aplicación
│   ├── 📁 components/         # Tests de componentes
│   ├── 📁 screens/            # Tests de pantallas
│   ├── 📁 services/           # Tests de servicios
│   └── 📁 utils/              # Tests de utilidades
├── 📁 docs/                   # Documentación del proyecto
├── 📁 .expo/                  # Configuración de Expo
├── 📁 android/                # Configuración específica de Android
├── 📁 ios/                    # Configuración específica de iOS
├── 📁 app.json                # Configuración de Expo
├── 📁 package.json            # Dependencias del proyecto
├── 📁 tsconfig.json           # Configuración de TypeScript
├── 📁 babel.config.js         # Configuración de Babel
└── 📁 metro.config.js         # Configuración de Metro
```

---

## 🔧 **Gestión de Estado**

### **📊 Arquitectura de Estado**
- **Redux Toolkit**: Estado global de la aplicación
- **React Context**: Estado local y configuración
- **AsyncStorage**: Persistencia de datos offline
- **Expo SecureStore**: Almacenamiento seguro de tokens

### **🔄 Flujo de Datos**
```
API Response → Redux Action → Reducer → Store → Component → UI Update
```

### **📱 Slices de Redux**
```typescript
// authSlice.ts
export const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    setUser: (state, action) => {
      state.user = action.payload;
      state.isAuthenticated = true;
    },
    logout: (state) => {
      state.user = null;
      state.isAuthenticated = false;
    }
  }
});

// requestsSlice.ts
export const requestsSlice = createSlice({
  name: 'requests',
  initialState,
  reducers: {
    setRequests: (state, action) => {
      state.requests = action.payload;
    },
    addRequest: (state, action) => {
      state.requests.push(action.payload);
    },
    updateRequest: (state, action) => {
      const index = state.requests.findIndex(r => r.id === action.payload.id);
      if (index !== -1) {
        state.requests[index] = action.payload;
      }
    }
  }
});
```

---

## 🧭 **Navegación**

### **📱 Estructura de Navegación**
```
AppNavigator
├── AuthStack (Login, Register, ForgotPassword)
└── MainStack
    ├── MainTabs
    │   ├── HomeTab (HomeScreen)
    │   ├── RequestsTab (RequestsListScreen)
    │   ├── ChatTab (ChatListScreen)
    │   └── ProfileTab (ProfileScreen)
    └── RequestStack
        ├── CreateRequestScreen
        ├── RequestDetailScreen
        └── EditRequestScreen
```

### **🔧 Configuración de Navegación**
```typescript
// MainTabs.tsx
const Tab = createBottomTabNavigator();

export const MainTabs = () => {
  return (
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ focused, color, size }) => {
          let iconName;
          switch (route.name) {
            case 'Home':
              iconName = focused ? 'home' : 'home-outline';
              break;
            case 'Requests':
              iconName = focused ? 'list' : 'list-outline';
              break;
            case 'Chat':
              iconName = focused ? 'chatbubble' : 'chatbubble-outline';
              break;
            case 'Profile':
              iconName = focused ? 'person' : 'person-outline';
              break;
          }
          return <Ionicons name={iconName} size={size} color={color} />;
        },
        tabBarActiveTintColor: colors.primary,
        tabBarInactiveTintColor: colors.gray,
      })}
    >
      <Tab.Screen name="Home" component={HomeScreen} />
      <Tab.Screen name="Requests" component={RequestsListScreen} />
      <Tab.Screen name="Chat" component={ChatListScreen} />
      <Tab.Screen name="Profile" component={ProfileScreen} />
    </Tab.Navigator>
  );
};
```

---

## 🌐 **Comunicación**

### **📡 API Client**
```typescript
// services/api.ts
import axios from 'axios';
import AsyncStorage from '@react-native-async-storage/async-storage';

const api = axios.create({
  baseURL: process.env.EXPO_PUBLIC_API_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Interceptor para agregar token de autenticación
api.interceptors.request.use(
  async (config) => {
    const token = await AsyncStorage.getItem('authToken');
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
      await AsyncStorage.removeItem('authToken');
      // Redirigir a login
    }
    return Promise.reject(error);
  }
);

export default api;
```

### **🔌 Socket.IO Integration**
```typescript
// services/socketService.ts
import io from 'socket.io-client';
import { store } from '../store/store';
import { addNotification } from '../store/slices/notificationsSlice';

class SocketService {
  private socket: any = null;

  connect(token: string) {
    this.socket = io(process.env.EXPO_PUBLIC_SOCKET_URL, {
      auth: { token },
      transports: ['websocket'],
    });

    this.socket.on('connect', () => {
      console.log('Connected to Socket.IO');
    });

    this.socket.on('musician_request_update', (data) => {
      store.dispatch(addNotification({
        id: Date.now(),
        type: 'request_update',
        title: 'Solicitud Actualizada',
        message: `La solicitud "${data.title}" ha sido actualizada`,
        data: data,
      }));
    });

    this.socket.on('new_message', (data) => {
      store.dispatch(addNotification({
        id: Date.now(),
        type: 'new_message',
        title: 'Nuevo Mensaje',
        message: `Tienes un nuevo mensaje de ${data.senderName}`,
        data: data,
      }));
    });
  }

  disconnect() {
    if (this.socket) {
      this.socket.disconnect();
      this.socket = null;
    }
  }

  emit(event: string, data: any) {
    if (this.socket) {
      this.socket.emit(event, data);
    }
  }
}

export default new SocketService();
```

---

## 💾 **Almacenamiento**

### **📱 Estrategia de Almacenamiento**
- **AsyncStorage**: Datos no sensibles y cache
- **Expo SecureStore**: Tokens y datos sensibles
- **Redux Persist**: Persistencia del estado global
- **Offline Queue**: Cola de operaciones offline

### **🔒 Implementación de Persistencia**
```typescript
// store/store.ts
import { configureStore } from '@reduxjs/toolkit';
import { persistStore, persistReducer } from 'redux-persist';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { authSlice } from './slices/authSlice';
import { requestsSlice } from './slices/requestsSlice';

const persistConfig = {
  key: 'root',
  storage: AsyncStorage,
  whitelist: ['auth', 'requests'], // Solo persistir estos slices
};

const persistedAuthReducer = persistReducer(persistConfig, authSlice.reducer);
const persistedRequestsReducer = persistReducer(persistConfig, requestsSlice.reducer);

export const store = configureStore({
  reducer: {
    auth: persistedAuthReducer,
    requests: persistedRequestsReducer,
    chat: chatSlice.reducer,
    notifications: notificationsSlice.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
      },
    }),
});

export const persistor = persistStore(store);
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

---

## 🧪 **Testing**

### **📊 Estrategia de Testing**
- **Unit Tests**: Componentes individuales y hooks
- **Integration Tests**: Flujos de usuario completos
- **E2E Tests**: Testing de la aplicación completa
- **Snapshot Tests**: Capturas de componentes

### **🔧 Herramientas de Testing**
```typescript
// __tests__/components/Button.test.tsx
import React from 'react';
import { render, fireEvent } from '@testing-library/react-native';
import { Button } from '../../src/components/common/Button';

describe('Button Component', () => {
  it('renders correctly', () => {
    const { getByText } = render(
      <Button onPress={() => {}}>Test Button</Button>
    );
    
    expect(getByText('Test Button')).toBeTruthy();
  });

  it('calls onPress when pressed', () => {
    const onPressMock = jest.fn();
    const { getByText } = render(
      <Button onPress={onPressMock}>Test Button</Button>
    );
    
    fireEvent.press(getByText('Test Button'));
    expect(onPressMock).toHaveBeenCalledTimes(1);
  });
});
```

---

## 🚀 **Performance y Optimización**

### **⚡ Estrategias de Performance**
- **Lazy Loading**: Carga diferida de pantallas
- **Memoization**: React.memo y useMemo
- **Image Optimization**: Compresión y cache de imágenes
- **Bundle Splitting**: División del bundle por rutas

### **📱 Optimizaciones Específicas**
```typescript
// Lazy loading de pantallas
const RequestsListScreen = React.lazy(() => import('./RequestsListScreen'));
const CreateRequestScreen = React.lazy(() => import('./CreateRequestScreen'));

// Memoización de componentes
const MusicianCard = React.memo(({ musician, onPress }) => {
  return (
    <TouchableOpacity onPress={onPress}>
      <Text>{musician.name}</Text>
      <Text>{musician.instrument}</Text>
    </TouchableOpacity>
  );
});

// Optimización de listas
const RequestsList = ({ requests }) => {
  const renderItem = useCallback(({ item }) => (
    <RequestCard request={item} />
  ), []);

  const keyExtractor = useCallback((item) => item.id.toString(), []);

  return (
    <FlatList
      data={requests}
      renderItem={renderItem}
      keyExtractor={keyExtractor}
      removeClippedSubviews={true}
      maxToRenderPerBatch={10}
      windowSize={10}
    />
  );
};
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [React Native Documentation](https://reactnative.dev/)
- [Expo Documentation](https://docs.expo.dev/)
- [React Navigation](https://reactnavigation.org/)
- [Redux Toolkit](https://redux-toolkit.js.org/)

### **📖 Documentación Relacionada**
- [Stack Tecnológico Móvil](./STACK_TECNOLOGICO_MOVIL.md)
- [Sistema de Solicitudes Móvil](./SOLICITUDES_MOVIL.md)
- [Configuración del Entorno Móvil](./CONFIGURACION_MOVIL.md)

---

*Esta arquitectura móvil proporciona una base sólida para el desarrollo de la aplicación React Native, siguiendo las mejores prácticas de desarrollo móvil y patrones de diseño probados.*
