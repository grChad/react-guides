# Variables de Entorno (.env)

Existen 2 formas que yo sepa, `react-native-config` y `react-native-dotenv`. Para esta guia usare la primera, ya que `dotenv` es más rígido en su uso y expone el archivo `.env` en su configuración.

### Instalación

Puedes usar `npm, yarn, pnpm`

```bash
npm install react-native-config
```

### Crear tu archivo .env

En la ruta de tu proyecto y con el estilo **UPPER_CAMEL_CASE**:

```bash
# En your file .env
API_URL=https://api.example.com
```

### Configurar en Android y iOS

##### En Android

Edita el archivo `android/app/build.gradle` en la parte superior.

```java
apply from: project(':react-native-config').projectDir.getPath() + "/dotenv.gradle"
```

##### En Ios

Abre `ios/Podfile` y escribe lo siguiente:

```java
pod 'react-native-config', :path => '../node_modules/react-native-config'
```

### Usar

Para utilizar acceder a `API_URL`. Importa `Config` desde `react-native-config`

```typescript
import Config from 'react-native-config';

const apiUrl = Config.API_URL;
```

### Conclusion

- Puedes usar `.env.local`, `.env.production` o el que quieras. Solo que cada vez que agregas algo a tu archivo `.env` tendras que volver a iniciar tu proyecto `npm start`.
