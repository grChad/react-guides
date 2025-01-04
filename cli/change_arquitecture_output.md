<h1 align="center">Cambiar la arquitectura de salida Apk</h1>

> para que se pueda controlar la salida en arquitecturas como `"armeabi-v7a", "arm64-v8a", "x86", "x86_64"`

#### Razón

Normalmente la salida es una genérica global que ocupa pucho espacio. No esta optimizada para las diferentes arquitecturas.

#### Como modificar la salida

La configuración la hacemos dentro del archivo de la ruta `android/app/build.gradle`.

```groovy
android {
     // resto del codigo

    splits {
        abi {
            reset()
            enable true
            universalApk false
            include "armeabi-v7a", "arm64-v8a", "x86", "x86_64"
        }
    }
}
```

También puedes hacer otros cambios, todo depende de tus necesidades.
