<h1 align="center">Splash Screen</h1>

## Dependencias

- `react-native-bootsplash`
- `react-native-screens >= v4.16.0`
- `react-navigation/native`
- `react-native >= 0.80` o new architecture.

## Configurar Bootsplash

En la pagina de <a href="https://github.com/zoontek/react-native-bootsplash" target="_black">react-native-bootsplash</a>, hay algunos pasos a seguir, después de la instalación, lo configuro así:

```bash
npx react-native-bootsplash generate /path/logo.png --platforms=android --background=F5FCFF --logo-width=200 --flavor=main
```

- **/path/logo.png**: ruta en tu proyecto del logo del splash screen, puede ser también svg
- **--platforms=android**: puedes agregar mas android,ios,web
- **--background=F5FCFF**: Por defecto solo crea colores en 'light-mode'

  ```xml
  <!-- /android/app/src/main/res/values/colors.xml -->
  <?xml version="1.0" encoding="utf-8"?>
  <resources>
    <!-- --- -->
    <color name="bootsplash_background">#f5fcff</color>
  </resources>
  ```

- **--logo-width=200**: Size del logo
- **--flavor=main**: directorio por defecto de android.

También esta `--assets-output  --html  --plist` que puedes usarlos si los necesitas.

## Pasos extras

Eso en el mejor de los casos solo crea un splash en modo _light_, pero puede que no cree el logo `splash_logo` en el directorio `.../main/res/drawable/` así que toca agregarlo manualmente.

#### Modo Light - Dark

Creamos el directorio `values-night/` al mismo nivel que `drawable/` o `values/`, dentro copiamos exactamente lo siguiente:

**`values/colors.xml`**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="fill_logo">#FF000000</color>
    <color name="bootsplash_background">#f5fcff</color>
</resources>
```

En **`values-night/colors.xml`**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="fill_logo">#FFFFFFFF</color>
    <color name="bootsplash_background">#121212</color>
</resources>
```

De esta manera _Bootsplash_ utilizara el color de fondo desde `@color/bootsplash_background` y en cuanto a `fill_logo` lo utilizaremos en el logo si lo necesitamos.

#### Cambiar el logo

Si tienes un `splash_logo.png` o `splash_logo_xml` que combina tanto en _light_ como en _dark_ mode. Entonces puede que no necesites el siguiente paso.

Al mismo estilo que las imágenes vectoriales de _Android-Studio_ crea un logo vectorial. Es solo copiar el path y modificar `width, height, viewPortWidth, viewPortHeight, fillColor`. Si es mas complejo tomate tu tiempo para adaptarlo.

Ejemplo: `.../main/res/drawable/splash_logo.xml`

```xml
<vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="200dp"
    android:height="200dp"
    android:viewportWidth="512"
    android:viewportHeight="512">

    <path
        android:fillColor="@color/fill_logo"
        android:pathData="m 218.83 ........................... z"/>
</vector>
```

> [!IMPORTANT]
> El width y height deben ser iguales a `--logo-width=200` de la configuración de _Bootsplash_, asegúrate de que `android:fillColor` use `@color/fill_logo` el color del logo que definimos en `/values(-night)/colors.xml` y lo mas importante: eliminar el duplicado de `splash_logo.png` o `splash_logo.xml`, ya que al llamar a _splash_logo_ ignora el formato.

## Usar

Yo lo usare con `react-navigation/native`, para los demás pasos o incluso usar con _reanimated_ revisar <a href="https://github.com/zoontek/react-native-bootsplash#api" target="_blank">aquí</a>:

```typescript
import { NavigationContainer } from "@react-navigation/native";
import BootSplash from "react-native-bootsplash";

const App = () => (
  <NavigationContainer
    onReady={() => {
      BootSplash.hide();
      // o con animación al final
      BootSplash.hide({{ fade: true }})
    }}
  >
    {/* content */}
  </NavigationContainer>
);
```
