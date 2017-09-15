### iOS11中appIcon设置无效的问题

使用了CocoaPods的Xcode工程,在iOS11版的手机上图标不显示,原因在于CocoaPods脚本出了点问题.解决方案如下:

######解决方案：
1. 在Podfile 添加如下代码.
```
post_install do |installer|
    copy_pods_resources_path = "Pods/Target Support Files/Pods-[工程名]/Pods-[工程名]-resources.sh"
    string_to_replace = '--compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}"'
    assets_compile_with_app_icon_arguments = '--compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}" --app-icon "${ASSETCATALOG_COMPILER_APPICON_NAME}" --output-partial-info-plist "${BUILD_DIR}/assetcatalog_generated_info.plist"'
    text = File.read(copy_pods_resources_path)
    new_contents = text.gsub(string_to_replace, assets_compile_with_app_icon_arguments)
    File.open(copy_pods_resources_path, "w") {|file| file.puts new_contents }
end
```
**需要注意的是,将`[工程名]` 换成自己工程的名称**

2.然后运行
```
$pod install
```

###### [参考这里](https://github.com/CocoaPods/CocoaPods/issues/7003)

