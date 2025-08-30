# ⚙️ **CONFIGURACIÓN MÓVIL - FRONTEND MUSSIKON**

## 📋 **Índice de Configuración Móvil**

### **🔧 Configuración del Entorno**
- [Variables de Entorno](./CONFIGURACION_MOVIL.md#variables-de-entorno) - Configuración del sistema
- [Dependencias](./CONFIGURACION_MOVIL.md#dependencias) - Paquetes npm y configuración
- [Expo](./CONFIGURACION_MOVIL.md#expo) - Configuración de Expo

### **🚀 Build y Despliegue**
- [Android](./CONFIGURACION_MOVIL.md#android) - Configuración específica de Android
- [iOS](./CONFIGURACION_MOVIL.md#ios) - Configuración específica de iOS
- [CI/CD](./CONFIGURACION_MOVIL.md#cicd) - Pipeline de integración continua

---

## 🎯 **Propósito de la Configuración Móvil**

### **Objetivo Principal**
Establecer la configuración completa del entorno de desarrollo, testing y build para la aplicación React Native de MussikOn, asegurando consistencia entre plataformas.

### **Principios Clave**
- **Cross-Platform**: Configuración unificada para iOS y Android
- **Performance**: Optimización para dispositivos móviles
- **Seguridad**: Variables sensibles protegidas
- **Escalabilidad**: Preparada para crecer con el negocio

---

## 🔧 **Variables de Entorno**

### **📁 Archivo .env**
```bash
# API Configuration
EXPO_PUBLIC_API_URL=https://api.mussikon.com
EXPO_PUBLIC_SOCKET_URL=wss://api.mussikon.com
EXPO_PUBLIC_API_VERSION=v1

# Supabase Configuration
EXPO_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=your-anon-key

# Feature Flags
EXPO_PUBLIC_ENABLE_PUSH_NOTIFICATIONS=true
EXPO_PUBLIC_ENABLE_LOCATION_SERVICES=true
EXPO_PUBLIC_ENABLE_OFFLINE_MODE=true

# Analytics
EXPO_PUBLIC_ANALYTICS_ENABLED=true
EXPO_PUBLIC_ANALYTICS_KEY=your-analytics-key

# Maps
EXPO_PUBLIC_GOOGLE_MAPS_API_KEY=your-google-maps-key

# Build Configuration
EXPO_PUBLIC_APP_VERSION=1.0.0
EXPO_PUBLIC_BUILD_NUMBER=1
```

### **📱 app.config.js**
```javascript
export default {
  expo: {
    name: "MussikOn",
    slug: "mussikon-mobile",
    version: "1.0.0",
    orientation: "portrait",
    icon: "./assets/icon.png",
    userInterfaceStyle: "automatic",
    splash: {
      image: "./assets/splash.png",
      resizeMode: "contain",
      backgroundColor: "#1E40AF"
    },
    assetBundlePatterns: [
      "**/*"
    ],
    ios: {
      supportsTablet: true,
      bundleIdentifier: "com.mussikon.mobile",
      buildNumber: "1",
      infoPlist: {
        NSCameraUsageDescription: "Esta app necesita acceso a la cámara para tomar fotos de perfil",
        NSLocationWhenInUseUsageDescription: "Esta app necesita acceso a la ubicación para mostrar eventos cercanos",
        NSMicrophoneUsageDescription: "Esta app necesita acceso al micrófono para grabaciones de audio"
      }
    },
    android: {
      adaptiveIcon: {
        foregroundImage: "./assets/adaptive-icon.png",
        backgroundColor: "#1E40AF"
      },
      package: "com.mussikon.mobile",
      versionCode: 1,
      permissions: [
        "android.permission.CAMERA",
        "android.permission.ACCESS_FINE_LOCATION",
        "android.permission.ACCESS_COARSE_LOCATION",
        "android.permission.RECORD_AUDIO"
      ]
    },
    web: {
      favicon: "./assets/favicon.png"
    },
    plugins: [
      "expo-router",
      "expo-location",
      "expo-notifications",
      "expo-camera",
      "expo-media-library"
    ],
    extra: {
      eas: {
        projectId: "your-project-id"
      }
    }
  }
};
```

---

## 📦 **Dependencias**

### **🔧 package.json**
```json
{
  "name": "mussikon-mobile",
  "version": "1.0.0",
  "main": "expo-router/entry",
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web",
    "test": "jest",
    "test:watch": "jest --watch",
    "lint": "eslint . --ext .js,.jsx,.ts,.tsx",
    "lint:fix": "eslint . --ext .js,.jsx,.ts,.tsx --fix",
    "type-check": "tsc --noEmit",
    "build:android": "eas build --platform android",
    "build:ios": "eas build --platform ios",
    "submit:android": "eas submit --platform android",
    "submit:ios": "eas submit --platform ios"
  },
  "dependencies": {
    "expo": "~53.0.0",
    "expo-router": "~4.0.0",
    "expo-linking": "~7.0.0",
    "expo-constants": "~18.0.0",
    "expo-status-bar": "~2.0.0",
    "expo-secure-store": "~14.0.0",
    "expo-location": "~18.0.0",
    "expo-notifications": "~0.29.0",
    "expo-camera": "~16.0.0",
    "expo-media-library": "~17.0.0",
    "expo-image-picker": "~16.0.0",
    "expo-av": "~16.0.0",
    "expo-linear-gradient": "~14.0.0",
    "expo-blur": "~14.0.0",
    "expo-haptics": "~14.0.0",
    "react": "18.3.1",
    "react-native": "0.79.5",
    "react-native-reanimated": "~3.12.0",
    "react-native-gesture-handler": "~2.20.0",
    "react-native-screens": "~4.0.0",
    "react-native-safe-area-context": "4.12.0",
    "@react-navigation/native": "^7.0.0",
    "@react-navigation/bottom-tabs": "^7.0.0",
    "@react-navigation/stack": "^7.0.0",
    "@reduxjs/toolkit": "^2.8.2",
    "react-redux": "^9.1.0",
    "redux-persist": "^6.0.0",
    "socket.io-client": "^4.8.1",
    "axios": "^1.3.6",
    "react-hook-form": "^7.51.0",
    "react-native-vector-icons": "^10.0.3",
    "react-native-maps": "1.20.0",
    "react-native-maps-directions": "^1.9.0",
    "i18next": "^24.0.0",
    "react-i18next": "^14.1.0"
  },
  "devDependencies": {
    "@babel/core": "^7.20.0",
    "@types/react": "~18.2.45",
    "@types/react-native": "~0.73.0",
    "@typescript-eslint/eslint-plugin": "^7.0.0",
    "@typescript-eslint/parser": "^7.0.0",
    "eslint": "^8.57.0",
    "eslint-config-expo": "^7.0.0",
    "eslint-plugin-react": "^7.34.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "jest": "^29.7.0",
    "jest-expo": "~53.0.0",
    "typescript": "^5.3.0"
  },
  "private": true
}
```

---

## 📱 **Expo**

### **🔧 eas.json**
```json
{
  "cli": {
    "version": ">= 12.0.0"
  },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal",
      "env": {
        "EXPO_PUBLIC_API_URL": "https://dev-api.mussikon.com",
        "EXPO_PUBLIC_SOCKET_URL": "wss://dev-api.mussikon.com"
      }
    },
    "preview": {
      "distribution": "internal",
      "env": {
        "EXPO_PUBLIC_API_URL": "https://staging-api.mussikon.com",
        "EXPO_PUBLIC_SOCKET_URL": "wss://staging-api.mussikon.com"
      }
    },
    "production": {
      "env": {
        "EXPO_PUBLIC_API_URL": "https://api.mussikon.com",
        "EXPO_PUBLIC_SOCKET_URL": "wss://api.mussikon.com"
      }
    }
  },
  "submit": {
    "production": {}
  }
}
```

---

## 🤖 **Android**

### **📁 android/app/build.gradle**
```gradle
android {
    compileSdkVersion 34
    buildToolsVersion "34.0.0"

    defaultConfig {
        applicationId "com.mussikon.mobile"
        minSdkVersion 21
        targetSdkVersion 34
        versionCode 1
        versionName "1.0.0"
    }

    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
            signingConfig signingConfigs.release
        }
    }

    signingConfigs {
        release {
            storeFile file(MYAPP_RELEASE_STORE_FILE)
            storePassword MYAPP_RELEASE_STORE_PASSWORD
            keyAlias MYAPP_RELEASE_KEY_ALIAS
            keyPassword MYAPP_RELEASE_KEY_PASSWORD
        }
    }
}
```

---

## 🍎 **iOS**

### **📁 ios/MussikOnMobile/Info.plist**
```xml
<key>CFBundleDisplayName</key>
<string>MussikOn</string>
<key>CFBundleIdentifier</key>
<string>com.mussikon.mobile</string>
<key>CFBundleVersion</key>
<string>1</string>
<key>CFBundleShortVersionString</key>
<string>1.0.0</string>
<key>NSCameraUsageDescription</key>
<string>Esta app necesita acceso a la cámara para tomar fotos de perfil</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Esta app necesita acceso a la ubicación para mostrar eventos cercanos</string>
<key>NSMicrophoneUsageDescription</key>
<string>Esta app necesita acceso al micrófono para grabaciones de audio</string>
```

---

## 🔄 **CI/CD**

### **📁 .github/workflows/mobile-build.yml**
```yaml
name: Mobile Build

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build-android:
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
    
    - name: Setup EAS
      uses: expo/expo-github-action@v8
      with:
        eas-version: latest
        token: ${{ secrets.EXPO_TOKEN }}
    
    - name: Build Android
      run: eas build --platform android --non-interactive
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [Expo Documentation](https://docs.expo.dev/)
- [React Native Documentation](https://reactnative.dev/)
- [EAS Build](https://docs.expo.dev/build/introduction/)

### **📖 Documentación Relacionada**
- [Arquitectura Móvil](./ARQUITECTURA_MOVIL.md)
- [Stack Tecnológico Móvil](./STACK_TECNOLOGICO_MOVIL.md)
- [Sistema de Solicitudes Móvil](./SOLICITUDES_MOVIL.md)

---

*Esta configuración proporciona una base sólida para el desarrollo, testing y build de la aplicación móvil React Native de MussikOn.*
