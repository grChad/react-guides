<h1 align="center">Administrar los colores</h1>

## Definir los colores

Por lo general lo guardo en la ruta `src/constants/colors.ts` y gracias a la estructura de `react-navigation/native`. Ya tengo predefinido los nombres de las variables, y el modelo de los colores.

```typescript
import type { Theme } from '@react-navigation/native'

export const DarkTheme: Theme = {
  // code
}

export const DefaultTheme: Theme = {
  // code
}

export const SchemeDarkColor = {
  isDark: true,
  ...DarkTheme.colors
  // code
}

export const SchemeLightColor = {
  isDark: false,
  ...DefaultTheme.colors
  // code
}
```

Donde `DarkTheme` y `DefaultTheme` se usan en **NavigationContainer** de `react-navigation/native`, que es el `root` de la aplicación. Y `SchemeDarkColor` y `SchemeLightColor` se usan en cualquier parte de la aplicación.

<details>
  <summary>Código completo aquí</summary>

```typescript
import type { Theme } from '@react-navigation/native'

export const DarkTheme: Theme = {
  dark: true,
  colors: {
    primary: '#b098e9',
    background: '#303446',
    card: '#151e27',
    text: 'rgb(229, 229, 231)',
    border: 'rgb(39, 39, 41)',
    notification: 'rgb(255, 69, 58)'
  },
  fonts: {
    regular: {
      fontFamily: 'sans-serif',
      fontWeight: 'normal'
    },
    medium: {
      fontFamily: 'sans-serif-medium',
      fontWeight: 'normal'
    },
    bold: {
      fontFamily: 'sans-serif',
      fontWeight: '600'
    },
    heavy: {
      fontFamily: 'sans-serif',
      fontWeight: '700'
    }
  }
}

export const DefaultTheme: Theme = {
  dark: false,
  colors: {
    primary: '#7750a8',
    background: '#F7F7F7',
    card: 'white',
    text: 'rgb(28, 28, 30)',
    border: 'rgb(216, 216, 216)',
    notification: 'rgb(255, 59, 48)'
  },
  fonts: {
    regular: {
      fontFamily: 'sans-serif',
      fontWeight: 'normal'
    },
    medium: {
      fontFamily: 'sans-serif-medium',
      fontWeight: 'normal'
    },
    bold: {
      fontFamily: 'sans-serif',
      fontWeight: '600'
    },
    heavy: {
      fontFamily: 'sans-serif',
      fontWeight: '700'
    }
  }
}

export const SchemeDarkColor = {
  isDark: true,
  ...DarkTheme.colors,
  secondText: '#ACABB3',
  popup: '#484848',
  pressPopup: '#585858',
  light: '#D8D5D1'
}

export const SchemeLightColor = {
  isDark: false,
  ...DefaultTheme.colors,
  secondText: '#5B5961',
  popup: '#F5F5F5',
  pressPopup: '#DFDFDF',
  light: '#FFFFFF'
}
```

</details>

## Crear un Hook

Es mas conveniente y fácil de usar si creamos un `hook` exclusivo para los colores. Así que en la ruta `./src/hooks/` creamos el archivo `useColor.ts`. Dentro de ella ponemos lo siguiente.

```typescript
import { useColorScheme } from 'react-native'
import {
  SchemeDarkColor,
  SchemeLightColor,
  DarkTheme,
  DefaultTheme
} from '../constants/colors'

export const useScheme = () => {
  const theme = useColorScheme()
  return theme === 'dark' ? SchemeDarkColor : SchemeLightColor
}

export const useTheme = () => {
  const theme = useColorScheme()
  return theme === 'dark' ? DarkTheme : DefaultTheme
}
```

Donde `useTheme` sera usado en **NavigationContainer** y `useScheme` en toda la App.

## Usar el useColor

#### useTheme en NavigationContainer

```typescript
import { NavigationContainer } from '@react-navigation/native'
import { useTheme } from '../hooks/useColor'

export default function NavigationApp() {
  const theme = useTheme()

  return (
    <NavigationContainer theme={theme}>
      {/* code of Application */}
    </NavigationContainer>
  )
}
```

#### useScheme dentro de la App

El `useTheme` de `react-navigation/native` no puede controlar todos los aspectos de nuestra app, Para ello heredando los colores agregando unos cuantos mas en `useScheme` y podemos controlarlo nosotros mismos.

```typescript
import { View, Text } from 'react-native'
import { useScheme } from '../hooks/useColor'

export default function Example() {
  const scheme = useScheme()

  return (
    <View style={{ backgroundColor: scheme.primary }}>
      <Text style={{ color: scheme.text }}>Example</Text>
    </View>
  )
}
```
