<h1 align="center">Define Theme, typography, shaders and spacing</h1>
<p align="center">
  El objetivo es Tener una configuración que no dependa de <strong>Context</strong> o un <strong>Provider</strong> que envuelva tu <i>App</i>, Ademas de tener persistencia de datos de la configuración de tu tema.
</p>

## Definir colores

Dentro de `/src/themes/` creamos 2 archivos. `darkTheme.ts` y `lightTheme.ts`.

<details>
  <summary><code>/src/themes/darkTheme.ts</code></summary>

```typescript
// los 6 colores que usa 'react-navigation/native', solo si quieres reemplazarlos.
export const DarkNavigation = {
  primary: '#A2C9FD',
  background: '#111418',
  card: '#000000',
  text: '#E1E2E8',
  border: '#111418',
  notification: '#FFB4AB'
}

// Los demás colores que quieras usar en tu App.
// Me baso en material 3, https://materialkolor.com
export const DarkExtra = {
  primary: '#A2C9FD',
  onPrimary: '#00325A',
  primaryContainer: '#1C4875',
  onPrimaryContainer: '#D2E4FF',

  secondary: '#BBC7DB',
  onSecondary: '#253141'
  // Demás colors, agrega los que creas conveniente.
}

export const DarkThemeApp = {
  dark: true,
  colors: {
    ...DarkNavigation,
    ...DarkExtra
  }
}
```

</details>

<details>
  <summary><code>/src/themes/lightTheme.ts</code></summary>

```typescript
// los 6 colores que usa 'react-navigation/native', solo si quieres reemplazarlos.
export const LightNavigation = {
  primary: '#38608F', // to primary
  background: '#F8F9FF', // to surface
  card: '#FFFFFF', // white
  text: '#191C20', // to onSurface
  border: '#D8DAE0', // to surfaceDim
  notification: '#BA1A1A' // to error
}

// Los demás colores que quieras usar en tu App.
// Me baso en material 3, https://materialkolor.com
export const LightExtra = {
  primary: '#38608F',
  onPrimary: '#FFFFFF',
  primaryContainer: '#D2E4FF',
  onPrimaryContainer: '#1C4875',

  secondary: '#535F70',
  onSecondary: '#FFFFFF'
  // Demás colors, agrega los que creas conveniente.
}

export const LightThemeApp = {
  dark: false,
  colors: {
    ...LightNavigation,
    ...LightExtra
  }
}
```

</details>

## Configurar Zustand

_Zustand_ gestiona el estado global de la App, como _Context_ o mas poderoso _Redux_, La diferencia es que es más simple.

Install, visita el <a href="https://zustand.docs.pmnd.rs/getting-started/introduction" target="_blank">Website</a>.

```bash
npm install zustand
```

Creamos el directorio `/src/store/` y dentro `themeStore.ts`.

<details>
  <summary><code>/src/store/themeStore.ts</code></summary>

```typescript
import { createJSONStorage, persist } from 'zustand/middleware'
import { create } from 'zustand/react'
import { ThemeStorage } from '@/storage/mmkv'

type ThemeType = 'light' | 'dark' | 'system'

interface ThemeStore {
  theme: ThemeType
  setTheme: (theme: ThemeType) => void
}

export const useThemeStore = create<ThemeStore>()(
  persist(
    (set) => ({
      theme: 'system',
      setTheme: (theme) => set({ theme })
    }),
    {
      name: 'theme-settings',
      storage: createJSONStorage(() => ThemeStorage)
    }
  )
)
```

</details>

En este código falta configurar `ThemeStorage`

## Configurar MMKV

El almacenamiento de clave/valor más rápido para React Native. ¡~30 veces más rápido que AsyncStorage!

Install y website en Github <a href="https://github.com/mrousavy/react-native-mmkv" target="_black">here</a>.

```bash
npm install react-native-mmkv react-native-nitro-modules
```

Tiene la dependencia de nitro-modules, luego de instalar resetear.

```bash
npm start -- --reset-cache
```

Creamos el en el directorio `/src/storage/` creamos el archivo `mmkv.ts`.

<details>
  <summary><code>/src/storage/mmkv.ts</code></summary>

```typescript
import { createMMKV } from 'react-native-mmkv'

// Configuración centralizada de todas las instancias MMKV
export const MMKVInstances = {
  theme: createMMKV({ id: 'theme-storage' })
  // Agrega más según necesites
}

// Adapter genérico para Zustand
export const createMMKVStorage = (instance: ReturnType<typeof createMMKV>) => ({
  getItem: (name: string): string | null => {
    const value = instance.getString(name)
    return value ?? null
  },
  setItem: (name: string, value: string): void => {
    instance.set(name, value)
  },
  removeItem: (name: string): void => {
    instance.remove(name)
  }
})

// Storage adapters pre-creados
export const ThemeStorage = createMMKVStorage(MMKVInstances.theme)
```

</details>

## Crear un Hook

Es mas conveniente y fácil de usar si creamos un `hook` exclusivo para tu tema.

En `/home/hooks/` creamos `theme.ts` y editamos.

<details>
  <summary><code>/src/hooks/theme.ts</code></summary>

```typescript
import { useMemo } from 'react'
import { useColorScheme } from 'react-native'
import { DarkTheme, DefaultTheme } from '@react-navigation/native'

// Config User
import { DarkNavigation, DarkThemeApp } from '@/themes/darkTheme'
import { LightNavigation, LightThemeApp } from '@/themes/lightTheme'

// Store
import { useThemeStore } from '@/store/themeStore'

// Mezclamos los temas de 'react-navigation/native' y los nuestros para NavigationContainer
// y definimos nuestros propios temas para usarlos en toda la app THEMES['mode'].app
const THEMES = {
  light: {
    navigation: {
      ...DefaultTheme,
      colors: { ...LightNavigation }
    },
    app: LightThemeApp
  },
  dark: {
    navigation: {
      ...DarkTheme,
      colors: { ...DarkNavigation }
    },
    app: DarkThemeApp
  }
} as const

// El nuestro propio
export const useTheme = () => {
  const systemTheme = useColorScheme()
  const userTheme = useThemeStore((state) => state.theme)

  let effectiveTheme: 'light' | 'dark' = 'light'

  if (userTheme === 'system') {
    effectiveTheme = systemTheme === 'dark' ? 'dark' : 'light'
  } else {
    effectiveTheme = userTheme
  }

  return useMemo(() => THEMES[effectiveTheme].app, [effectiveTheme])
}

// Para usar en NavigationContainer
export const useThemeNavigation = () => {
  const systemTheme = useColorScheme()
  const userTheme = useThemeStore((state) => state.theme)

  let effectiveTheme: 'light' | 'dark' = 'light'

  if (userTheme === 'system') {
    effectiveTheme = systemTheme === 'dark' ? 'dark' : 'light'
  } else {
    effectiveTheme = userTheme
  }

  return useMemo(() => THEMES[effectiveTheme].navigation, [effectiveTheme])
}
```

</details>

## Usar el el Tema

El paso final. Usar el Theme, por defecto esta en 'system' así que dependerá del dispositivo.

En `/src/navigation/NavigationApp.tsx` o donde se encuentra la configuración principal de _react-navigation/native_.

<details>
  <summary><code>/src/navigation/NavigationApp.tsx</code></summary>

```typescript
import { NavigationContainer } from '@react-navigation/native'
import { SafeAreaProvider } from 'react-native-safe-area-context'
import { useThemeNavigation } from '@/hooks/theme' // <-- Aquí
import StackNavigation from './StackNavigation'

export default function App() {
	const themeNavigation = useThemeNavigation() // <-- Aquí

	return (
		<SafeAreaProvider>
			<NavigationContainer theme={themeNavigation}> {/*<--- y Aquí*/}
				<StackNavigation />
			</NavigationContainer>
		</SafeAreaProvider>
	)
}
```

</details>

En cuanto a `useTheme()` lo podemos utilizar en donde queramos.

Ejemplo:

```typescript
import { View, Text } from 'react-native'
import { useTheme } from '@/hooks/theme'

export default function Example() {
  const theme = useTheme()
  // o también se puede usar destructuring
  const {colors, dark} = useTheme()

  return (
    <View style={{ backgroundColor: theme.colors.primary }}>
      <Text style={{ color: theme.colors.onSurface }}>Example</Text>
    </View>
  )
}
```

## Cambiar el Theme Permanentemente

Como ya construimos la estructura para ello ahora solo queda usar.

```typescript
import { View, Button } from 'react-native'
import { useThemeStore } from '@/store/themeStore'


export default function CardTheme() {
	const { setTheme } = useThemeStore()

  return (
    <View>
      <Button title="system" onPress={() => setTheme('system')} />
      <Button title="light" onPress={() => setTheme('light')} />
      <Button title="dark" onPress={() => setTheme('dark')} />
    </View>
  )
}
```

## Extras: Shapes, Typography & Spacing

Para que toda la app aparente mas profesionalismo; y tengamos que pensar en menos cosas, podemos definirlas desde el inicio.

En `/src/themes/` agregamos otro archivo, le puse `others.ts`.

<details>
  <summary><code>/src/themes/others.ts</code></summary>

```typescript
export const Typography = {
  title: {
    lg: {
      fontFamily: 'Asap',
      fontSize: 24,
      lineHeight: 32,
      fontWeight: '700' as const
    },
    md: {
      fontFamily: 'Asap',
      fontSize: 20,
      lineHeight: 28,
      fontWeight: '600' as const
    },
    sm: {
      fontFamily: 'Asap',
      fontSize: 18,
      lineHeight: 24,
      fontWeight: '600' as const
    }
  },

  // Cuerpo de texto (3 opciones)
  body: {
    lg: {
      fontSize: 16,
      lineHeight: 24,
      fontWeight: '500' as const
    },
    md: {
      fontSize: 14,
      lineHeight: 20,
      fontWeight: '500' as const
    },
    sm: {
      fontSize: 12,
      lineHeight: 16,
      fontWeight: '400' as const
    }
  },

  // Etiquetas (3 opciones)
  label: {
    lg: {
      fontSize: 14,
      lineHeight: 20,
      fontWeight: '500' as const
    },
    md: {
      fontSize: 12,
      lineHeight: 16,
      fontWeight: '500' as const
    },
    sm: {
      fontSize: 10,
      lineHeight: 14,
      fontWeight: '500' as const
    }
  }
}
export const Spacing = { none: 0, xs: 4, sm: 8, md: 16, lg: 24, xl: 32 }
export const Shapes = { none: 0, xs: 4, sm: 8, md: 12, lg: 16, xl: 24, full: 1000 }
```

</details>

Puedes modificarlo tanto como quieras.

Luego los agregas a `darkTheme.ts` y `lightTheme.ts`.

Modificamos `/src/themes/darkTheme.ts`.

```typescript
import { Shapes, Spacing, Typography } from './others' // <-- Importamos

// ....
// Resto del código

export const DarkThemeApp = {
  dark: true,
  colors: {
    ...DarkNavigation,
    ...DarkExtra
  },
  typography: Typography, // <-- Aquí
  spacing: Spacing, // <-- Aquí
  shapes: Shapes // <-- Aquí
}
```

Modificamos `/src/themes/lightTheme.ts`.

```typescript
import { Shapes, Spacing, Typography } from './others' // <-- Importamos

// ....
// Resto del código

export const LightThemeApp = {
  dark: true,
  colors: {
    ...LightNavigation,
    ...LightExtra
  },
  typography: Typography, // <-- Aquí
  spacing: Spacing, // <-- Aquí
  shapes: Shapes // <-- Aquí
}
```

Y eso es todo, Ahora podemos usarlo en cualquier lado con `useTheme()`.

Ejemplo:

```typescript
import { View, Text } from 'react-native'
import { useTheme } from '@/hooks/theme'

export default function Example() {
  const {colors, dark, shapes, spacing, typography} = useTheme()

  return (
    <View style={{
      backgroundColor: dark ? '#EDF3FE' : '#FF6347',
      paddingVertical: spacing.xs,
      paddingHorizontal: spacing.md,
      borderWidth: 1,
      borderRadius: shapes.md
    }}>
      <Text style={[
        typography.body.md,
        { color: colors.onSurface }
      ]}>Example</Text>
    </View>
  )
}
```
