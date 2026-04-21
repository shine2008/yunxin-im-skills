
# IM UIKit Flutter 常见问题排查指南

本文档收集了使用IM UIKit Flutter时的常见问题及其解决方案，帮助开发者快速排查和解决集成过程中遇到的问题。

## 1. 混合开发集成 IM UIKit 模拟器运行报错

问题描述：混合开发集成 IM Flutter UIKit 模拟器运行报错。

问题原因：Google 兼容性问题，XCode 14 以上版本会出现该问题。

解决方法：修改本地缓存文件。

在 `FlutterPluginRegistrant.podspec` 文件中添加以下内容进行模拟器支持配置：
```ruby
# FlutterPluginRegistrant.podspec 配置
s.pod_target_xcconfig = {
    'DEFINES_MODULE' => 'YES',
    'EXCLUDED_ARCHS[sdk=iphonesimulator*]' => 'i386 arm64'
}
```
:::note notice
由于上述修改的是本地缓存文件，因此，若您重新 `pod install`，需要重新修改本地的缓存文件。
:::



## 2. iOS 端相册和拍摄功能无法使用

问题描述：Flutter UIKit 的 iOS 端的相册和拍摄功能无法使用，不弹出获取权限弹框。

问题原因：permission_handler 兼容性问题。

解决方法：自行设置 permission_handler 的兼容性配置，具体可参考 permission_handler 官网文档。

在 `podflie`文件中增加如下配置：
```ruby
# podfile 权限配置
post_install do |installer|
  installer.pods_project.targets.each do |target|
    flutter_additional_ios_build_settings(target)
        target.build_configurations.each do |config|
         config.build_settings['GCC_PREPROCESSOR_DEFINITIONS'] ||= [
             '$(inherited)',

             ## dart: PermissionGroup.calendar
             # 'PERMISSION_EVENTS=0',

             ## dart: PermissionGroup.reminders
             # 'PERMISSION_REMINDERS=0',

             ## dart: PermissionGroup.contacts
             # 'PERMISSION_CONTACTS=0',

             # dart: PermissionGroup.camera
             'PERMISSION_CAMERA=1',

             # dart: PermissionGroup.microphone
             'PERMISSION_MICROPHONE=1',

             ## dart: PermissionGroup.speech
             # 'PERMISSION_SPEECH_RECOGNIZER=0',

             # dart: PermissionGroup.photos
             'PERMISSION_PHOTOS=1',

             ## dart: [PermissionGroup.location, PermissionGroup.locationAlways, PermissionGroup.locationWhenInUse]
             # 'PERMISSION_LOCATION=0',

             ## dart: PermissionGroup.notification
             # 'PERMISSION_NOTIFICATIONS=0',

             # dart: PermissionGroup.mediaLibrary
             'PERMISSION_MEDIA_LIBRARY=1',

             ## dart: PermissionGroup.sensors
             # 'PERMISSION_SENSORS=0',

             ## dart: PermissionGroup.bluetooth
             # 'PERMISSION_BLUETOOTH=0'
           ]
         end 
  end
end
```




