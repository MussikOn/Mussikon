# 📝 **TIPOGRAFÍA - MUSSIKON**

## 📋 **Índice de Tipografía**

### **🔤 Sistema de Fuentes**
- [Familias de Fuentes](#-familias-de-fuentes)
- [Jerarquía Tipográfica](#-jerarquía-tipográfica)
- [Tamaños y Escalas](#-tamaños-y-escalas)
- [Pesos de Fuente](#-pesos-de-fuente)

### **📱 Aplicación por Plataforma**
- [React Native (Móvil)](#-react-native-móvil)
- [Next.js (Admin)](#-nextjs-admin)
- [Componentes](#-componentes-y-uso)

---

## 🔤 **Familias de Fuentes**

### **Fuente Principal: Inter**
```css
/* CSS Variables */
--font-family-primary: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
--font-family-mono: 'JetBrains Mono', 'Fira Code', 'Monaco', 'Cascadia Code', monospace;
--font-family-display: 'Inter', 'SF Pro Display', 'Segoe UI Display', sans-serif;
```

**Características:**
- **Inter**: Fuente moderna y legible para interfaces
- **JetBrains Mono**: Fuente monoespaciada para código
- **Fallbacks**: Fuentes del sistema para compatibilidad

### **Fuentes Alternativas**
```css
/* Fuentes de respaldo */
--font-fallback-system: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
--font-fallback-mono: 'SF Mono', Monaco, 'Cascadia Code', 'Roboto Mono', Consolas, 'Courier New', monospace;
```

---

## 📊 **Jerarquía Tipográfica**

### **Niveles de Jerarquía**
1. **H1**: Títulos principales de página
2. **H2**: Títulos de sección
3. **H3**: Títulos de subsección
4. **H4**: Títulos de grupo
5. **Body Large**: Texto de párrafo principal
6. **Body Medium**: Texto de párrafo secundario
7. **Body Small**: Texto de nota o pie
8. **Caption**: Texto de etiqueta o metadata
9. **Button**: Texto de botón
10. **Input**: Texto de campo de entrada

### **Mapeo de Elementos HTML**
```html
<!-- Estructura semántica -->
<h1>MussikOn - Sistema de Solicitudes</h1>
<h2>Gestión de Músicos</h2>
<h3>Crear Nueva Solicitud</h3>
<h4>Información del Evento</h4>
<p class="body-large">Descripción principal del contenido...</p>
<p class="body-medium">Información adicional...</p>
<p class="body-small">Nota importante...</p>
<span class="caption">Fecha de creación</span>
<button class="button">Crear Solicitud</button>
<input class="input" placeholder="Buscar músicos..." />
```

---

## 📏 **Tamaños y Escalas**

### **Escala de Tamaños (Sistema 8pt)**
```css
/* CSS Variables para tamaños */
--font-size-xs: 0.75rem;    /* 12px */
--font-size-sm: 0.875rem;   /* 14px */
--font-size-base: 1rem;     /* 16px */
--font-size-lg: 1.125rem;   /* 18px */
--font-size-xl: 1.25rem;    /* 20px */
--font-size-2xl: 1.5rem;    /* 24px */
--font-size-3xl: 1.875rem;  /* 30px */
--font-size-4xl: 2.25rem;   /* 36px */
--font-size-5xl: 3rem;      /* 48px */
--font-size-6xl: 3.75rem;   /* 60px */
--font-size-7xl: 4.5rem;    /* 72px */
--font-size-8xl: 6rem;      /* 96px */
--font-size-9xl: 8rem;      /* 128px */
```

### **Aplicación por Jerarquía**
```css
/* H1 - Título principal */
--h1-font-size: var(--font-size-4xl);      /* 36px */
--h1-line-height: 1.2;
--h1-letter-spacing: -0.025em;

/* H2 - Título de sección */
--h2-font-size: var(--font-size-3xl);      /* 30px */
--h2-line-height: 1.25;
--h2-letter-spacing: -0.025em;

/* H3 - Título de subsección */
--h3-font-size: var(--font-size-2xl);      /* 24px */
--h3-line-height: 1.3;
--h3-letter-spacing: -0.025em;

/* H4 - Título de grupo */
--h4-font-size: var(--font-size-xl);       /* 20px */
--h4-line-height: 1.4;
--h4-letter-spacing: -0.025em;

/* Body Large - Párrafo principal */
--body-large-font-size: var(--font-size-lg); /* 18px */
--body-large-line-height: 1.6;
--body-large-letter-spacing: 0em;

/* Body Medium - Párrafo secundario */
--body-medium-font-size: var(--font-size-base); /* 16px */
--body-medium-line-height: 1.6;
--body-medium-letter-spacing: 0em;

/* Body Small - Nota */
--body-small-font-size: var(--font-size-sm);   /* 14px */
--body-small-line-height: 1.5;
--body-small-letter-spacing: 0em;

/* Caption - Etiqueta */
--caption-font-size: var(--font-size-xs);      /* 12px */
--caption-line-height: 1.4;
--caption-letter-spacing: 0.025em;

/* Button - Botón */
--button-font-size: var(--font-size-sm);       /* 14px */
--button-line-height: 1.4;
--button-letter-spacing: 0.025em;

/* Input - Campo de entrada */
--input-font-size: var(--font-size-base);      /* 16px */
--input-line-height: 1.5;
--input-letter-spacing: 0em;
```

---

## ⚖️ **Pesos de Fuente**

### **Escala de Pesos**
```css
/* CSS Variables para pesos */
--font-weight-thin: 100;
--font-weight-extralight: 200;
--font-weight-light: 300;
--font-weight-normal: 400;
--font-weight-medium: 500;
--font-weight-semibold: 600;
--font-weight-bold: 700;
--font-weight-extrabold: 800;
--font-weight-black: 900;
```

### **Aplicación por Jerarquía**
```css
/* H1 - Título principal */
--h1-font-weight: var(--font-weight-bold);      /* 700 */

/* H2 - Título de sección */
--h2-font-weight: var(--font-weight-semibold);  /* 600 */

/* H3 - Título de subsección */
--h3-font-weight: var(--font-weight-semibold);  /* 600 */

/* H4 - Título de grupo */
--h4-font-weight: var(--font-weight-medium);    /* 500 */

/* Body Large - Párrafo principal */
--body-large-font-weight: var(--font-weight-normal); /* 400 */

/* Body Medium - Párrafo secundario */
--body-medium-font-weight: var(--font-weight-normal); /* 400 */

/* Body Small - Nota */
--body-small-font-weight: var(--font-weight-normal); /* 400 */

/* Caption - Etiqueta */
--caption-font-weight: var(--font-weight-medium); /* 500 */

/* Button - Botón */
--button-font-weight: var(--font-weight-medium); /* 500 */

/* Input - Campo de entrada */
--input-font-weight: var(--font-weight-normal); /* 400 */
```

---

## 📱 **React Native (Móvil)**

### **Configuración del Tema**
```typescript
// theme/typography.ts
export const typography = {
  // Familias de fuentes
  fontFamily: {
    primary: 'Inter',
    mono: 'JetBrainsMono-Regular',
    display: 'Inter',
  },
  
  // Tamaños de fuente
  fontSize: {
    xs: 12,
    sm: 14,
    base: 16,
    lg: 18,
    xl: 20,
    '2xl': 24,
    '3xl': 30,
    '4xl': 36,
    '5xl': 48,
    '6xl': 60,
    '7xl': 72,
    '8xl': 96,
    '9xl': 128,
  },
  
  // Pesos de fuente
  fontWeight: {
    thin: '100',
    extralight: '200',
    light: '300',
    normal: '400',
    medium: '500',
    semibold: '600',
    bold: '700',
    extrabold: '800',
    black: '900',
  },
  
  // Alturas de línea
  lineHeight: {
    none: 1,
    tight: 1.2,
    snug: 1.25,
    normal: 1.5,
    relaxed: 1.6,
    loose: 2,
  },
  
  // Espaciado de letras
  letterSpacing: {
    tighter: -0.05,
    tight: -0.025,
    normal: 0,
    wide: 0.025,
    wider: 0.05,
    widest: 0.1,
  },
};
```

### **Estilos de Texto**
```typescript
// Componentes de texto tipográfico
export const textStyles = StyleSheet.create({
  // Títulos
  h1: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize['4xl'],
    fontWeight: typography.fontWeight.bold,
    lineHeight: typography.lineHeight.tight,
    letterSpacing: typography.letterSpacing.tight,
    color: colors.text.primary,
  },
  
  h2: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize['3xl'],
    fontWeight: typography.fontWeight.semibold,
    lineHeight: typography.lineHeight.snug,
    letterSpacing: typography.letterSpacing.tight,
    color: colors.text.primary,
  },
  
  h3: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize['2xl'],
    fontWeight: typography.fontWeight.semibold,
    lineHeight: typography.lineHeight.snug,
    letterSpacing: typography.letterSpacing.tight,
    color: colors.text.primary,
  },
  
  h4: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.xl,
    fontWeight: typography.fontWeight.medium,
    lineHeight: typography.lineHeight.normal,
    letterSpacing: typography.letterSpacing.tight,
    color: colors.text.primary,
  },
  
  // Párrafos
  bodyLarge: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.lg,
    fontWeight: typography.fontWeight.normal,
    lineHeight: typography.lineHeight.relaxed,
    letterSpacing: typography.letterSpacing.normal,
    color: colors.text.primary,
  },
  
  bodyMedium: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.base,
    fontWeight: typography.fontWeight.normal,
    lineHeight: typography.lineHeight.relaxed,
    letterSpacing: typography.letterSpacing.normal,
    color: colors.text.primary,
  },
  
  bodySmall: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.sm,
    fontWeight: typography.fontWeight.normal,
    lineHeight: typography.lineHeight.normal,
    letterSpacing: typography.letterSpacing.normal,
    color: colors.text.secondary,
  },
  
  // Elementos especiales
  caption: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.xs,
    fontWeight: typography.fontWeight.medium,
    lineHeight: typography.lineHeight.normal,
    letterSpacing: typography.letterSpacing.wide,
    color: colors.text.secondary,
  },
  
  button: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.sm,
    fontWeight: typography.fontWeight.medium,
    lineHeight: typography.lineHeight.normal,
    letterSpacing: typography.letterSpacing.wide,
    color: colors.text.white,
  },
  
  input: {
    fontFamily: typography.fontFamily.primary,
    fontSize: typography.fontSize.base,
    fontWeight: typography.fontWeight.normal,
    lineHeight: typography.lineHeight.normal,
    letterSpacing: typography.letterSpacing.normal,
    color: colors.text.primary,
  },
});
```

### **Componentes de Texto**
```typescript
// Componentes tipográficos reutilizables
import { Text, TextProps } from 'react-native';
import { textStyles } from './textStyles';

export const H1: React.FC<TextProps> = ({ children, style, ...props }) => (
  <Text style={[textStyles.h1, style]} {...props}>
    {children}
  </Text>
);

export const H2: React.FC<TextProps> = ({ children, style, ...props }) => (
  <Text style={[textStyles.h2, style]} {...props}>
    {children}
  </Text>
);

export const H3: React.FC<TextProps> = ({ children, style, ...props }) => (
  <Text style={[textStyles.h3, style]} {...props}>
    {children}
  </Text>
);

export const H4: React.FC<TextProps> = ({ children, style, ...props }) => (
  <Text style={[textStyles.h4, style]} {...props}>
    {children}
  </Text>
);

export const BodyLarge: React.FC<TextProps> = ({ children, style, ...props }) => (
  <Text style={[textStyles.bodyLarge, style]} {...props}>
    {children}
  </Text>
);

export const BodyMedium: React.FC<TextProps> = ({ children, style, ...props }) => (
  <Text style={[textStyles.bodyMedium, style]} {...props}>
    {children}
  </Text>
);

export const BodySmall: React.FC<TextProps> = ({ children, style, ...props }) => (
  <Text style={[textStyles.bodySmall, style]} {...props}>
    {children}
  </Text>
);

export const Caption: React.FC<TextProps> = ({ children, style, ...props }) => (
  <Text style={[textStyles.caption, style]} {...props}>
    {children}
  </Text>
);
```

---

## 🖥️ **Next.js (Admin)**

### **Configuración de Tailwind CSS**
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      fontFamily: {
        sans: ['Inter', 'ui-sans-serif', 'system-ui', 'sans-serif'],
        mono: ['JetBrains Mono', 'ui-monospace', 'monospace'],
        display: ['Inter', 'ui-sans-serif', 'system-ui', 'sans-serif'],
      },
      fontSize: {
        'xs': ['0.75rem', { lineHeight: '1rem' }],
        'sm': ['0.875rem', { lineHeight: '1.25rem' }],
        'base': ['1rem', { lineHeight: '1.5rem' }],
        'lg': ['1.125rem', { lineHeight: '1.75rem' }],
        'xl': ['1.25rem', { lineHeight: '1.75rem' }],
        '2xl': ['1.5rem', { lineHeight: '2rem' }],
        '3xl': ['1.875rem', { lineHeight: '2.25rem' }],
        '4xl': ['2.25rem', { lineHeight: '2.5rem' }],
        '5xl': ['3rem', { lineHeight: '1' }],
        '6xl': ['3.75rem', { lineHeight: '1' }],
        '7xl': ['4.5rem', { lineHeight: '1' }],
        '8xl': ['6rem', { lineHeight: '1' }],
        '9xl': ['8rem', { lineHeight: '1' }],
      },
      fontWeight: {
        thin: '100',
        extralight: '200',
        light: '300',
        normal: '400',
        medium: '500',
        semibold: '600',
        bold: '700',
        extrabold: '800',
        black: '900',
      },
      lineHeight: {
        'none': '1',
        'tight': '1.2',
        'snug': '1.25',
        'normal': '1.5',
        'relaxed': '1.6',
        'loose': '2',
      },
      letterSpacing: {
        'tighter': '-0.05em',
        'tight': '-0.025em',
        'normal': '0em',
        'wide': '0.025em',
        'wider': '0.05em',
        'widest': '0.1em',
      },
    },
  },
};
```

### **CSS Variables Globales**
```css
/* globals.css */
:root {
  /* Familias de fuentes */
  --font-family-primary: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  --font-family-mono: 'JetBrains Mono', 'Fira Code', 'Monaco', 'Cascadia Code', monospace;
  --font-family-display: 'Inter', 'SF Pro Display', 'Segoe UI Display', sans-serif;
  
  /* Tamaños de fuente */
  --font-size-xs: 0.75rem;
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.125rem;
  --font-size-xl: 1.25rem;
  --font-size-2xl: 1.5rem;
  --font-size-3xl: 1.875rem;
  --font-size-4xl: 2.25rem;
  --font-size-5xl: 3rem;
  --font-size-6xl: 3.75rem;
  --font-size-7xl: 4.5rem;
  --font-size-8xl: 6rem;
  --font-size-9xl: 8rem;
  
  /* Pesos de fuente */
  --font-weight-thin: 100;
  --font-weight-extralight: 200;
  --font-weight-light: 300;
  --font-weight-normal: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;
  --font-weight-extrabold: 800;
  --font-weight-black: 900;
  
  /* Alturas de línea */
  --line-height-none: 1;
  --line-height-tight: 1.2;
  --line-height-snug: 1.25;
  --line-height-normal: 1.5;
  --line-height-relaxed: 1.6;
  --line-height-loose: 2;
  
  /* Espaciado de letras */
  --letter-spacing-tighter: -0.05em;
  --letter-spacing-tight: -0.025em;
  --letter-spacing-normal: 0em;
  --letter-spacing-wide: 0.025em;
  --letter-spacing-wider: 0.05em;
  --letter-spacing-widest: 0.1em;
}
```

### **Clases de Tailwind Personalizadas**
```css
/* typography.css */
/* Títulos */
.text-h1 {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-4xl);
  font-weight: var(--font-weight-bold);
  line-height: var(--line-height-tight);
  letter-spacing: var(--letter-spacing-tight);
}

.text-h2 {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-3xl);
  font-weight: var(--font-weight-semibold);
  line-height: var(--line-height-snug);
  letter-spacing: var(--letter-spacing-tight);
}

.text-h3 {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-2xl);
  font-weight: var(--font-weight-semibold);
  line-height: var(--line-height-snug);
  letter-spacing: var(--letter-spacing-tight);
}

.text-h4 {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-xl);
  font-weight: var(--font-weight-medium);
  line-height: var(--line-height-normal);
  letter-spacing: var(--letter-spacing-tight);
}

/* Párrafos */
.text-body-large {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-lg);
  font-weight: var(--font-weight-normal);
  line-height: var(--line-height-relaxed);
  letter-spacing: var(--letter-spacing-normal);
}

.text-body-medium {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-base);
  font-weight: var(--font-weight-normal);
  line-height: var(--line-height-relaxed);
  letter-spacing: var(--letter-spacing-normal);
}

.text-body-small {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-sm);
  font-weight: var(--font-weight-normal);
  line-height: var(--line-height-normal);
  letter-spacing: var(--letter-spacing-normal);
}

/* Elementos especiales */
.text-caption {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-xs);
  font-weight: var(--font-weight-medium);
  line-height: var(--line-height-normal);
  letter-spacing: var(--letter-spacing-wide);
}

.text-button {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-sm);
  font-weight: var(--font-weight-medium);
  line-height: var(--line-height-normal);
  letter-spacing: var(--letter-spacing-wide);
}

.text-input {
  font-family: var(--font-family-primary);
  font-size: var(--font-size-base);
  font-weight: var(--font-weight-normal);
  line-height: var(--line-height-normal);
  letter-spacing: var(--letter-spacing-normal);
}
```

### **Componentes de Texto**
```tsx
// Componentes tipográficos reutilizables
interface TypographyProps {
  children: React.ReactNode;
  className?: string;
  as?: keyof JSX.IntrinsicElements;
}

export const H1: React.FC<TypographyProps> = ({ 
  children, 
  className = '', 
  as: Component = 'h1',
  ...props 
}) => (
  <Component 
    className={`text-h1 text-mussikon-800 ${className}`}
    {...props}
  >
    {children}
  </Component>
);

export const H2: React.FC<TypographyProps> = ({ 
  children, 
  className = '', 
  as: Component = 'h2',
  ...props 
}) => (
  <Component 
    className={`text-h2 text-mussikon-800 ${className}`}
    {...props}
  >
    {children}
  </Component>
);

export const H3: React.FC<TypographyProps> = ({ 
  children, 
  className = '', 
  as: Component = 'h3',
  ...props 
}) => (
  <Component 
    className={`text-h3 text-mussikon-800 ${className}`}
    {...props}
  >
    {children}
  </Component>
);

export const H4: React.FC<TypographyProps> = ({ 
  children, 
  className = '', 
  as: Component = 'h4',
  ...props 
}) => (
  <Component 
    className={`text-h4 text-mussikon-800 ${className}`}
    {...props}
  >
    {children}
  </Component>
);

export const BodyLarge: React.FC<TypographyProps> = ({ 
  children, 
  className = '', 
  as: Component = 'p',
  ...props 
}) => (
  <Component 
    className={`text-body-large text-neutral-800 ${className}`}
    {...props}
  >
    {children}
  </Component>
);

export const BodyMedium: React.FC<TypographyProps> = ({ 
  children, 
  className = '', 
  as: Component = 'p',
  ...props 
}) => (
  <Component 
    className={`text-body-medium text-neutral-800 ${className}`}
    {...props}
  >
    {children}
  </Component>
);

export const BodySmall: React.FC<TypographyProps> = ({ 
  children, 
  className = '', 
  as: Component = 'p',
  ...props 
}) => (
  <Component 
    className={`text-body-small text-neutral-600 ${className}`}
    {...props}
  >
    {children}
  </Component>
);

export const Caption: React.FC<TypographyProps> = ({ 
  children, 
  className = '', 
  as: Component = 'span',
  ...props 
}) => (
  <Component 
    className={`text-caption text-neutral-500 ${className}`}
    {...props}
  >
    {children}
  </Component>
);
```

---

## 🎨 **Componentes y Uso**

### **Ejemplos de Uso**
```tsx
// Ejemplo de página con jerarquía tipográfica
const MusicianRequestPage = () => (
  <div className="max-w-4xl mx-auto p-6">
    <H1>Nueva Solicitud de Músico</H1>
    
    <BodyLarge className="mt-4">
      Completa el formulario para solicitar músicos para tu evento.
    </BodyLarge>
    
    <div className="mt-8">
      <H2>Información del Evento</H2>
      
      <div className="mt-4">
        <H4>Detalles Básicos</H4>
        <BodyMedium className="mt-2">
          Proporciona información esencial sobre tu evento.
        </BodyMedium>
      </div>
      
      <div className="mt-6">
        <H4>Requerimientos Musicales</H4>
        <BodyMedium className="mt-2">
          Especifica qué tipo de música necesitas.
        </BodyMedium>
        <Caption className="mt-1">
          * Los campos marcados son obligatorios
        </Caption>
      </div>
    </div>
  </div>
);
```

### **Responsive Typography**
```css
/* Responsive typography para diferentes tamaños de pantalla */
@media (max-width: 640px) {
  .text-h1 {
    font-size: var(--font-size-3xl);
  }
  
  .text-h2 {
    font-size: var(--font-size-2xl);
  }
  
  .text-h3 {
    font-size: var(--font-size-xl);
  }
  
  .text-body-large {
    font-size: var(--font-size-base);
  }
}

@media (max-width: 768px) {
  .text-h1 {
    font-size: var(--font-size-4xl);
  }
  
  .text-h2 {
    font-size: var(--font-size-3xl);
  }
}
```

---

## 📊 **Accesibilidad y Legibilidad**

### **Contraste y Legibilidad**
- **Contraste mínimo**: 4.5:1 para texto normal
- **Tamaño mínimo**: 16px para texto de lectura
- **Altura de línea**: 1.5 para párrafos largos
- **Espaciado**: 0.025em para títulos

### **Navegación por Teclado**
- **Focus visible**: Contorno claro en elementos interactivos
- **Skip links**: Enlaces para saltar contenido
- **Landmarks**: Estructura semántica clara

---

## 🚀 **Próximos Pasos**

1. **Implementar Fuentes**: Configurar Inter y JetBrains Mono en ambos proyectos
2. **Crear Componentes**: Desarrollar componentes tipográficos reutilizables
3. **Testing de Accesibilidad**: Verificar legibilidad y contraste
4. **Documentación Visual**: Crear ejemplos visuales de uso
5. **Guía de Marca**: Integrar con la guía de marca de MussikOn

---

*Esta guía de tipografía establece los fundamentos del sistema de texto de MussikOn, asegurando consistencia, legibilidad y accesibilidad en todas las plataformas.*
