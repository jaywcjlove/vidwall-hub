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
    <a href="https://github.com/jaywcjlove/vidwall-hub/releases">
      <img src="https://img.shields.io/github/v/release/jaywcjlove/vidwall-hub?color=3b82f6" alt="Release" />
    </a>
    <a href="https://github.com/jaywcjlove/vidwall-hub/releases/latest/">
      <img src="https://img.shields.io/github/downloads/jaywcjlove/vidwall-hub/total?color=22c55e" alt="Downloads" />
    </a>
    <a href="#brew-安装">
      <img src="https://img.shields.io/badge/Homebrew-available-orange?logo=homebrew&logoColor=white" alt="Homebrew" />
    </a>
    <img src="https://img.shields.io/badge/macOS-26%2B-363b44?logo=apple&logoColor=white" alt="macOS 26+" />
  </p>
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

**Vidwall Hub** 是一款可以轻松将视频（`mp4`、`mov`、`m4v`）导入系统壁纸服务，并在 `系统设置 → 墙纸` 中用作锁屏动画的工具。支持 **macOS 26** 与 **macOS 27**。

在尝试通过 [Vidwall](https://github.com/jaywcjlove/vidwall) 应用同时实现动态壁纸和动态锁屏功能时，由于 macOS 沙盒限制，这一功能无法实现。因此，我将测试后的代码独立成一个新应用 免费提供给大家使用，作为 [Vidwall](https://github.com/jaywcjlove/vidwall) 的补充。macOS 并未提供设置动态锁屏的公开 API。Vidwall Hub 将视频登记到系统 Aerial 壁纸目录，并写入当前壁纸配置；导入后也可在系统设置的墙纸选项中查看或切换。

### Brew 安装

```shell
# 安装（自动到 /Applications）
$ brew install --cask jaywcjlove/tap/vidwall-hub
```

### URL Scheme

**Vidwall Hub** 支持通过 URL 激活工具，并根据 URL 中的视频文件路径参数自动导入视频。

```bash
vidwallhub://open?file=/file/to/path/video.mp4
```

### 锁屏（macOS 26 / macOS 27）

macOS 没有设置动态锁屏的公开 API。Vidwall Hub 仍走系统 Aerial 壁纸通道（用户态，无需管理员权限），目录与 macOS 26 相同：

```
~/Library/Application Support/com.apple.wallpaper/aerials/manifest/entries.json
~/Library/Application Support/com.apple.wallpaper/aerials/videos/<UUID>.mov
~/Library/Application Support/com.apple.wallpaper/aerials/thumbnails/<UUID>.png
~/Library/Application Support/com.apple.wallpaper/Store/Index.plist
```

导入时**只补丁** Vidwall 自己的分类和资源条目，不会用整份重写覆盖系统自带的 Aerial 目录。

**共用流程：**

1. **读取系统清单**：加载 `entries.json`（只读解析，不整文件重写）。  
2. **创建 Vidwall 分类（首次）**：若还没有 Vidwall 分类，会创建 `Vidwall Videos` 分类与子分类。  
3. **生成资源条目**：为每个导入视频创建一个 `asset`，以补丁方式写入 `entries.json`（视频地址使用 `file://` URL）。  
4. **写入缩略图**：使用视频首帧生成 PNG 缩略图。  
5. **写入视频文件**：见下方按系统版本的差异。  
6. **设为当前壁纸**：写入 `Store/Index.plist`，将刚导入的 `assetID` 设为 `com.apple.wallpaper.choice.aerials`。  
7. **刷新壁纸服务**：重启 `WallpaperAgent` 及相关 Aerial 扩展，让系统重新加载资源。  
8. **删除**：从清单中移除该条目并删除对应文件；若删的是当前壁纸，会改指到其它 Vidwall 视频或一条系统自带 Aerial，避免系统设置里留下幽灵记录或把默认壁纸列表清空。

**macOS 26：**

- `.mov`：原样复制到 `aerials/videos/<UUID>.mov`。  
- `.mp4` / `.m4v`：通过 `AVAssetExportPresetPassthrough` 导出为 `.mov`（不重编码，保留完整时长）。  
- 若「系统设置」已打开，会先切到「通用」再写目录（避免壁纸面板写回旧数据），这是 26 上原来就在用的做法。

**macOS 27：**

macOS 27 的 `WallpaperAerialsExtension` 需要 HEVC 时序可分层（`tscl` / `tsas`）。普通用户视频通常没有这些采样组，锁屏第一次解锁后会冻结或变黑。

- 若视频**已有**时序层：直接复制为 `.mov`。  
- 若**没有**：在后台重编码为 HEVC Main 10（带时序层），锁屏循环只需要短片，因此最多转前 **30 秒**。  
- 导入时不切换系统设置面板、不结束 `Wallpaper` 扩展进程，避免拖放时把壁纸设置打崩。  
- 在应用中点击缩略图，会把该视频设为当前 Aerial 壁纸/锁屏。

**资源占用说明：**

- macOS 26：一次性文件读写，以及（仅 `.mp4` / `.m4v`）容器封装。  
- macOS 27：若需时序层转码，导入时会有一次性编码开销（限 30 秒）；已兼容的 `.mov` 仍是拷贝。  
- 导入完成后，播放由系统壁纸相关进程负责；Vidwall Hub 不会持续参与渲染，也不会常驻占用额外 CPU/GPU 资源。  
- 应用的职责是管理 `entries.json`、`Index.plist` 与素材文件（视频/缩略图）的导入、更新和删除。

**macOS 15 暂时不支持**

```
/Library/Application Support/com.apple.idleassetsd/Customer/4KSDR240FPS
/Library/Application Support/com.apple.idleassetsd/Customer/entries.json
```

<!--idoc:config:
title: Vidwall Hub
keywords: Vidwall Hub,视频壁纸,锁屏动画,macOS 工具,系统效率
description: Vidwall Hub 是一款可以轻松将视频（mp4、mov、m4v）导入系统壁纸服务，并在系统设置中将其用作锁屏动画的工具。支持 macOS 26 与 macOS 27。
-->