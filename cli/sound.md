<h1 align="center">react-native-sound</h1>

> Modulo de React Native, para reproducir clips de sonidos en Ios, Android y Windows. Tiene calidad alpha y puede tener errores.

### Instalacion

```bash
npm install react-native-sound
```

##### Si encuentra el error

```bash
undefined is not an object (evaluating 'RNSound.IsAndroid')
```

Es posible que también necesites borrar completamente tus cachés de compilación para Android. Tú puede hacer esto usando

```bash
cd android
./gradlew cleanBuildCache
```

### Uso

Necesitas agregar tu archivo de audio en tu proyecto.

- **Android:** guarde sus archivos de clips de sonido en el directorio `android/app/src/main/res/raw/`. Tenga en cuenta que los archivos de este directorio deben estar en minúsculas y subrayados(underline). Por ejemplo(mi_nombre_archivo.mp3), y que los subdirectorios no son compatibles con Android.
- **Ios:** abra Xcode y agregue sus archivos de sonido al proyecto (haga clic derecho en el proyecto y seleccione `Add Files to [PROJECTNAME])`.

```javascript
import Sound from 'react-native-sound'

// Enable playback in silence mode
Sound.setCategory('Playback')

// Load the sound file 'whoosh.mp3' from the app bundle
// See notes below about preloading sounds within initialization code below.
const sound = new Sound('sound_filename.mp3', Sound.MAIN_BUNDLE, (error) => {
  if (error) {
    console.log('failed to load the sound', error)
  }
})

// Para reproducir el sonido llame al method play()
sound.play((success) => {
  if (success) {
    console.log('successfully finished playing')
  } else {
    console.log('playback failed due to audio decoding errors')
  }
})

// Reduce the volume
sound.setVolume(0.5)

// Position the sound to the full right in a stereo field
sound.setPan(1)

// Loop indefinitely until stop() is called
sound.setNumberOfLoops(-1)

// Get properties of the player instance
console.log('volume: ' + sound.getVolume())
console.log('pan: ' + sound.getPan())
console.log('loops: ' + sound.getNumberOfLoops())

// Seek to a specific point in seconds
sound.setCurrentTime(2.5)

// Get the current playback point in seconds
sound.getCurrentTime((seconds) => console.log('at ' + seconds))

// Pause the sound
sound.pause()

// Stop the sound and rewind to the beginning
sound.stop(() => {
  // Note: If you want to play a sound after stopping and rewinding it,
  // it is important to call play() in a callback.
  sound.play()
})

// Release the audio player resource
sound.release()
```

### Notas

- Para minimizar el retraso en la reproducción, precargar un archivo de sonido sin llamar al method `play()`, durante la inicialización de la aplicación. Ejemplo:
- Use archivos de sonido compatibles: MP3 o WAV.
- Puede concatenar los methods, por ejemplo: `sound.setVolume(.5).setPan(.5).play()`.
- Puedes utilizar rutas absolutas como: `.../raw/sounds/sound_file.mp3`.
  ```javascript
  const sound = new Sound('sounds/sound_file.mp3',...)
  ```
- Puedes utilizar rutas remotas para reproducir el sonido, solo necesitar indicar la URL como primer parametro. Ejemplo `'http://example.com/sound.mp3'`

  ```javascript
  const sound = new Sound('http://example.com/sound.mp3', null, (error) => {
    if (error) {
      console.log('Failed to load the sound', error)
    }
  })

  // el uso es similar.
  sound.play((success) => {
    if (success) {
      console.log('Sound played successfully')
    } else {
      console.log('Sound playback failed')
    }
  })
  ```
