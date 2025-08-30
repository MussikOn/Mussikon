# 🧩 **COMPONENTES UI - MUSSIKON**

## 📋 **Índice de Componentes**

### **🔘 Componentes Base**
- [Sistema de Botones](#-sistema-de-botones)
- [Componentes de Formulario](#-componentes-de-formulario)
- [Componentes de Navegación](#-componentes-de-navegación)
- [Componentes de Layout](#-componentes-de-layout)

### **📱 Aplicación por Plataforma**
- [React Native (Móvil)](#-react-native-móvil)
- [Next.js (Admin)](#-nextjs-admin)
- [Biblioteca de Componentes](#-biblioteca-de-componentes)

---

## 🔘 **Sistema de Botones**

### **Tipos de Botones**
1. **Primary**: Acciones principales (Azul MussikOn)
2. **Secondary**: Acciones secundarias (Verde Música)
3. **Accent**: Acciones destacadas (Naranja Energía)
4. **Ghost**: Botones sin fondo
5. **Danger**: Acciones destructivas (Rojo)
6. **Success**: Acciones exitosas (Verde)
7. **Warning**: Acciones de advertencia (Amarillo)

### **Estados de Botones**
- **Default**: Estado normal
- **Hover**: Al pasar el mouse
- **Active**: Al hacer clic
- **Disabled**: Deshabilitado
- **Loading**: Cargando

### **Tamaños de Botones**
- **Small**: 32px altura
- **Medium**: 40px altura (default)
- **Large**: 48px altura
- **Extra Large**: 56px altura

---

## 📝 **Componentes de Formulario**

### **Inputs**
- **Text Input**: Campo de texto básico
- **Text Area**: Campo de texto multilínea
- **Select**: Lista desplegable
- **Checkbox**: Casilla de verificación
- **Radio Button**: Botón de opción única
- **Toggle**: Interruptor on/off
- **Date Picker**: Selector de fecha
- **Time Picker**: Selector de hora

### **Validación**
- **Real-time**: Validación en tiempo real
- **Error States**: Estados de error visuales
- **Success States**: Estados de éxito
- **Helper Text**: Texto de ayuda
- **Character Count**: Contador de caracteres

---

## 🧭 **Componentes de Navegación**

### **Móvil**
- **Bottom Tabs**: Navegación inferior
- **Stack Navigation**: Navegación jerárquica
- **Drawer**: Menú lateral
- **Tab Bar**: Barra de pestañas
- **Breadcrumbs**: Migas de pan

### **Admin**
- **Sidebar**: Menú lateral
- **Top Navigation**: Navegación superior
- **Breadcrumbs**: Migas de pan
- **Pagination**: Paginación
- **Search Bar**: Barra de búsqueda

---

## 🏗️ **Componentes de Layout**

### **Contenedores**
- **Card**: Tarjeta de contenido
- **Modal**: Ventana modal
- **Drawer**: Panel deslizable
- **Accordion**: Acordeón expandible
- **Tabs**: Pestañas de contenido

### **Grid y Flexbox**
- **Container**: Contenedor principal
- **Row**: Fila de elementos
- **Column**: Columna de elementos
- **Grid**: Sistema de cuadrícula
- **Spacer**: Espaciador

---

## 📱 **React Native (Móvil)**

### **Sistema de Botones**
```typescript
// types/button.ts
export interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'accent' | 'ghost' | 'danger' | 'success' | 'warning';
  size?: 'small' | 'medium' | 'large' | 'xl';
  disabled?: boolean;
  loading?: boolean;
  onPress?: () => void;
  children: React.ReactNode;
  style?: StyleProp<ViewStyle>;
  textStyle?: StyleProp<TextStyle>;
}

// components/Button.tsx
import React from 'react';
import { TouchableOpacity, Text, ActivityIndicator, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'medium',
  disabled = false,
  loading = false,
  onPress,
  children,
  style,
  textStyle,
}) => {
  const buttonStyles = [
    styles.base,
    styles[variant],
    styles[size],
    disabled && styles.disabled,
    style,
  ];

  const textStyles = [
    styles.text,
    styles[`${variant}Text`],
    styles[`${size}Text`],
    disabled && styles.disabledText,
    textStyle,
  ];

  return (
    <TouchableOpacity
      style={buttonStyles}
      onPress={onPress}
      disabled={disabled || loading}
      activeOpacity={0.8}
    >
      {loading ? (
        <ActivityIndicator 
          color={variant === 'ghost' ? colors.primary[800] : colors.white} 
          size="small" 
        />
      ) : (
        <Text style={textStyles}>{children}</Text>
      )}
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  base: {
    borderRadius: 8,
    alignItems: 'center',
    justifyContent: 'center',
    flexDirection: 'row',
  },
  
  // Variantes
  primary: {
    backgroundColor: colors.primary[800],
    borderWidth: 1,
    borderColor: colors.primary[700],
  },
  
  secondary: {
    backgroundColor: colors.secondary[500],
    borderWidth: 1,
    borderColor: colors.secondary[600],
  },
  
  accent: {
    backgroundColor: colors.accent[500],
    borderWidth: 1,
    borderColor: colors.accent[600],
  },
  
  ghost: {
    backgroundColor: 'transparent',
    borderWidth: 1,
    borderColor: colors.primary[300],
  },
  
  danger: {
    backgroundColor: colors.error,
    borderWidth: 1,
    borderColor: colors.error,
  },
  
  success: {
    backgroundColor: colors.success,
    borderWidth: 1,
    borderColor: colors.success,
  },
  
  warning: {
    backgroundColor: colors.warning,
    borderWidth: 1,
    borderColor: colors.warning,
  },
  
  // Tamaños
  small: {
    paddingHorizontal: 16,
    paddingVertical: 8,
    minHeight: 32,
  },
  
  medium: {
    paddingHorizontal: 20,
    paddingVertical: 10,
    minHeight: 40,
  },
  
  large: {
    paddingHorizontal: 24,
    paddingVertical: 12,
    minHeight: 48,
  },
  
  xl: {
    paddingHorizontal: 28,
    paddingVertical: 14,
    minHeight: 56,
  },
  
  // Estados
  disabled: {
    opacity: 0.5,
  },
  
  // Texto
  text: {
    fontFamily: typography.fontFamily.primary,
    fontWeight: typography.fontWeight.medium,
    textAlign: 'center',
  },
  
  primaryText: {
    color: colors.white,
  },
  
  secondaryText: {
    color: colors.white,
  },
  
  accentText: {
    color: colors.white,
  },
  
  ghostText: {
    color: colors.primary[800],
  },
  
  dangerText: {
    color: colors.white,
  },
  
  successText: {
    color: colors.white,
  },
  
  warningText: {
    color: colors.white,
  },
  
  // Tamaños de texto
  smallText: {
    fontSize: typography.fontSize.xs,
  },
  
  mediumText: {
    fontSize: typography.fontSize.sm,
  },
  
  largeText: {
    fontSize: typography.fontSize.base,
  },
  
  xlText: {
    fontSize: typography.fontSize.lg,
  },
  
  disabledText: {
    color: colors.neutral[400],
  },
});
```

### **Componentes de Formulario**
```typescript
// components/Input.tsx
import React, { useState } from 'react';
import { View, Text, TextInput, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

interface InputProps {
  label?: string;
  placeholder?: string;
  value: string;
  onChangeText: (text: string) => void;
  error?: string;
  helperText?: string;
  required?: boolean;
  disabled?: boolean;
  multiline?: boolean;
  numberOfLines?: number;
  secureTextEntry?: boolean;
  keyboardType?: 'default' | 'email-address' | 'numeric' | 'phone-pad';
  autoCapitalize?: 'none' | 'sentences' | 'words' | 'characters';
  autoCorrect?: boolean;
  style?: StyleProp<ViewStyle>;
}

export const Input: React.FC<InputProps> = ({
  label,
  placeholder,
  value,
  onChangeText,
  error,
  helperText,
  required,
  disabled = false,
  multiline = false,
  numberOfLines = 1,
  secureTextEntry = false,
  keyboardType = 'default',
  autoCapitalize = 'sentences',
  autoCorrect = true,
  style,
}) => {
  const [isFocused, setIsFocused] = useState(false);

  const inputStyles = [
    styles.input,
    multiline && styles.multiline,
    isFocused && styles.focused,
    error && styles.error,
    disabled && styles.disabled,
    style,
  ];

  const labelStyles = [
    styles.label,
    required && styles.required,
    error && styles.errorLabel,
  ];

  return (
    <View style={styles.container}>
      {label && (
        <Text style={labelStyles}>
          {label}
          {required && <Text style={styles.requiredMark}> *</Text>}
        </Text>
      )}
      
      <TextInput
        style={inputStyles}
        placeholder={placeholder}
        value={value}
        onChangeText={onChangeText}
        onFocus={() => setIsFocused(true)}
        onBlur={() => setIsFocused(false)}
        editable={!disabled}
        multiline={multiline}
        numberOfLines={numberOfLines}
        secureTextEntry={secureTextEntry}
        keyboardType={keyboardType}
        autoCapitalize={autoCapitalize}
        autoCorrect={autoCorrect}
        placeholderTextColor={colors.neutral[400]}
      />
      
      {(error || helperText) && (
        <Text style={[styles.helperText, error && styles.errorText]}>
          {error || helperText}
        </Text>
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    marginBottom: 16,
  },
  
  label: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.sm,
    fontWeight: typography.fontWeight.medium,
    color: colors.neutral[700],
    marginBottom: 6,
  },
  
  required: {
    color: colors.neutral[800],
  },
  
  requiredMark: {
    color: colors.error,
  },
  
  errorLabel: {
    color: colors.error,
  },
  
  input: {
    borderWidth: 1,
    borderColor: colors.neutral[300],
    borderRadius: 8,
    paddingHorizontal: 16,
    paddingVertical: 12,
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.base,
    color: colors.neutral[800],
    backgroundColor: colors.white,
  },
  
  multiline: {
    textAlignVertical: 'top',
    minHeight: 80,
  },
  
  focused: {
    borderColor: colors.primary[500],
    borderWidth: 2,
  },
  
  error: {
    borderColor: colors.error,
    borderWidth: 2,
  },
  
  disabled: {
    backgroundColor: colors.neutral[100],
    color: colors.neutral[500],
  },
  
  helperText: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.xs,
    color: colors.neutral[500],
    marginTop: 4,
  },
  
  errorText: {
    color: colors.error,
  },
});
```

### **Componentes de Layout**
```typescript
// components/Card.tsx
import React from 'react';
import { View, StyleSheet, ViewStyle } from 'react-native';
import { colors } from '../theme/colors';

interface CardProps {
  children: React.ReactNode;
  style?: ViewStyle;
  padding?: 'small' | 'medium' | 'large';
  elevation?: 'low' | 'medium' | 'high';
}

export const Card: React.FC<CardProps> = ({
  children,
  style,
  padding = 'medium',
  elevation = 'medium',
}) => {
  const cardStyles = [
    styles.base,
    styles[padding],
    styles[elevation],
    style,
  ];

  return <View style={cardStyles}>{children}</View>;
};

const styles = StyleSheet.create({
  base: {
    backgroundColor: colors.white,
    borderRadius: 12,
    borderWidth: 1,
    borderColor: colors.neutral[200],
  },
  
  // Padding
  small: {
    padding: 16,
  },
  
  medium: {
    padding: 20,
  },
  
  large: {
    padding: 24,
  },
  
  // Elevación
  low: {
    shadowColor: colors.black,
    shadowOffset: { width: 0, height: 1 },
    shadowOpacity: 0.05,
    shadowRadius: 2,
    elevation: 1,
  },
  
  medium: {
    shadowColor: colors.black,
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
    elevation: 3,
  },
  
  high: {
    shadowColor: colors.black,
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.15,
    shadowRadius: 8,
    elevation: 6,
  },
});
```

---

## 🖥️ **Next.js (Admin)**

### **Sistema de Botones**
```tsx
// components/Button.tsx
import React from 'react';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-lg font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none',
  {
    variants: {
      variant: {
        primary: 'bg-mussikon-800 text-white hover:bg-mussikon-700 border border-mussikon-700',
        secondary: 'bg-music-500 text-white hover:bg-music-600 border border-music-600',
        accent: 'bg-energy-500 text-white hover:bg-energy-600 border border-energy-600',
        ghost: 'bg-transparent text-mussikon-800 hover:bg-mussikon-50 border border-mussikon-300',
        danger: 'bg-error text-white hover:bg-error/90 border border-error',
        success: 'bg-success text-white hover:bg-success/90 border border-success',
        warning: 'bg-warning text-white hover:bg-warning/90 border border-warning',
      },
      size: {
        small: 'h-8 px-3 text-xs',
        medium: 'h-10 px-4 text-sm',
        large: 'h-12 px-6 text-base',
        xl: 'h-14 px-8 text-lg',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'medium',
    },
  }
);

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  loading?: boolean;
}

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, loading, children, disabled, ...props }, ref) => {
    return (
      <button
        className={cn(buttonVariants({ variant, size, className }))}
        ref={ref}
        disabled={disabled || loading}
        {...props}
      >
        {loading ? (
          <div className="mr-2 h-4 w-4 animate-spin rounded-full border-2 border-current border-t-transparent" />
        ) : null}
        {children}
      </button>
    );
  }
);

Button.displayName = 'Button';

export { Button, buttonVariants };
```

### **Componentes de Formulario**
```tsx
// components/Input.tsx
import React from 'react';
import { cn } from '@/lib/utils';

export interface InputProps
  extends React.InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  error?: string;
  helperText?: string;
  required?: boolean;
}

const Input = React.forwardRef<HTMLInputElement, InputProps>(
  ({ className, type, label, error, helperText, required, ...props }, ref) => {
    return (
      <div className="space-y-2">
        {label && (
          <label className="text-sm font-medium text-neutral-700">
            {label}
            {required && <span className="text-error ml-1">*</span>}
          </label>
        )}
        
        <input
          type={type}
          className={cn(
            'flex h-10 w-full rounded-lg border border-neutral-300 bg-white px-3 py-2 text-sm',
            'placeholder:text-neutral-400',
            'focus:outline-none focus:ring-2 focus:ring-mussikon-500 focus:border-transparent',
            'disabled:cursor-not-allowed disabled:opacity-50',
            error && 'border-error focus:ring-error',
            className
          )}
          ref={ref}
          {...props}
        />
        
        {(error || helperText) && (
          <p className={cn(
            'text-xs',
            error ? 'text-error' : 'text-neutral-500'
          )}>
            {error || helperText}
          </p>
        )}
      </div>
    );
  }
);

Input.displayName = 'Input';

export { Input };
```

### **Componentes de Layout**
```tsx
// components/Card.tsx
import React from 'react';
import { cn } from '@/lib/utils';

interface CardProps extends React.HTMLAttributes<HTMLDivElement> {
  padding?: 'small' | 'medium' | 'large';
  elevation?: 'low' | 'medium' | 'high';
}

const Card = React.forwardRef<HTMLDivElement, CardProps>(
  ({ className, padding = 'medium', elevation = 'medium', ...props }, ref) => {
    const paddingClasses = {
      small: 'p-4',
      medium: 'p-6',
      large: 'p-8',
    };

    const elevationClasses = {
      low: 'shadow-sm',
      medium: 'shadow-md',
      high: 'shadow-lg',
    };

    return (
      <div
        ref={ref}
        className={cn(
          'rounded-xl border border-neutral-200 bg-white',
          paddingClasses[padding],
          elevationClasses[elevation],
          className
        )}
        {...props}
      />
    );
  }
);

Card.displayName = 'Card';

export { Card };
```

---

## 🧩 **Biblioteca de Componentes**

### **Estructura de Archivos**
```
src/
├── components/
│   ├── ui/
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.test.tsx
│   │   │   └── index.ts
│   │   ├── Input/
│   │   │   ├── Input.tsx
│   │   │   ├── Input.test.tsx
│   │   │   └── index.ts
│   │   ├── Card/
│   │   │   ├── Card.tsx
│   │   │   ├── Card.test.tsx
│   │   │   └── index.ts
│   │   └── index.ts
│   ├── forms/
│   │   ├── FormField.tsx
│   │   ├── FormSection.tsx
│   │   └── index.ts
│   ├── layout/
│   │   ├── Container.tsx
│   │   ├── Grid.tsx
│   │   └── index.ts
│   └── navigation/
│       ├── Sidebar.tsx
│       ├── TopNav.tsx
│       └── index.ts
```

### **Storybook Configuration**
```typescript
// .storybook/main.ts
import type { StorybookConfig } from '@storybook/nextjs';

const config: StorybookConfig = {
  stories: ['../src/**/*.mdx', '../src/**/*.stories.@(js|jsx|mjs|ts|tsx)'],
  addons: [
    '@storybook/addon-links',
    '@storybook/addon-essentials',
    '@storybook/addon-onboarding',
    '@storybook/addon-interactions',
    '@storybook/addon-a11y',
    '@storybook/addon-themes',
  ],
  framework: {
    name: '@storybook/nextjs',
    options: {},
  },
  docs: {
    autodocs: 'tag',
  },
};

export default config;
```

### **Ejemplo de Story**
```tsx
// Button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'UI/Button',
  component: Button,
  parameters: {
    layout: 'centered',
  },
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: { type: 'select' },
      options: ['primary', 'secondary', 'accent', 'ghost', 'danger', 'success', 'warning'],
    },
    size: {
      control: { type: 'select' },
      options: ['small', 'medium', 'large', 'xl'],
    },
  },
};

export default meta;
type Story = StoryObj<typeof meta>;

export const Primary: Story = {
  args: {
    variant: 'primary',
    children: 'Button',
  },
};

export const Secondary: Story = {
  args: {
    variant: 'secondary',
    children: 'Button',
  },
};

export const Large: Story = {
  args: {
    size: 'large',
    children: 'Button',
  },
};

export const Loading: Story = {
  args: {
    loading: true,
    children: 'Button',
  },
};
```

---

## 🎨 **Sistema de Diseño**

### **Tokens de Diseño**
```typescript
// design-tokens/index.ts
export const designTokens = {
  colors: {
    primary: {
      50: '#EFF6FF',
      100: '#DBEAFE',
      // ... resto de colores
    },
    // ... otros colores
  },
  
  spacing: {
    xs: '4px',
    sm: '8px',
    md: '16px',
    lg: '24px',
    xl: '32px',
    '2xl': '48px',
    '3xl': '64px',
  },
  
  borderRadius: {
    none: '0px',
    sm: '4px',
    md: '8px',
    lg: '12px',
    xl: '16px',
    full: '9999px',
  },
  
  shadows: {
    sm: '0 1px 2px 0 rgb(0 0 0 / 0.05)',
    md: '0 4px 6px -1px rgb(0 0 0 / 0.1)',
    lg: '0 10px 15px -3px rgb(0 0 0 / 0.1)',
  },
  
  transitions: {
    fast: '150ms ease-in-out',
    normal: '250ms ease-in-out',
    slow: '350ms ease-in-out',
  },
};
```

---

## 🚀 **Próximos Pasos**

1. **Implementar Componentes Base**: Crear botones, inputs y cards
2. **Configurar Storybook**: Documentar componentes visualmente
3. **Testing de Componentes**: Implementar tests unitarios
4. **Documentación Visual**: Crear ejemplos de uso
5. **Sistema de Diseño**: Establecer tokens de diseño

---

*Esta guía de componentes UI establece los fundamentos del sistema de diseño de MussikOn, asegurando consistencia y reutilización en todas las plataformas.*
