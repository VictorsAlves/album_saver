# Album saver plugin for Flutter

A Flutter plugin for iOS and Android for saving image to album

## Installation

First, add `album_saver` as a [dependency in your pubspec.yaml file](https://flutter.io/platform-plugins/).

> **NOTE:** There's a known issue with integrating plugins that use Swift into a Flutter project created with the Objective-C template. See issue [Flutter#16049](https://github.com/flutter/flutter/issues/16049) for help on integration.

### iOS

Edit your Podfile as follows:

``` pod
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

If you use image_picker plugin, add the following keys to your _Info.plist_ file, located in `<project root>/ios/Runner/Info.plist`:

* `NSPhotoLibraryUsageDescription` - describe why your app needs permission for the photo library. This is called _Privacy - Photo Library Usage Description_ in the visual editor.
* `NSCameraUsageDescription` - describe why your app needs access to the camera. This is called _Privacy - Camera Usage Description_ in the visual editor.
* `NSMicrophoneUsageDescription` - describe why your app needs access to the microphone, if you intend to record videos. This is called _Privacy - Microphone Usage Description_ in the visual editor.

### Android

Make sure you add the needed permissions to your Android Manifest Permission.

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### API
``` dart
import 'package:album_saver/album_saver.dart';
import 'package:image_picker/image_picker.dart';

File image = await ImagePicker.pickImage(source: ImageSource.gallery);

// Save to ablum
AlbumSaver.saveToAlbum(filePath: image.path, albumName: "YourAlbumName");

// Create album
// In Android, it will create a folder in DCIM folder
AlbumSaver.createAlbum(albumName: "YourAlbumName");

// Get DCIM folder path (just work on Android)
AlbumSaver.getDcimPath();
```

### Example

``` dart
import 'dart:io';

import 'package:flutter/material.dart';
import 'package:album_saver/album_saver.dart';
import 'package:image_picker/image_picker.dart';

void main() => runApp(MyApp());

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  File _image;
  String _dcimPath;

  void _getDcimPath()async{
    _dcimPath = await AlbumSaver.getDcimPath();
    setState(() {
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Plugin example app'),
        ),
        body: ListView(
          children: <Widget>[
            _buildPickedImage(),
            _dcimPath != null
            ? Container(
              padding: const EdgeInsets.all(16),
              child: Text("DCIM path: $_dcimPath")
            )
            : Container(),
            _buildImagePickerButton(),
            _buildSaveToAlbumButton(),
            _buildCreateAlbumButton(),
            Platform.isAndroid==true
            ? _builGetDcimPathButton()
            : Container(),
          ],
        ),
      ),
    );
  }

  Widget _buildPickedImage(){
    return Container(
      margin: EdgeInsets.all(16),
      height: 300.0,
      child: _image!=null
        ? null
        : Center(
          child: Container(
            child: Text("No selected image"),
          ),
        ),
      decoration: _image!=null
        ? BoxDecoration(
          border: Border.all(width: 1),
          image: DecorationImage(
            image: FileImage(_image),
          )
        )
        : BoxDecoration(
          border: Border.all(width: 1)
        ),
    );
  }

  Widget _buildImagePickerButton(){
    return Container(
      child: Row(
        mainAxisAlignment: MainAxisAlignment.center,
        children: <Widget>[
          IconButton(
            icon: Icon(Icons.camera_alt),
            onPressed: ()async{
              var image = await ImagePicker.pickImage(source: ImageSource.camera);
              setState(() {
                _image = image;
              });
            },
          ),
          IconButton(
            icon: Icon(Icons.folder),
            onPressed: ()async{
              var image = await ImagePicker.pickImage(source: ImageSource.gallery);
              setState(() {
                _image = image;
              });
            },
          )
        ],
      ),
    );
  }

  Widget _buildSaveToAlbumButton(){
    return Container(
      padding: const EdgeInsets.symmetric(horizontal: 16.0),
      child: RaisedButton(
        color: Colors.blue,
        textColor: Colors.white,
        child: Text("Save to album"),
        onPressed: (){
          AlbumSaver.saveToAlbum(filePath: _image.path, albumName: "test_album_saver2");
        },
      ),
    );
  }

  Widget _buildCreateAlbumButton(){
    return Container(
      padding: const EdgeInsets.symmetric(horizontal: 16.0),
      child: RaisedButton(
        color: Colors.blue,
        textColor: Colors.white,
        child: Text("Create a album named MyTestAlbum"),
        onPressed: (){
          AlbumSaver.createAlbum(albumName: "MyTestAlbum");
        },
      ),
    );
  }

  Widget _builGetDcimPathButton(){
    return Container(
      padding: const EdgeInsets.symmetric(horizontal: 16.0),
      child: RaisedButton(
                color: Colors.blue,
        textColor: Colors.white,
        child: Text("Get Dcim path"),
        onPressed: (){
          _getDcimPath();
        },
      ),
    );
  }
}
```
## Donate
If you find it is useful, consider buy me a cup of coffee:
[Donation](https://paypal.me/maithang)