# 涩涩漫画（sscomic.top）— Tachimanga 扩展仓库

给 iOS/macOS 的 [Tachimanga](https://tachimanga.app/) 用的非官方扩展仓库。

## 安装

Tachimanga 里 **更多 → 扩展 → 扩展仓库 → 添加**，填这条：

```text
https://raw.githubusercontent.com/teng662858/sscomic-tachimanga/refs/heads/main/index.pb
```

不认 `index.pb` 的老版本客户端用兼容入口：

```text
https://raw.githubusercontent.com/teng662858/sscomic-tachimanga/refs/heads/main/index.min.json
```

> **为什么用 raw 而不是 GitHub Pages**：国内网络下 `github.io` 基本连不上（PC 走代理能通、手机常常不行），而 `raw.githubusercontent.com` 一般能通——你已有的那些仓库也都在 raw 上，所以以它为准。
> 备用镜像（jsDelivr，同样是本仓库的内容）：
> ```text
> https://cdn.jsdelivr.net/gh/teng662858/sscomic-tachimanga@main/index.pb
> ```

添加后回到扩展页刷新，安装「SsComic」即可；源的名字在 App 里显示为**涩涩漫画**。

> iOS 端 Tachimanga 下载的是 `jarUrl`（不是 apkUrl）。本仓库两者都有：`.apk` 给安卓阅读器（Mihon 等），`.jar` 给 iOS 的 Tachimanga。JAR 是「文本 AndroidManifest.xml + JVM .class」的形式，这也是 iOS 端能直接加载的形态。

## 不可变的三样东西

更新版本时必须保持不变，否则 Tachimanga 会当成另一个扩展，已安装用户无法升级：

| 项目 | 值 |
|---|---|
| 包名 | `eu.kanade.tachiyomi.extension.zh.sscomic` |
| 源 ID | `2587594711397904199` |
| 签名证书 SHA-256 | `6e08dd197ec331011d131859ce401ab504a68662cd3cc621570f336fe61db137` |

签名私钥在本地 `../keystore.jks`（**不要提交、请离线备份**；换密钥 = 老用户必须卸载重装）。

## 版本规则

- `versionCode` 每次发版 +1（当前 `1`），`versionName` 跟着走（当前 `1.4.1`，其中 `1.4` 是对应的扩展库版本）。
- 仓库索引 `index.min.json` 的 `code` 必须等于 JAR 里的 `android:versionCode`，否则 App 判断不出更新。

## 出包流程

源码与构建脚本在上一层目录（不在本仓库里）：

```powershell
# 1) 编出并签名 JAR（不需要 Android Studio / Android SDK）
powershell -ExecutionPolicy Bypass -File ..\tools\build_jar_nosdk.ps1

# 2) 把新 JAR 放进本目录并改索引
copy ..\apk\sscomic-v1.4.2.jar .\apk\
#    然后同步修改 index.json / index.min.json 里的文件名与版本号

# 3) 重新生成 protobuf 索引
node ..\tools\make_index_pb.mjs

# 4) 校验 JAR 结构
powershell -ExecutionPolicy Bypass -File ..\tools\inspect_jar.ps1 -Jar .\apk\sscomic-v1.4.2.jar
```

## 声明

- 与 sscomic.top、Tachimanga 官方均无关系。扩展只解析网站公开可访问的页面，内容与可用性由原站决定。
- 站点内容为成人向，索引里已标 `CONTENT_WARNING_NSFW`（`nsfw: 1`）。
- 站点若变更域名或改版，扩展需要重新构建发布。
