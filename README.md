# 📞 BCR-onKSU (Basic Call Recorder - KSU Edition)

[中文](#中文) | [English](#english)

---

## 中文

**BCR-onKSU** 是优秀的开源通话录音模块 [Basic Call Recorder (BCR)](https://github.com/chenxiaolong/BCR) 的自动化软分支 (Soft-fork)，专为 **KernelSU (KSU)** 和 **APatch** 用户量身定制。

本仓库通过 GitHub Actions 等工具全自动追踪原版主线更新，并原生集成了针对 KSU/APatch 隐藏模块机制的防崩溃补丁（[PR #913](https://github.com/chenxiaolong/BCR/pull/913)）。

### 🐛 为什么需要这个分支？(修复了什么 BUG？)

为了躲避银行或游戏 App 的 Root 检测，KernelSU / APatch 用户通常会开启**“卸载模块 (Unmount modules)”**功能。但在原版 BCR 中，这会引发一个致命崩溃：

1. **原版的崩溃原因 (Issue #912)：** 
   在开启“卸载模块”的挂载隔离状态下，系统界面（`SystemUI`）无法读取被隐藏的 BCR 物理 APK 路径，从而无法加载录音通知的图标资源。这会触发 Android 严格的前台服务规则，抛出 `BadForegroundServiceNotificationException` 异常，导致系统**直接强杀 BCR 进程，录音闪退**。
2. **原版官方的妥协方案：**
   原版 README 建议将 APK 提取并手动安装到 `/data` (用户数据) 分区。但这会让应用包名暴露在标准 App 的查询范围内，增加被反作弊扫描到的风险，破坏了 KSU / APatch“纯粹无痕 (Systemless)”的初衷。

### ✨ 本项目的解决方案 (PR #913)

本分支原生合并了 [**PR #913**](https://github.com/chenxiaolong/BCR/pull/913) 补丁：
* **技术原理：** 我们在通知发出前，提前在 BCR 进程内部将矢量图标渲染为**纯像素位图 (Bitmap)**，并将纯图像数据直接传递给 `SystemUI`。
* **实际效果：** 彻底解除了状态栏通知对物理 APK 路径的依赖！**你不再需要把 BCR 安装到 `/data` 分区。** 在 KSU / APatch 严格的隔离环境下，通知依然完美显示，彻底告别闪退问题。

### 📦 下载与安装

1. 前往本项目的 [Releases 页面](../../releases/latest) 下载最新的 `.zip` 模块包。
2. 在 KernelSU 或 APatch 中刷入并重启设备即可。
3. *(提示：你可以放心大胆地在 KSU 中为 BCR 开启“卸载模块”，它现在能完美运行了！)*

### ❤️ 鸣谢与声明

* 绝佳的通话录音底层逻辑与 UI 完全归功于原项目作者 **[@chenxiaolong](https://github.com/chenxiaolong)**。
* 原作者明确表示 BCR 期望在标准的 Android 环境下运行，不接纳针对特定 Root 隐藏机制的绕过补丁 (Workaround)。我们完全理解并尊重这一严谨的开源理念。本分支仅作为全自动构建的附属仓库，在不打扰上游开发的前提下，为特定极客受众提供便利。

---

## English

**BCR-onKSU** is an automated soft-fork of the amazing [Basic Call Recorder (BCR)](https://github.com/chenxiaolong/BCR), tailored specifically for **KernelSU** and **APatch** users. 

This repository automatically tracks upstream updates and applies a native compatibility patch ([PR #913](https://github.com/chenxiaolong/BCR/pull/913)).

### 🐛 What bug does this fix? (About PR #913)

If a KernelSU or APatch user enables the **"Unmount modules"** feature to pass root detections, the official BCR app will immediately crash upon starting a recording.
*   **The Root Cause**: Because of the isolated mount namespace, `SystemUI` cannot access the physical APK path to load the notification icon. This throws a fatal `BadForegroundServiceNotificationException`, and the Android OS strictly kills the BCR process.
*   **The Upstream Workaround**: The official README suggests installing the APK manually to the `/data` partition. However, doing so exposes the app to PackageManager queries (e.g., from banking apps), defeating the purpose of a 100% stealthy, systemless environment.

### ✨ How this fork solves it
This fork natively merges [**PR #913**](https://github.com/chenxiaolong/BCR/pull/913). It fundamentally decouples the notification icon from the file path by rendering the icon into a pure memory **Bitmap** before passing it directly to `SystemUI`.
*   **The Result**: You can enjoy fully working BCR with notifications, zero crashes, and **no need** to install the APK to the `/data` partition. Your environment stays 100% systemless and undetectable!

### 📥 Download & Install
Grab the latest module `.zip` from the [Releases page](../../releases/latest) and flash it via KernelSU or APatch.

### 🙏 Credits & Disclaimer
All core logic, UI, and immense credits go to the original author [@chenxiaolong](https://github.com/chenxiaolong). The maintainer understandably decided to keep root-hiding workarounds out of the core project to maintain standard Android compliance. We completely respect this design philosophy; this fork simply exists as an automated alternative for users with strict root-hiding environment needs.