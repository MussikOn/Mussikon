# ⚙️ **CONFIGURACIÓN ADMIN - FRONTEND MUSSIKON**

## 📋 **Índice de Configuración Admin**

### **🔧 Configuración del Entorno**
- [Variables de Entorno](./CONFIGURACION_ADMIN.md#variables-de-entorno) - Configuración del sistema
- [Dependencias](./CONFIGURACION_ADMIN.md#dependencias) - Paquetes npm y configuración
- [Next.js](./CONFIGURACION_ADMIN.md#nextjs) - Configuración de Next.js

### **🚀 Build y Despliegue**
- [Vercel](./CONFIGURACION_ADMIN.md#vercel) - Configuración de Vercel
- [Docker](./CONFIGURACION_ADMIN.md#docker) - Contenedores
- [CI/CD](./CONFIGURACION_ADMIN.md#cicd) - Pipeline de integración continua

---

## 🎯 **Propósito de la Configuración Admin**

### **Objetivo Principal**
Establecer la configuración completa del entorno de desarrollo, testing y build para el panel de administración Next.js de MussikOn, asegurando consistencia y facilidad de despliegue.

### **Principios Clave**
- **Performance**: Optimización para aplicaciones web empresariales
- **Seguridad**: Variables sensibles protegidas
- **Escalabilidad**: Preparada para crecer con el negocio
- **Profesional**: Interfaz confiable para administradores

---

## 🔧 **Variables de Entorno**

### **📁 Archivo .env.local**
```bash
# API Configuration
NEXT_PUBLIC_API_URL=https://api.mussikon.com
NEXT_PUBLIC_API_VERSION=v1
NEXT_PUBLIC_WS_URL=wss://api.mussikon.com

# Supabase Configuration
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key

# Authentication
NEXTAUTH_URL=https://admin.mussikon.com
NEXTAUTH_SECRET=your-nextauth-secret-key-here
NEXTAUTH_GITHUB_ID=your-github-oauth-id
NEXTAUTH_GITHUB_SECRET=your-github-oauth-secret

# Analytics
NEXT_PUBLIC_ANALYTICS_ENABLED=true
NEXT_PUBLIC_ANALYTICS_KEY=your-analytics-key

# Feature Flags
NEXT_PUBLIC_ENABLE_REAL_TIME_UPDATES=true
NEXT_PUBLIC_ENABLE_ADVANCED_ANALYTICS=true
NEXT_PUBLIC_ENABLE_USER_MANAGEMENT=true

# Build Configuration
NEXT_PUBLIC_APP_VERSION=1.0.0
NEXT_PUBLIC_BUILD_ENVIRONMENT=production
```

### **📁 next.config.js**
```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    appDir: true,
  },
  images: {
    domains: ['mussikon.com', 'api.mussikon.com'],
    formats: ['image/webp', 'image/avif'],
  },
  env: {
    CUSTOM_KEY: process.env.CUSTOM_KEY,
  },
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'X-Frame-Options',
            value: 'DENY',
          },
          {
            key: 'X-Content-Type-Options',
            value: 'nosniff',
          },
          {
            key: 'Referrer-Policy',
            value: 'origin-when-cross-origin',
          },
        ],
      },
    ];
  },
  async redirects() {
    return [
      {
        source: '/',
        destination: '/dashboard',
        permanent: true,
      },
    ];
  },
  webpack: (config, { buildId, dev, isServer, defaultLoaders, webpack }) => {
    config.resolve.fallback = {
      ...config.resolve.fallback,
      fs: false,
    };
    return config;
  },
};

module.exports = nextConfig;
```

---

## 📦 **Dependencias**

### **🔧 package.json**
```json
{
  "name": "mussikon-admin",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "lint:fix": "next lint --fix",
    "type-check": "tsc --noEmit",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "e2e": "cypress run",
    "e2e:open": "cypress open",
    "storybook": "storybook dev -p 6006",
    "build-storybook": "storybook build"
  },
  "dependencies": {
    "next": "14.0.0",
    "react": "18.3.1",
    "react-dom": "18.3.1",
    "typescript": "5.8.3",
    "@types/node": "20.0.0",
    "@types/react": "18.3.1",
    "@types/react-dom": "18.3.1",
    
    "tailwindcss": "3.4.0",
    "autoprefixer": "10.4.16",
    "postcss": "8.4.32",
    
    "@headlessui/react": "1.7.17",
    "@radix-ui/react-dialog": "1.0.5",
    "@radix-ui/react-dropdown-menu": "2.0.6",
    "@radix-ui/react-select": "2.0.0",
    "@radix-ui/react-tabs": "1.0.4",
    "@radix-ui/react-toast": "1.1.5",
    
    "framer-motion": "10.16.16",
    "react-hook-form": "7.48.2",
    "@hookform/resolvers": "3.3.2",
    "zod": "3.22.4",
    
    "@tanstack/react-query": "5.8.4",
    "zustand": "4.4.7",
    
    "next-auth": "4.24.5",
    "axios": "1.6.2",
    "socket.io-client": "4.8.1",
    
    "react-table": "7.8.0",
    "recharts": "2.8.0",
    "chart.js": "4.4.0",
    "react-chartjs-2": "5.2.0",
    
    "react-hot-toast": "2.4.1",
    "clsx": "2.0.0",
    "class-variance-authority": "0.7.0",
    "lucide-react": "0.294.0",
    
    "next-i18next": "15.1.1",
    "i18next": "23.7.6",
    "react-i18next": "13.5.0"
  },
  "devDependencies": {
    "eslint": "8.55.0",
    "eslint-config-next": "14.0.0",
    "@typescript-eslint/eslint-plugin": "6.13.1",
    "@typescript-eslint/parser": "6.13.1",
    "eslint-plugin-react": "7.33.2",
    "eslint-plugin-react-hooks": "4.6.0",
    
    "jest": "29.7.0",
    "@testing-library/react": "14.1.2",
    "@testing-library/jest-dom": "6.1.5",
    "@testing-library/user-event": "14.5.1",
    "jest-environment-jsdom": "29.7.0",
    
    "cypress": "13.6.1",
    "playwright": "1.40.1",
    
    "@storybook/addon-essentials": "7.6.7",
    "@storybook/addon-interactions": "7.6.7",
    "@storybook/addon-links": "7.6.7",
    "@storybook/blocks": "7.6.7",
    "@storybook/nextjs": "7.6.7",
    "@storybook/react": "7.6.7",
    "@storybook/testing-library": "0.2.2",
    "storybook": "7.6.7"
  }
}
```

---

## ⚡ **Next.js**

### **📁 tailwind.config.js**
```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          200: '#bfdbfe',
          300: '#93c5fd',
          400: '#60a5fa',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
          800: '#1e40af',
          900: '#1e3a8a',
          950: '#172554',
        },
        secondary: {
          50: '#f0fdf4',
          100: '#dcfce7',
          200: '#bbf7d0',
          300: '#86efac',
          400: '#4ade80',
          500: '#22c55e',
          600: '#16a34a',
          700: '#15803d',
          800: '#166534',
          900: '#14532d',
          950: '#052e16',
        },
        accent: {
          50: '#fffbeb',
          100: '#fef3c7',
          200: '#fde68a',
          300: '#fcd34d',
          400: '#fbbf24',
          500: '#f59e0b',
          600: '#d97706',
          700: '#b45309',
          800: '#92400e',
          900: '#78350f',
          950: '#451a03',
        },
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        mono: ['JetBrains Mono', 'monospace'],
      },
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
        '128': '32rem',
      },
      animation: {
        'fade-in': 'fadeIn 0.5s ease-in-out',
        'slide-up': 'slideUp 0.3s ease-out',
        'bounce-gentle': 'bounceGentle 2s infinite',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { transform: 'translateY(10px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
        bounceGentle: {
          '0%, 100%': { transform: 'translateY(0)' },
          '50%': { transform: 'translateY(-5px)' },
        },
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
    require('@tailwindcss/aspect-ratio'),
  ],
};
```

### **📁 tsconfig.json**
```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "es6"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/lib/*": ["./src/lib/*"],
      "@/hooks/*": ["./src/hooks/*"],
      "@/stores/*": ["./src/stores/*"],
      "@/types/*": ["./src/types/*"],
      "@/utils/*": ["./src/utils/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

---

## 🚀 **Vercel**

### **📁 vercel.json**
```json
{
  "version": 2,
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/next"
    }
  ],
  "routes": [
    {
      "src": "/api/(.*)",
      "dest": "/api/$1"
    },
    {
      "src": "/(.*)",
      "dest": "/$1"
    }
  ],
  "env": {
    "NEXT_PUBLIC_API_URL": "https://api.mussikon.com",
    "NEXT_PUBLIC_SUPABASE_URL": "https://your-project.supabase.co",
    "NEXTAUTH_URL": "https://admin.mussikon.com"
  },
  "functions": {
    "app/api/**/*.ts": {
      "maxDuration": 30
    }
  }
}
```

---

## 🐳 **Docker**

### **📁 Dockerfile**
```dockerfile
# Multi-stage build para optimizar el tamaño de la imagen
FROM node:18-alpine AS base

# Dependencias de producción
FROM base AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app

COPY package.json package-lock.json* ./
RUN npm ci --only=production && npm cache clean --force

# Build de la aplicación
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

RUN npm run build

# Imagen de producción
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public

RUN mkdir .next
RUN chown nextjs:nodejs .next

COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000
ENV HOSTNAME "0.0.0.0"

CMD ["node", "server.js"]
```

### **📁 docker-compose.yml**
```yaml
version: '3.8'

services:
  admin:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - NEXT_PUBLIC_API_URL=https://api.mussikon.com
      - NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
    volumes:
      - ./logs:/app/logs
    restart: unless-stopped

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - admin
    restart: unless-stopped
```

---

## 🔄 **CI/CD**

### **📁 .github/workflows/admin-deploy.yml**
```yaml
name: Admin Deploy

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run tests
      run: npm test
    
    - name: Run type check
      run: npm run type-check
    
    - name: Run linting
      run: npm run lint

  deploy:
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main'
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Build application
      run: npm run build
    
    - name: Deploy to Vercel
      uses: amondnet/vercel-action@v25
      with:
        vercel-token: ${{ secrets.VERCEL_TOKEN }}
        vercel-org-id: ${{ secrets.ORG_ID }}
        vercel-project-id: ${{ secrets.PROJECT_ID }}
        vercel-args: '--prod'
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [Next.js Documentation](https://nextjs.org/docs)
- [Tailwind CSS](https://tailwindcss.com/)
- [Vercel Documentation](https://vercel.com/docs)

### **📖 Documentación Relacionada**
- [Arquitectura Admin](./ARQUITECTURA_ADMIN.md)
- [Stack Tecnológico Admin](./STACK_TECNOLOGICO_ADMIN.md)
- [Sistema de Solicitudes Admin](./SOLICITUDES_ADMIN.md)

---

*Esta configuración proporciona una base sólida para el desarrollo, testing y build del panel de administración Next.js de MussikOn.*
