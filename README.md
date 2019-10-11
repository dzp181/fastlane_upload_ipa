# fastlane_upload_ipa

### 1.文件目录格式：
![1.jpg](https://upload-images.jianshu.io/upload_images/1874585-70ca13547acb4904.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
备注:
- 1.Deliverfile文件是deliver需要的文件，和fastfile文件类似；
- 2. ipa文件是自己创建的，ipa和dSYM文件输出路径；
- 3.metadata文件是审核提交时提供的一些信息，例如隐私网站，登录账号等等；
- 4.Pluginfile是插件文件；
- 5.screenshots文件是模拟器截图，即预览图文件。
``` 

### 2.打包方式
>1.development

``` 
lane :beta do
gym(
scheme: "XXX项目文件名",
clean: true, 
silent: true, 
export_xcargs: "-allowProvisioningUpdates", 
output_directory: "./fastlane/ipa", //指定ipa和dSYM文件输出路径
export_method:"development", //打包方式
export_options: {
provisioningProfiles: { 
"XXX项目bundle id" => "描述文件对应的名字" 
},  
signingStyle: "manual"  //手动签名方式
}
)
pgyer(api_key: "xxx", user_key: "xxx", update_description: "")  //蒲公英账号密钥等

end
```

>2.adhoc （与development类似，证书类型不同）

``` 
lane :beta do
gym(
scheme: "XXX项目文件名",
clean: true, 
silent: true, 
export_xcargs: "-allowProvisioningUpdates", 
output_directory: "./fastlane/ipa", //指定ipa和dSYM文件输出路径
export_method:"ad-hoc", //打包方式
export_options: {
provisioningProfiles: { 
"XXX项目bundle id" => "描述文件对应的名字"  //这里对应的是adhoc的描述文件
},
signingStyle: "manual"  //手动签名方式
}
)
pgyer(api_key: "xxx", user_key: "xxx", update_description: "")  //蒲公英账号密钥等

end
```

>3.release 

``` 
//自定义方法，控制版本号和build号
def prepare_version(options)
increment_version_number(
version_number: options[:version]
)

increment_build_number(
build_number: options[:build]
)
end


lane :release do |options|

prepare_version(options)

gym(
scheme: "XXX项目文件名",
clean: true,
silent: true,
export_xcargs: "-allowProvisioningUpdates",
output_directory: "./fastlane/ipa",
export_method:"app-store",
export_options: {
provisioningProfiles: { 
"XXX项目bundle id" => "描述文件对应的名字"
},
signingStyle: "manual"
}
)

deliver(
submit_for_review: false, //是否提交审核
automatic_release: false, //审核通过是否自动发布
force: true,  
skip_metadata: true, //是否跳过更新网页数据配置，提交审核的一些信息
skip_screenshots: true, //是否跳过更新预览图
app_identifier: "项目id",
username: "苹果开发者账号"
)

end
```

### 4.官方文档参考  (https://docs.fastlane.tools)
