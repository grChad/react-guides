<h1 align="center">Renombrar App</h1>

> Tanto para una aplicación `CLI` o `Expo`.

#### Primero

En su Archivo de configuración `app.json`, solo cambie `displayName`.

```json
{
  "name": "MyAppName",
  "displayName": "My App Name"
}
```

#### Configuración para Android

Modificar el archivo `string.xml` de la ruta: `android/app/src/main/res/values/`.

```xml
<resources>
  <string name="app_name">My App Name</string>
</resources>
```

#### Para Ios

Modifique su archivo `info.plist`, después del key tag.

```txt
<key>CFBundleDisplayName</key>
<string>My App Name</string>
```
