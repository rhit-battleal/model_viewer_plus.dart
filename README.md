# 3D Model Viewer for Flutter

<https://pub.dev/packages/model_viewer> - Original fork from a package that is discontinued,
and add the web support for it.

<https://pub.dev/packages/model_viewer_plus> - Latest fork from a package that does not provide mobile support for Webview_flutter 4.0.0 <https://pub.dev/packages/webview_flutter>

This is a [Flutter](https://flutter.dev) widget for rendering interactive
3D models in the [glTF](https://www.khronos.org/gltf/) and
[GLB](https://wiki.fileformat.com/3d/glb/) formats.

The widget embeds Google's [`<model-viewer>`](https://modelviewer.dev)
web component in a [WebView](https://pub.dev/packages/webview_flutter).

## Screenshot

<img alt="Screenshot of astronaut model" src="./example/flutter_01.png" width="480"/>
<img alt="Screenshot of astronaut model" src="./example/flutter_02.png" width="480"/>

## Compatibility

- Android
- iOS (AR View may not avaliable on iOS 16+)

## Notes

We use the [Google APP](https://play.google.com/store/apps/details?id=com.google.android.googlequicksearchbox), `com.google.android.googlequicksearchbox` to display interactive 3D models on Android. The model displays in 'ar_preferred' mode by default, Scene Viewer launches in AR native mode as the entry mode. If [Google Play Services for AR (ARCore, `com.google.ar.core`)](https://play.google.com/store/apps/details?id=com.google.ar.core) isn't present, Scene Viewer gracefully falls back to 3D mode as the entry mode.

## Installation

### `pubspec.yaml`

```yaml
dependencies:
   model_viewer_plus: ^(newest from https://pub.dev/packages/model_viewer_plus)
```

### `AndroidManifest.xml` (Android 9+ only)

To use this widget on Android 9+ devices, your app must be permitted to make
an HTTP connection to `http://localhost:XXXXX`. Android 9 (API level 28)
changed the default for [`android:usesCleartextTraffic`] from `true` to
`false`, so you will need to configure your app's
`android/app/src/main/AndroidManifest.xml` as follows:

```diff
--- a/example/android/app/src/main/AndroidManifest.xml
+++ b/example/android/app/src/main/AndroidManifest.xml
@@ -8,7 +8,8 @@
     <application
         android:name="io.flutter.app.FlutterApplication"
         android:label="model_viewer_example"
-        android:icon="@mipmap/ic_launcher">
+        android:icon="@mipmap/ic_launcher"
+        android:usesCleartextTraffic="true">
         <activity
             android:name=".MainActivity"
             android:launchMode="singleTop"
```

This does not affect Android 8 and earlier. See [#7] for more information.

### `app/build.gradle` (Android only)

Change minSdkVersion to 19.

    defaultConfig {
        // TODO: Specify your own unique Application ID (https://developer.android.com/studio/build/application-id.html).
        applicationId "com.example.lab_rat"
        minSdkVersion 19
        targetSdkVersion flutter.targetSdkVersion
        versionCode flutterVersionCode.toInteger()
        versionName flutterVersionName
    }

### `Info.plist` (iOS only)

To use this widget on iOS, you need to opt-in to the embedded views preview
by adding a boolean property to your app's `ios/Runner/Info.plist` file, with
the key `io.flutter.embedded_views_preview` and the value `YES`:

```xml
<key>io.flutter.embedded_views_preview</key>
<true/>
```

### `web/index.html` (Web only)

Modify the `<head>` tag of your `web/index.html` to load the JavaScript, like so:

```html
<head>

  <!-- Other stuff -->

  <script type="module" src="./assets/packages/model_viewer_plus/assets/model-viewer.min.js" defer></script>
</head>
```

`./assets/packages/model_viewer_plus/assets/model-viewer.min.js` will use the default js file which is included in this package's asset. The [official site](https://modelviewer.dev) uses unpkg, by using `https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js`, you are using the latest version of `<model-viewier>`. You may replace the
value of `src` attribute with another CDN mirror's URL. But please notice that our model-viewer-plus maybe not able to keep up with the `<model-viewier>`'s latest version.

According to [#44](https://github.com/omchiii/model_viewer_plus.dart/issues/44), the render quality maybe low on some mobile devices. Please consider adding `<meta name="viewport" content="width=device-width, initial-scale=1" />` to the `<head>` of your `web/index.html`.

## Features

- Renders glTF and GLB models. (Also, [USDZ] models on iOS 12+.)

- Supports animated models, with a configurable auto-play setting.

- Optionally supports launching the model into an [AR] viewer.

- Optionally auto-rotates the model, with a configurable delay.

- Supports a configurable background color for the widget.

[USDZ]: https://graphics.pixar.com/usd/docs/Usdz-File-Format-Specification.html
[AR]:   https://en.wikipedia.org/wiki/Augmented_reality

## Examples

### Importing the library

```dart
import 'package:model_viewer_plus/model_viewer_plus.dart';
```

### Creating a `ModelViewer` widget

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: Text("Model Viewer")),
        body: ModelViewer(
          src: 'https://modelviewer.dev/shared-assets/models/Astronaut.glb',
          alt: "A 3D model of an astronaut",
          ar: true,
          autoRotate: true,
          cameraControls: true,
        ),
      ),
    );
  }
}
```

### Loading a bundled Flutter asset

```dart
class MyApp extends StatelessWidget {
// ...
          src: 'assets/MyModel.glb',
// ...
}
```

### Loading a model from the file system

This is not avaliable on Web.

```dart
class MyApp extends StatelessWidget {
// ...
          src: 'file:///path/to/MyModel.glb',
// ...
}
```

### Loading a model from the web

```dart
class MyApp extends StatelessWidget {
// ...
          src: 'https://modelviewer.dev/shared-assets/models/Astronaut.glb',
// ...
}
```

Note that due to browsers' [CORS] security restrictions, the model file
*must* be served with a `Access-Control-Allow-Origin: *` HTTP header.

[#7]:                       https://github.com/drydart/model_viewer.dart/issues/7
[CORS]:                     https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
[glTF Validator]:           https://github.khronos.org/glTF-Validator/
[google/model-viewer#1015]: https://github.com/google/model-viewer/issues/1015
[google/model-viewer#1109]: https://github.com/google/model-viewer/issues/1109
[`android:usesCleartextTraffic`]: https://developer.android.com/guide/topics/manifest/application-element#usesCleartextTraffic
