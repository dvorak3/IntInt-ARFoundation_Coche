# ARFoundation_Coche — Práctica de Realidad Aumentada

Aplicación móvil de Realidad Aumentada desarrollada en **Unity 6 (6000.0.6f1)** con **AR Foundation** y **Universal Render Pipeline (URP)**. El proyecto detecta superficies horizontales del entorno real para posicionar un coche mediante una retícula, permitiendo conducirlo para recoger paquetes, sumar puntos y esquivar arañas.

> **Nota sobre el vídeo demostrativo:** No se adjunta vídeo de ejecución en dispositivo físico debido a que el archivo `coche.apk` generado no se permite instalar en el terminal Android de pruebas por restricciones de instalación del sistema.

---

## Tecnologías y herramientas utilizadas

* **Motor:** Unity 6 (`6000.0.6f1`)
* **Frameworks AR:** AR Foundation, Google ARCore (Android) y Apple ARKit (iOS)
* **Renderizado:** Universal Render Pipeline (URP)
* **Control de versiones:** Git + Git LFS (para modelos 3D y texturas)
* **CI/CD:** GitHub Actions (`game-ci/unity-builder@v4` + `xcodebuild`)

---

## Pasos ejecutados en el desarrollo

### 1. Configuración de la escena AR y detección de planos
* Configuración de **`XR Origin`** con **`AR Plane Manager`** (detección horizontal usando el prefab `Driving Surface Plane`) y **`AR Raycast Manager`**.
* Verificación de **`Main Camera`** con los componentes **`AR Camera Manager`**, **`AR Camera Background`** y **`Tracked Pose Driver (Input System)`**.
* Inclusión de **`AR Session`** y **`DrivingSurfaceManager`** junto con **`Reticle Prefab`** para marcar la superficie detectada antes de instanciar el vehículo.

### 2. Mecánicas de juego, interfaz y puntuación
* Implementación de **`CarManager`** para el desplazamiento del coche sobre los planos detectados, **`PackageSpawner`** para la generación de paquetes recolectables y **`SpiderSpawner`** para los obstáculos.
* Desarrollo del sistema de puntuación mediante **`ScoreManager`** vinculado a la interfaz.
* Configuración del **`Canvas`** en modo *Scale With Screen Size* con los elementos de texto anclados en la parte superior central (*top-center*) para adaptarse a distintas resoluciones de pantalla móvil.

### 3. Renderizado URP y sistema de entrada
* Configuración de **`Mobile_RPAsset`** como perfil de calidad activo para plataformas móviles en *Project Settings > Quality*.
* Adición de **`AR Background Renderer Feature`** dentro de **`Mobile_Renderer`** para renderizar correctamente la imagen de la cámara física de fondo sin pantalla en negro.
* Configuración de **`Active Input Handling`** en modo **Both** (*Input Manager* + *Input System*) para asegurar la respuesta táctil en dispositivos móviles.

### 4. Configuración multiplataforma (Android e iOS)
* **Android:** Activación de *Google ARCore* en *XR Plug-in Management* y generación del paquete local `coche.apk`.
* **iOS:** Instalación y activación de *Apple ARKit* (*Requirement: Required*) en *XR Plug-in Management* y configuración del permiso obligatorio **`Camera Usage Description`** en *Player Settings*.

### 5. Repositorio, Git LFS y CI/CD con GitHub Actions
* Creación de `.gitignore` para excluir carpetas generadas por Unity (`Library/`, `Temp/`, `Logs/`, `Obj/`, `Build/`, `UserSettings/`, archivos de solución/proyecto y compilaciones locales).
* Configuración de **Git LFS** (`.gitattributes`) para gestionar los assets pesados (`.fbx`, `.png`, `.jpg`, `.tga`, `.psd`, audio) sin superar los límites de tamaño de GitHub.
* Automatización de compilación para iOS mediante el workflow `.github/workflows/build-ios.yml` en un runner `macos-latest`:
  1. Descarga del repositorio con soporte LFS y caché de la carpeta `Library`.
  2. Exportación del proyecto Xcode desde Unity con `game-ci/unity-builder@v4` utilizando los secretos del repositorio (`UNITY_LICENSE`, `UNITY_EMAIL`, `UNITY_PASSWORD`).
  3. Compilación sin firma (`CODE_SIGNING_ALLOWED=NO`) mediante `xcodebuild` y empaquetado del `.app` dentro de `Payload` para generar y subir el artefacto descargable `ARFoundation_Coche.ipa`.
