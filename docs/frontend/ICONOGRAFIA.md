# 🎭 **ICONOGRAFÍA - MUSSIKON**

## 📋 **Índice de Iconografía**

### **🎨 Sistema de Iconos**
- [Iconos Principales](#-iconos-principales)
- [Iconos de Navegación](#-iconos-de-navegación)
- [Iconos de Acción](#-iconos-de-acción)
- [Iconos de Estado](#-iconos-de-estado)

### **📱 Aplicación por Plataforma**
- [React Native (Móvil)](#-react-native-móvil)
- [Next.js (Admin)](#-nextjs-admin)
- [Biblioteca de Iconos](#-biblioteca-de-iconos)

---

## 🎨 **Sistema de Iconos**

### **Principios de Diseño**
- **Consistencia**: Estilo visual uniforme en toda la aplicación
- **Claridad**: Iconos fácilmente reconocibles y comprensibles
- **Simplicidad**: Diseño limpio y minimalista
- **Escalabilidad**: Iconos que se ven bien en diferentes tamaños
- **Accesibilidad**: Iconos con texto alternativo y alto contraste

### **Estilo Visual**
- **Outline**: Iconos de contorno para acciones secundarias
- **Filled**: Iconos rellenos para acciones principales
- **Duotone**: Iconos con dos colores para elementos destacados
- **Rounded**: Bordes redondeados para un look moderno
- **Consistent Stroke**: Grosor de línea uniforme

---

## 🎭 **Iconos Principales**

### **Iconos de Marca**
- **Logo**: Logo principal de MussikOn
- **Logo Icon**: Versión icono del logo
- **Logo Text**: Versión texto del logo
- **Logo Symbol**: Símbolo sin texto

### **Iconos de Categoría**
- **Music**: Nota musical, instrumentos
- **Event**: Calendario, evento, celebración
- **User**: Perfil, usuario, avatar
- **Location**: Ubicación, mapa, GPS
- **Time**: Reloj, tiempo, duración

---

## 🧭 **Iconos de Navegación**

### **Navegación Principal**
- **Home**: Casa, inicio
- **Search**: Lupa, búsqueda
- **Profile**: Usuario, perfil
- **Settings**: Engranaje, configuración
- **Notifications**: Campana, notificaciones

### **Navegación Secundaria**
- **Back**: Flecha hacia atrás
- **Forward**: Flecha hacia adelante
- **Close**: X, cerrar
- **Menu**: Hamburger, menú
- **More**: Tres puntos, más opciones

---

## ⚡ **Iconos de Acción**

### **Acciones Principales**
- **Add**: Plus, agregar
- **Edit**: Lápiz, editar
- **Delete**: Basura, eliminar
- **Save**: Disquete, guardar
- **Share**: Compartir, enlace

### **Acciones de Música**
- **Play**: Triángulo, reproducir
- **Pause**: Dos líneas, pausar
- **Stop**: Cuadrado, detener
- **Skip**: Flechas, saltar
- **Volume**: Altavoz, volumen

---

## 📊 **Iconos de Estado**

### **Estados de Solicitud**
- **Pending**: Reloj, pendiente
- **Approved**: Check, aprobado
- **Rejected**: X, rechazado
- **Completed**: Check circle, completado
- **Cancelled**: Prohibido, cancelado

### **Estados de Usuario**
- **Online**: Punto verde, en línea
- **Offline**: Punto gris, desconectado
- **Busy**: Punto rojo, ocupado
- **Away**: Punto amarillo, ausente
- **Verified**: Checkmark, verificado

---

## 📱 **React Native (Móvil)**

### **Configuración de Expo Vector Icons**
```typescript
// theme/icons.ts
export const iconSets = {
  // Conjuntos de iconos disponibles
  material: 'MaterialIcons',
  materialCommunity: 'MaterialCommunityIcons',
  ionicon: 'Ionicons',
  fontAwesome: 'FontAwesome',
  fontAwesome5: 'FontAwesome5',
  feather: 'Feather',
  antDesign: 'AntDesign',
  entypo: 'Entypo',
  evil: 'EvilIcons',
  foundation: 'Foundation',
  octicons: 'Octicons',
  simpleLine: 'SimpleLineIcons',
  zocial: 'Zocial',
};

// Iconos principales de la aplicación
export const appIcons = {
  // Navegación
  home: { set: 'material', name: 'home' },
  search: { set: 'material', name: 'search' },
  profile: { set: 'material', name: 'person' },
  settings: { set: 'material', name: 'settings' },
  notifications: { set: 'material', name: 'notifications' },
  
  // Acciones
  add: { set: 'material', name: 'add' },
  edit: { set: 'material', name: 'edit' },
  delete: { set: 'material', name: 'delete' },
  save: { set: 'material', name: 'save' },
  share: { set: 'material', name: 'share' },
  
  // Música
  play: { set: 'material', name: 'play-arrow' },
  pause: { set: 'material', name: 'pause' },
  stop: { set: 'material', name: 'stop' },
  skipNext: { set: 'material', name: 'skip-next' },
  skipPrevious: { set: 'material', name: 'skip-previous' },
  volume: { set: 'material', name: 'volume-up' },
  
  // Estados
  pending: { set: 'material', name: 'schedule' },
  approved: { set: 'material', name: 'check-circle' },
  rejected: { set: 'material', name: 'cancel' },
  completed: { set: 'material', name: 'task-alt' },
  cancelled: { set: 'material', name: 'block' },
  
  // Usuarios
  online: { set: 'material', name: 'fiber-manual-record' },
  offline: { set: 'material', name: 'fiber-manual-record' },
  busy: { set: 'material', name: 'fiber-manual-record' },
  away: { set: 'material', name: 'fiber-manual-record' },
  verified: { set: 'material', name: 'verified' },
  
  // Categorías
  music: { set: 'material', name: 'music-note' },
  event: { set: 'material', name: 'event' },
  user: { set: 'material', name: 'person' },
  location: { set: 'material', name: 'location-on' },
  time: { set: 'material', name: 'access-time' },
};
```

### **Componente de Icono Reutilizable**
```typescript
// components/Icon.tsx
import React from 'react';
import { StyleProp, ViewStyle } from 'react-native';
import MaterialIcons from '@expo/vector-icons/MaterialIcons';
import MaterialCommunityIcons from '@expo/vector-icons/MaterialCommunityIcons';
import Ionicons from '@expo/vector-icons/Ionicons';
import FontAwesome from '@expo/vector-icons/FontAwesome';
import FontAwesome5 from '@expo/vector-icons/FontAwesome5';
import Feather from '@expo/vector-icons/Feather';
import AntDesign from '@expo/vector-icons/AntDesign';
import Entypo from '@expo/vector-icons/Entypo';
import EvilIcons from '@expo/vector-icons/EvilIcons';
import Foundation from '@expo/vector-icons/Foundation';
import Octicons from '@expo/vector-icons/Octicons';
import SimpleLineIcons from '@expo/vector-icons/SimpleLineIcons';
import Zocial from '@expo/vector-icons/Zocial';

interface IconProps {
  set: keyof typeof iconSets;
  name: string;
  size?: number;
  color?: string;
  style?: StyleProp<ViewStyle>;
}

const iconSets = {
  material: MaterialIcons,
  materialCommunity: MaterialCommunityIcons,
  ionicon: Ionicons,
  fontAwesome: FontAwesome,
  fontAwesome5: FontAwesome5,
  feather: Feather,
  antDesign: AntDesign,
  entypo: Entypo,
  evil: EvilIcons,
  foundation: Foundation,
  octicons: Octicons,
  simpleLine: SimpleLineIcons,
  zocial: Zocial,
};

export const Icon: React.FC<IconProps> = ({
  set,
  name,
  size = 24,
  color,
  style,
}) => {
  const IconComponent = iconSets[set];
  
  if (!IconComponent) {
    console.warn(`Icon set "${set}" not found`);
    return null;
  }
  
  return (
    <IconComponent
      name={name}
      size={size}
      color={color}
      style={style}
    />
  );
};

// Componente de icono con configuración predefinida
interface AppIconProps {
  icon: keyof typeof appIcons;
  size?: number;
  color?: string;
  style?: StyleProp<ViewStyle>;
}

export const AppIcon: React.FC<AppIconProps> = ({
  icon,
  size = 24,
  color,
  style,
}) => {
  const iconConfig = appIcons[icon];
  
  if (!iconConfig) {
    console.warn(`App icon "${icon}" not found`);
    return null;
  }
  
  return (
    <Icon
      set={iconConfig.set}
      name={iconConfig.name}
      size={size}
      color={color}
      style={style}
    />
  );
};
```

### **Uso en Componentes**
```typescript
// Ejemplo de uso en navegación
import { AppIcon } from '../components/Icon';

const BottomTabs = () => (
  <Tab.Navigator>
    <Tab.Screen
      name="Home"
      component={HomeScreen}
      options={{
        tabBarIcon: ({ color, size }) => (
          <AppIcon icon="home" size={size} color={color} />
        ),
      }}
    />
    <Tab.Screen
      name="Search"
      component={SearchScreen}
      options={{
        tabBarIcon: ({ color, size }) => (
          <AppIcon icon="search" size={size} color={color} />
        ),
      }}
    />
    <Tab.Screen
      name="Profile"
      component={ProfileScreen}
      options={{
        tabBarIcon: ({ color, size }) => (
          <AppIcon icon="profile" size={size} color={color} />
        ),
      }}
    />
  </Tab.Navigator>
);

// Ejemplo de uso en botones
const ActionButton = ({ onPress, icon, children }) => (
  <TouchableOpacity style={styles.button} onPress={onPress}>
    <AppIcon icon={icon} size={20} color={colors.white} />
    <Text style={styles.buttonText}>{children}</Text>
  </TouchableOpacity>
);

// Ejemplo de uso en listas
const RequestItem = ({ request }) => (
  <View style={styles.item}>
    <AppIcon 
      icon={getStatusIcon(request.status)} 
      size={24} 
      color={getStatusColor(request.status)} 
    />
    <Text style={styles.title}>{request.title}</Text>
  </View>
);

const getStatusIcon = (status: string) => {
  switch (status) {
    case 'pending': return 'pending';
    case 'approved': return 'approved';
    case 'rejected': return 'rejected';
    case 'completed': return 'completed';
    case 'cancelled': return 'cancelled';
    default: return 'pending';
  }
};

const getStatusColor = (status: string) => {
  switch (status) {
    case 'pending': return colors.warning;
    case 'approved': return colors.success;
    case 'rejected': return colors.error;
    case 'completed': return colors.success;
    case 'cancelled': return colors.neutral[500];
    default: return colors.neutral[500];
  }
};
```

---

## 🖥️ **Next.js (Admin)**

### **Configuración de Heroicons**
```typescript
// lib/icons.ts
import {
  HomeIcon,
  MagnifyingGlassIcon,
  UserIcon,
  Cog6ToothIcon,
  BellIcon,
  PlusIcon,
  PencilIcon,
  TrashIcon,
  DocumentArrowDownIcon,
  ShareIcon,
  PlayIcon,
  PauseIcon,
  StopIcon,
  ForwardIcon,
  BackwardIcon,
  SpeakerWaveIcon,
  ClockIcon,
  CheckCircleIcon,
  XCircleIcon,
  CheckBadgeIcon,
  XMarkIcon,
  MusicalNoteIcon,
  CalendarIcon,
  MapPinIcon,
  ExclamationTriangleIcon,
  InformationCircleIcon,
} from '@heroicons/react/24/outline';

import {
  HomeIcon as HomeIconSolid,
  MagnifyingGlassIcon as MagnifyingGlassIconSolid,
  UserIcon as UserIconSolid,
  Cog6ToothIcon as Cog6ToothIconSolid,
  BellIcon as BellIconSolid,
  PlusIcon as PlusIconSolid,
  PencilIcon as PencilIconSolid,
  TrashIcon as TrashIconSolid,
  DocumentArrowDownIcon as DocumentArrowDownIconSolid,
  ShareIcon as ShareIconSolid,
  PlayIcon as PlayIconSolid,
  PauseIcon as PauseIconSolid,
  StopIcon as StopIconSolid,
  ForwardIcon as ForwardIconSolid,
  BackwardIcon as BackwardIconSolid,
  SpeakerWaveIcon as SpeakerWaveIconSolid,
  ClockIcon as ClockIconSolid,
  CheckCircleIcon as CheckCircleIconSolid,
  XCircleIcon as XCircleIconSolid,
  CheckBadgeIcon as CheckBadgeIconSolid,
  XMarkIcon as XMarkIconSolid,
  MusicalNoteIcon as MusicalNoteIconSolid,
  CalendarIcon as CalendarIconSolid,
  MapPinIcon as MapPinIconSolid,
  ExclamationTriangleIcon as ExclamationTriangleIconSolid,
  InformationCircleIcon as InformationCircleIconSolid,
} from '@heroicons/react/24/solid';

// Iconos de la aplicación
export const appIcons = {
  // Navegación
  home: { outline: HomeIcon, solid: HomeIconSolid },
  search: { outline: MagnifyingGlassIcon, solid: MagnifyingGlassIconSolid },
  profile: { outline: UserIcon, solid: UserIconSolid },
  settings: { outline: Cog6ToothIcon, solid: Cog6ToothIconSolid },
  notifications: { outline: BellIcon, solid: BellIconSolid },
  
  // Acciones
  add: { outline: PlusIcon, solid: PlusIconSolid },
  edit: { outline: PencilIcon, solid: PencilIconSolid },
  delete: { outline: TrashIcon, solid: TrashIconSolid },
  save: { outline: DocumentArrowDownIcon, solid: DocumentArrowDownIconSolid },
  share: { outline: ShareIcon, solid: ShareIconSolid },
  
  // Música
  play: { outline: PlayIcon, solid: PlayIconSolid },
  pause: { outline: PauseIcon, solid: PauseIconSolid },
  stop: { outline: StopIcon, solid: StopIconSolid },
  skipNext: { outline: ForwardIcon, solid: ForwardIconSolid },
  skipPrevious: { outline: BackwardIcon, solid: BackwardIconSolid },
  volume: { outline: SpeakerWaveIcon, solid: SpeakerWaveIconSolid },
  
  // Estados
  pending: { outline: ClockIcon, solid: ClockIconSolid },
  approved: { outline: CheckCircleIcon, solid: CheckCircleIconSolid },
  rejected: { outline: XCircleIcon, solid: XCircleIconSolid },
  completed: { outline: CheckBadgeIcon, solid: CheckBadgeIconSolid },
  cancelled: { outline: XMarkIcon, solid: XMarkIconSolid },
  
  // Categorías
  music: { outline: MusicalNoteIcon, solid: MusicalNoteIconSolid },
  event: { outline: CalendarIcon, solid: CalendarIconSolid },
  location: { outline: MapPinIcon, solid: MapPinIconSolid },
  warning: { outline: ExclamationTriangleIcon, solid: ExclamationTriangleIconSolid },
  info: { outline: InformationCircleIcon, solid: InformationCircleIconSolid },
};

// Tipos de iconos
export type IconVariant = 'outline' | 'solid';
export type AppIconName = keyof typeof appIcons;
```

### **Componente de Icono Reutilizable**
```tsx
// components/Icon.tsx
import React from 'react';
import { appIcons, type AppIconName, type IconVariant } from '@/lib/icons';
import { cn } from '@/lib/utils';

interface IconProps {
  name: AppIconName;
  variant?: IconVariant;
  size?: number;
  className?: string;
}

export const Icon: React.FC<IconProps> = ({
  name,
  variant = 'outline',
  size = 24,
  className,
}) => {
  const iconConfig = appIcons[name];
  
  if (!iconConfig) {
    console.warn(`App icon "${name}" not found`);
    return null;
  }
  
  const IconComponent = iconConfig[variant];
  
  return (
    <IconComponent
      width={size}
      height={size}
      className={cn('inline-block', className)}
    />
  );
};

// Componente de icono con configuración predefinida
interface AppIconProps {
  icon: AppIconName;
  variant?: IconVariant;
  size?: number;
  className?: string;
}

export const AppIcon: React.FC<AppIconProps> = ({
  icon,
  variant = 'outline',
  size = 24,
  className,
}) => {
  return (
    <Icon
      name={icon}
      variant={variant}
      size={size}
      className={className}
    />
  );
};
```

### **Uso en Componentes**
```tsx
// Ejemplo de uso en navegación
import { AppIcon } from '@/components/Icon';

const Sidebar = () => (
  <nav className="w-64 bg-white border-r border-neutral-200">
    <div className="p-4">
      <div className="space-y-2">
        <a href="/" className="flex items-center px-3 py-2 text-neutral-700 hover:bg-neutral-100 rounded-lg">
          <AppIcon icon="home" size={20} className="mr-3" />
          <span>Inicio</span>
        </a>
        
        <a href="/search" className="flex items-center px-3 py-2 text-neutral-700 hover:bg-neutral-100 rounded-lg">
          <AppIcon icon="search" size={20} className="mr-3" />
          <span>Búsqueda</span>
        </a>
        
        <a href="/profile" className="flex items-center px-3 py-2 text-neutral-700 hover:bg-neutral-100 rounded-lg">
          <AppIcon icon="profile" size={20} className="mr-3" />
          <span>Perfil</span>
        </a>
        
        <a href="/settings" className="flex items-center px-3 py-2 text-neutral-700 hover:bg-neutral-100 rounded-lg">
          <AppIcon icon="settings" size={20} className="mr-3" />
          <span>Configuración</span>
        </a>
      </div>
    </div>
  </nav>
);

// Ejemplo de uso en botones
const ActionButton = ({ onClick, icon, variant = 'outline', children }) => (
  <button
    onClick={onClick}
    className="inline-flex items-center px-4 py-2 bg-mussikon-800 text-white rounded-lg hover:bg-mussikon-700 transition-colors"
  >
    <AppIcon icon={icon} variant={variant} size={20} className="mr-2" />
    {children}
  </button>
);

// Ejemplo de uso en tablas
const RequestTable = ({ requests }) => (
  <table className="min-w-full divide-y divide-neutral-200">
    <thead className="bg-neutral-50">
      <tr>
        <th className="px-6 py-3 text-left text-xs font-medium text-neutral-500 uppercase tracking-wider">
          Estado
        </th>
        <th className="px-6 py-3 text-left text-xs font-medium text-neutral-500 uppercase tracking-wider">
          Título
        </th>
        <th className="px-6 py-3 text-left text-xs font-medium text-neutral-500 uppercase tracking-wider">
          Acciones
        </th>
      </tr>
    </thead>
    <tbody className="bg-white divide-y divide-neutral-200">
      {requests.map((request) => (
        <tr key={request.id}>
          <td className="px-6 py-4 whitespace-nowrap">
            <div className="flex items-center">
              <AppIcon
                icon={getStatusIcon(request.status)}
                variant="solid"
                size={20}
                className={cn(
                  'mr-2',
                  getStatusColor(request.status)
                )}
              />
              <span className="text-sm text-neutral-900">
                {getStatusLabel(request.status)}
              </span>
            </div>
          </td>
          <td className="px-6 py-4 whitespace-nowrap text-sm text-neutral-900">
            {request.title}
          </td>
          <td className="px-6 py-4 whitespace-nowrap text-sm font-medium">
            <div className="flex space-x-2">
              <button className="text-mussikon-600 hover:text-mussikon-900">
                <AppIcon icon="edit" size={16} />
              </button>
              <button className="text-error hover:text-error/80">
                <AppIcon icon="delete" size={16} />
              </button>
            </div>
          </td>
        </tr>
      ))}
    </tbody>
  </table>
);

const getStatusIcon = (status: string): AppIconName => {
  switch (status) {
    case 'pending': return 'pending';
    case 'approved': return 'approved';
    case 'rejected': return 'rejected';
    case 'completed': return 'completed';
    case 'cancelled': return 'cancelled';
    default: return 'pending';
  }
};

const getStatusColor = (status: string): string => {
  switch (status) {
    case 'pending': return 'text-warning';
    case 'approved': return 'text-success';
    case 'rejected': return 'text-error';
    case 'completed': return 'text-success';
    case 'cancelled': return 'text-neutral-500';
    default: return 'text-neutral-500';
  }
};

const getStatusLabel = (status: string): string => {
  switch (status) {
    case 'pending': return 'Pendiente';
    case 'approved': return 'Aprobado';
    case 'rejected': return 'Rechazado';
    case 'completed': return 'Completado';
    case 'cancelled': return 'Cancelado';
    default: return 'Desconocido';
  }
};
```

---

## 🧩 **Biblioteca de Iconos**

### **Estructura de Archivos**
```
src/
├── components/
│   ├── Icon/
│   │   ├── Icon.tsx
│   │   ├── Icon.test.tsx
│   │   └── index.ts
│   └── ui/
├── lib/
│   └── icons.ts
└── types/
    └── icon.ts
```

### **Configuración de Storybook**
```typescript
// Icon.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Icon } from './Icon';
import { appIcons } from '@/lib/icons';

const meta: Meta<typeof Icon> = {
  title: 'UI/Icon',
  component: Icon,
  parameters: {
    layout: 'centered',
  },
  tags: ['autodocs'],
  argTypes: {
    name: {
      control: { type: 'select' },
      options: Object.keys(appIcons),
    },
    variant: {
      control: { type: 'select' },
      options: ['outline', 'solid'],
    },
    size: {
      control: { type: 'range', min: 16, max: 64, step: 4 },
    },
  },
};

export default meta;
type Story = StoryObj<typeof meta>;

export const Default: Story = {
  args: {
    name: 'home',
    variant: 'outline',
    size: 24,
  },
};

export const AllIcons: Story = {
  render: () => (
    <div className="grid grid-cols-6 gap-4 p-6">
      {Object.entries(appIcons).map(([name, variants]) => (
        <div key={name} className="text-center">
          <div className="flex flex-col items-center space-y-2">
            <Icon name={name as keyof typeof appIcons} variant="outline" size={32} />
            <Icon name={name as keyof typeof appIcons} variant="solid" size={32} />
            <span className="text-xs text-neutral-600">{name}</span>
          </div>
        </div>
      ))}
    </div>
  ),
};
```

---

## 🎨 **Sistema de Colores para Iconos**

### **Colores por Contexto**
```typescript
// theme/iconColors.ts
export const iconColors = {
  // Estados
  success: 'text-success',
  warning: 'text-warning',
  error: 'text-error',
  info: 'text-info',
  
  // Acciones
  primary: 'text-mussikon-800',
  secondary: 'text-music-600',
  accent: 'text-energy-500',
  
  // Navegación
  active: 'text-mussikon-800',
  inactive: 'text-neutral-400',
  hover: 'text-mussikon-600',
  
  // Contenido
  text: 'text-neutral-700',
  muted: 'text-neutral-500',
  disabled: 'text-neutral-300',
};
```

---

## 🚀 **Próximos Pasos**

1. **Implementar Iconos Base**: Crear componentes de iconos reutilizables
2. **Configurar Storybook**: Documentar iconos visualmente
3. **Testing de Iconos**: Implementar tests unitarios
4. **Documentación Visual**: Crear ejemplos de uso
5. **Sistema de Colores**: Establecer paleta de colores para iconos

---

*Esta guía de iconografía establece los fundamentos del sistema de iconos de MussikOn, asegurando consistencia visual y facilidad de uso en todas las plataformas.*
