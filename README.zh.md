<!--idoc:ignore:start-->
> [!TIP]
> 声明：此项目并非开源项目，仓库作为官方网站，用于收集问题和用户需求。这样做是为了节省成本，因为没有官网，应用无法通过审核。
<!--idoc:ignore:end-->

<div align="center">
  <br />
  <br />
  <img src="./assets/logo.png" width="160" height="160">
  <h1>
    Vidwall Hub
  </h1>
  <!--rehype:style=border: 0;-->
  <p>
    <a href="./README.md">English</a> • 
    <a href="https://wangchujiang.com/vidwall/" target="_blank">Vidwall</a> • 
    <a target="_blank" href="https://github.com/jaywcjlove/vidwall-hub/issues/new?template=bug_report_cn.yml">联系&支持</a> • 
    <a href="./CHANGELOG.zh.md">更新日志</a>
  </p>
  <p>
    <a target="_blank" href="https://github.com/jaywcjlove/vidwall-hub/releases/latest/" title="Vidwall Hub for macOS">
      <img alt="Vidwall Hub AppStore" src="https://jaywcjlove.github.io/sb/download/apple-download.svg" height="51" />
    </a>
  </p>
</div>

<div align="center">

最低操作系统要求：`macOS Tahoe 26+`

</div>

![Vidwall Hub](./assets/VidwallHub.gif)

![Vidwall Hub](./assets/vidwall-hub-screenshots-1.png)

**Vidwall Hub** 是一款可以轻松将视频（`mp4`、`mov`）导入系统壁纸服务，并在 `系统设置` 中用作锁屏动画的工具。

在尝试通过 [Vidwall](https://github.com/jaywcjlove/vidwall) 应用同时实现动态壁纸和动态锁屏功能时，由于 macOS 沙盒限制，这一功能无法实现。因此，我将测试后的代码独立成一个新应用 免费提供给大家使用，作为 [Vidwall](https://github.com/jaywcjlove/vidwall) 的补充。即使独立运行、绕过沙盒限制，仍无法直接设置动态锁屏，因为 macOS 并未提供相关 API。Vidwall Hub 仅将视频导入系统壁纸服务，用户需要在系统设置的壁纸选项中完成最终应用。

```shell
$ brew install --cask jaywcjlove/tap/vidwall-hub
```

### URL Scheme

**Vidwall Hub** 支持通过 URL 激活工具，并根据 URL 中的视频文件路径参数自动导入视频。

```bash
vidwallhub://open?file=/file/to/path/video.mp4
```

### 锁屏（macOS 26+）

基于当前实现，Vidwall Hub 的锁屏导入流程如下（用户态，无需管理员权限）：

1. **读取系统清单**：加载 `~/Library/Application Support/com.apple.wallpaper/aerials/manifest/entries.json`。  
2. **创建 Vidwall 分类（首次）**：若还没有 Vidwall 分类，会创建 `Vidwall Videos` 分类与子分类。  
3. **生成资源条目**：为每个导入视频创建一个 `asset`，并写入 `entries.json`。  
4. **写入缩略图**：使用视频首帧生成 PNG 缩略图，写入 `~/Library/Application Support/com.apple.wallpaper/aerials/thumbnails/<UUID>.png`。  
5. **写入视频文件**：  
   - `.mov`：复制到 `~/Library/Application Support/com.apple.wallpaper/aerials/videos/<UUID>.mov`  
   - `.mp4`：通过 `AVAssetExportPresetPassthrough` 导出为 `.mov` 后写入上述目录  
6. **刷新壁纸服务**：执行  
   `killall Wallpaper WallpaperAgent WallpaperAerialsExtension WallpaperImageExtension WallpaperLegacyExtension`  
   让系统立即重新加载资源。

**资源占用说明：**

- 导入过程中会产生一次性的文件读写与（仅 `.mp4`）封装转换开销。  
- 导入完成后，播放由系统壁纸相关进程负责；Vidwall Hub 不会持续参与渲染，也不会常驻占用额外 CPU/GPU 资源。  
- 应用的职责仅是管理 `entries.json` 与素材文件（视频/缩略图）的导入、更新和删除。

**macOS 15 暂时不支持**

```
/Library/Application Support/com.apple.idleassetsd/Customer/4KSDR240FPS
/Library/Application Support/com.apple.idleassetsd/Customer/entries.json
```

<!--idoc:config:
title: Vidwall Hub
keywords: Vidwall Hub,视频壁纸,锁屏动画,macOS 工具,系统效率
description: Vidwall Hub 是一款可以轻松将视频（mp4、mov）导入系统壁纸服务，并在系统设置中将其用作锁屏动画的工具。
-->