
##  iOS11中appIcon设置无效的问题

使用了CocoaPods的Xcode工程,在iOS11版的手机上AppIcon不显示,原因是CocoaPods的资源编译脚本在iOS11下出了点问题.需要修改脚本.两种修改方式: 

###### 1.在Podfile添加脚本修改：

  1). 在Podfile 添加如下代码.
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

  2).然后运行
```
$pod install
```

###### 2.手动修改

打开工程目录下:`[工程名]/Pods/Target Support Files/Pods-[工程名]/Pods-[工程名]-resources.sh `这个文件,替换最后一段代码:

修改前:
```
 printf "%s\0" "${XCASSET_FILES[@]}" | xargs -0 xcrun actool --output-format human-readable-text --notices --warnings --platform "${PLATFORM_NAME}" --minimum-deployment-target "${!DEPLOYMENT_TARGET_SETTING_NAME}" ${TARGET_DEVICE_ARGS} --compress-pngs --compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}"
fi
```
修改后:
```
printf "%s\0" "${XCASSET_FILES[@]}" | xargs -0 xcrun actool --output-format human-readable-text --notices --warnings --platform "${PLATFORM_NAME}" --minimum-deployment-target "${!DEPLOYMENT_TARGET_SETTING_NAME}" ${TARGET_DEVICE_ARGS} --compress-pngs --compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}" --app-icon "${ASSETCATALOG_COMPILER_APPICON_NAME}" --output-partial-info-plist "${BUILD_DIR}/assetcatalog_generated_info.plist"
fi
```
然后重新运行工程即可

###### [解决方案源于这哥们](https://github.com/CocoaPods/CocoaPods/issues/7003)
我的本地环境是:Xcode9(GM)+iOS11手机+pod1.2.0

