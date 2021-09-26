# flutter_in_uni

> uniapp 使用[Flutter 插件](https://ext.dcloud.net.cn/plugin?id=4302)，集成 flutter

### 资源

1. [uniapp - Flutter 插件](https://ext.dcloud.net.cn/plugin?id=4302)
2. 作者[官方案例](https://github.com/snice/uniapp-flutter)
3. [Flutter 插件 - 项目文档](https://www.yuque.com/books/share/79a0282c-e800-408a-9d1f-226682cf77a1)

### 关于本项目

开发环境： [flutter 2.5.1 stable channel](https://storage.flutter-io.cn/flutter_infra_release/releases/stable/windows/flutter_windows_2.5.1-stable.zip)

开发流程（**_具体改变参考提交记录_**）

1. 使用 flutter create 创建一个空项目
2. 引入通讯相关代码
3. resources 文件夹内放置 uniapp 项目所需的 sdk

### Flutter 插件使用流程

1. 创建 flutter module 项目。命令：`flutter create -t module --org=com.xxx.xxxxx flutter_module_name`
2. 开发调试后进行打包。参考[文档](https://www.yuque.com/books/share/79a0282c-e800-408a-9d1f-226682cf77a1/wk8ay7#C8EPz)。

   - Android：命令 `flutter build aar --no-debug --no-profile` 生成至`build/host/outputs/repo/xx/xx/flutter_release/1.0/flutter_release-1.0.aar`
   - iOS：命令 `flutter build ios-framework --no-debug --no-profile --output=build/ios` 生成至`build/ios/Release/App.xcframework/ios-arm64_armv7/App.framework`

3. 项目内集成
   1. src 同级创建 nativeplugins 目录，将下载得到的 sn-flutter 放进去。使用上一步得到的打包文件，替换对应文件
   2. src 下创建 js*sdk 目录（*别的名字也可\_），将下载得到的 flutter.js 放进去。该 js 提供 `MethodChannel` 类，使用普通的 `import` 语法导入，用于 uniapp 与 Flutter 进行通信
   3. hbuilder：打开 manifest.json ，App 原生插件配置 -> 选择本地插件 -> sn-flutter
4. 打自定义基座，然后使用自定义基座进行 uniapp 开发调试。(_参考[文档](https://ask.dcloud.net.cn/article/35115)，占用免费打包额度_)

### 基本开发

uniapp

```js
// 以 App.vue 为例
import Vue from "vue";
import { MethodChannel } from "@/js_sdk/flutter/flutter.js";

const flutter = uni.requireNativePlugin("sn-flutter");

export default Vue.extend({
  async onLaunch() {
    // ...

    // 消息通道
    const methodChannel = new MethodChannel("main");
    methodChannel.$on("change", (e) => {
      console.log("msg from flutter: ", e);
    });

    // 打开页面
    flutter.openPage({
      instanceId: "main",
      id: "main",
      params: {
        a: 1,
      },
    });
  },
});
```
