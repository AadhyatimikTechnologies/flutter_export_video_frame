# Export Video frame for Flutter

A Flutter plugin for iOS and Android for exporting picture from video file.

## Installation

add ```export_video_frame``` as a dependency in your pubspec.yaml file.

## Usage

```dart

/// Returns whether clean success
  static Future<bool> cleanImageCache() async {
    final String result = await _channel.invokeMethod('cleanImageCache');
    if (result == "success") {
      return true;
    }
    return false;
  }

  /// Save image to album
  ///
  /// - parameters:
  ///    - file: file of video
  ///    - albumName: save the album name
  /// Returns whether save success
  static Future<bool> saveImage(File file, String albumName) async {
    var para = {"filePath":file.path,"albumName":albumName};
    final bool result =
        await _channel.invokeMethod('saveImage', para);
    return result;
  }

  /// Returns the file list of the exporting image
  ///
  /// - parameters:
  ///    - filePath: file path of video
  ///    - number: export the number of frames
  ///    - quality: scale of export frame."0" is lowest,"1" is origin.("0" is scale for 0.1 in android) 
  static Future<List<File>> exportImage(String filePath, int number,double quality) async {
    var para = {"filePath":filePath,"number":number,"quality":quality};
    final List<dynamic> list =
        await _channel.invokeMethod('exportImage', para);
    var result = list
        .cast<String>()
        .map((path) => File.fromUri(Uri.file(path)))
        .toList();
    return result;
  }

  /// Returns the file list of the exporting image
  ///
  /// - parameters:
  ///    - file: file of video
  ///    - duration: export the duration of frames
  ///    - radian: rotation the frame ,which will export frame.
  static Future<File> exportImageBySeconds(File file, Duration duration,double radian) async {
    var milli = duration.inMilliseconds;
    var para = {"filePath":file.path,"duration":milli,"radian":radian};
    final String path = await _channel
        .invokeMethod('exportImageBySeconds', para);
    try {
      var result = File.fromUri(Uri.file(path));
      return result;
    } catch (e) {
      throw e;
    }
  }

```

### ios

If there will be an error when compile ios app.Because flutter use swift is 4.0.
located in ios/Podfile
Edit your Podfile as follows:

``` ruby

target 'Runner' do
  use_frameworks! # <--- add this
  ...
end

post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings['ENABLE_BITCODE'] = 'NO'
      config.build_settings['SWIFT_VERSION'] = '4.2' # <--- add this
    end
  end
end

```

If you use saveAblum api,you need add the add the following keys to your Info.plist file, located ios/Runner/Info.plist:

```xml

<key>NSPhotoLibraryUsageDescription</key>
<string>Use Ablum For your purpose</string>

```

### Android

Make sure you add the needed permissions to your Android Manifest Permission.

``` gradle
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

[Example Demo](https://pub.dev/packages/export_video_frame#-example-tab-)