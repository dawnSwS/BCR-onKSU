# BCR-onKSU (Basic Call Recorder - KSU Edition)

[中文](#中文) | [English](#english)

## 中文

**BCR-onKSU** 是 [Basic Call Recorder (BCR)](https://github.com/chenxiaolong/BCR) 的自动化下游构建（soft-fork），面向需要 KernelSU / APatch 挂载隔离兼容补丁的用户，由本项目独立维护。

工作流每日检查上游最新发布版本，拉取对应标签，应用 [PR #913](https://github.com/chenxiaolong/BCR/pull/913) 对应的 [913.patch](913.patch)，并在构建成功后发布带 `-onKSU` 后缀的模块。跟进新版本取决于补丁兼容性和构建结果。实现见 [构建工作流](.github/workflows/auto-build.yml)。

### 下载与安装

1. 从本项目的 [Releases](https://github.com/dawnSwS/BCR-onKSU/releases/latest) 下载 `BCR-<上游版本>-onKSU.zip` 模块附件。GitHub 自动生成的 `Source code` 压缩包是源码，不是可安装模块。
2. 使用 KernelSU 或 APatch 管理器安装模块。KernelSU 用户应按 [上游安装说明](https://github.com/chenxiaolong/BCR#usage) 准备所需的 metamodule。
3. 重启设备。
4. 在开始录音前，确保 **BCR（`com.chiller3.bcr`）能够访问模块挂载的文件**。KernelSU：在 BCR 的应用配置中选择 **Custom（自定义）**，关闭 **Unmount modules（卸载模块）**。APatch：如果已对 BCR 启用模块卸载配置，请关闭该配置；选项名称以所用版本为准。修改挂载配置后再次重启设备。
5. 打开 BCR，启用录音，按提示授予所需权限并选择保存目录；进行一次测试通话，确认通知和录音文件正常。

**BCR 自身仍需访问模块中的代码和资源。请保留上述应用配置，即使已经安装本补丁。** 其他功能与使用要求请参阅 [上游文档](https://github.com/chenxiaolong/BCR#usage)。

### 修复范围与限制

[Issue #912](https://github.com/chenxiaolong/BCR/issues/912) 报告了一类通知崩溃：在特定挂载隔离配置下，`SystemUI` 无法读取 BCR 的 APK，导致通知小图标资源加载失败，并触发 `BadForegroundServiceNotificationException`。

本补丁在 BCR 进程中将通知小图标预先渲染为 `Bitmap`，通过 `Icon.createWithBitmap()` 传给通知系统，从而消除该图标对 `SystemUI` 读取 BCR APK 的依赖。其目标是避免仅为解决这一图标问题而额外安装 APK；其他 APK 可见性、权限或录音问题不在此补丁的修复范围内。

- 补丁针对上述特定崩溃路径，不保证解决所有闪退或兼容所有设备、ROM。
- 补丁不提供 Root 隐藏能力，也不保证通过银行、游戏或其他应用的检测。
- 上游提供自己的配置与安装建议，包括在 Root 隐藏环境下额外安装 APK。请结合具体故障参考 [上游说明](https://github.com/chenxiaolong/BCR#usage)。

### 构建签名与升级

本项目使用独立构建的 APK 签名，与上游官方发行签名不同。当前 [工作流](.github/workflows/auto-build.yml) 在每次执行构建时生成新的 keystore，没有复用固定签名密钥。

因此，不能假定不同构建具有相同的 APK 签名。官方版与本分支之间的切换、不同版本之间的覆盖升级，均需按实际版本验证兼容性；切换或升级前请备份录音和设置。上游官方 APK 的证书指纹不适用于验证本项目的 APK。

### 问题反馈

使用本构建遇到问题，请先向 [本仓库 Issues](https://github.com/dawnSwS/BCR-onKSU/issues) 反馈，并提供：

- 设备型号、Android 版本和 ROM。
- KernelSU / APatch 版本、BCR-onKSU 版本。
- BCR 的模块挂载配置、复现步骤及预期与实际结果。
- 与故障相关的错误日志；分享前请检查并移除个人信息。

### 鸣谢与许可证

录音功能及应用界面来自 [chenxiaolong/BCR](https://github.com/chenxiaolong/BCR)。本项目维护对应补丁的下游构建与发布；补丁背景见 [Issue #912](https://github.com/chenxiaolong/BCR/issues/912) 和 [PR #913](https://github.com/chenxiaolong/BCR/pull/913)。本构建由本项目负责支持，不代表上游官方发行或支持承诺。

许可证为 GPL-3.0，详见 [LICENSE](LICENSE)。对应版本的源码由 Release 标明的上游版本，以及该次构建使用的补丁和工作流版本组成。

---

## English

**BCR-onKSU** is an automated downstream build (soft-fork) of [Basic Call Recorder (BCR)](https://github.com/chenxiaolong/BCR), independently maintained for users who need the KernelSU / APatch mount isolation compatibility patch.

The workflow checks for the latest upstream release daily, checks out its tag, applies [913.patch](913.patch) from [PR #913](https://github.com/chenxiaolong/BCR/pull/913), and publishes a module with the `-onKSU` suffix after a successful build. New releases depend on patch compatibility and build success. See the [build workflow](.github/workflows/auto-build.yml).

### Download and installation

1. Download the `BCR-<upstream-version>-onKSU.zip` module asset from this project's [Releases](https://github.com/dawnSwS/BCR-onKSU/releases/latest). GitHub's automatically generated `Source code` archives contain source files, not an installable module.
2. Install the module through KernelSU or APatch Manager. KernelSU users should prepare the required metamodule as described in the [upstream installation instructions](https://github.com/chenxiaolong/BCR#usage).
3. Reboot the device.
4. Before recording, **ensure that BCR (`com.chiller3.bcr`) can access the files mounted by the module**. In KernelSU, set BCR's app profile to **Custom** and disable **Unmount modules**. In APatch, disable any module unmounting configuration you have enabled for BCR; option names depend on your version. Reboot again after changing the mount configuration.
5. Open BCR, enable recording, grant the required permissions when prompted, and choose an output directory. Make a test call to check that notifications and recorded files work.

**BCR still needs access to the code and resources in its module. Keep the app configuration above even with this patch installed.** See the [upstream documentation](https://github.com/chenxiaolong/BCR#usage) for other features and usage requirements.

### Scope and limitations

[Issue #912](https://github.com/chenxiaolong/BCR/issues/912) reports a notification crash under certain mount isolation configurations: `SystemUI` cannot read BCR's APK, so loading the notification's small icon fails and triggers `BadForegroundServiceNotificationException`.

The patch renders the small icon into a `Bitmap` inside BCR and passes it to the notification system through `Icon.createWithBitmap()`. This removes the icon's dependency on `SystemUI` reading BCR's APK. It aims to avoid an additional APK installation solely to address this icon issue; other APK visibility, permission, or recording problems are outside the patch's scope.

- The patch addresses this specific crash path. It does not guarantee that all crashes are fixed or that every device and ROM is compatible.
- The patch does not provide Root hiding or guarantee passing checks performed by banking apps, games, or other applications.
- Upstream provides its own configuration and installation guidance, including an additional APK installation when using Root hiding. Consult the [upstream instructions](https://github.com/chenxiaolong/BCR#usage) for the problem you encounter.

### Build signing and upgrades

This project uses its own APK signing identity, which differs from official upstream releases. The current [workflow](.github/workflows/auto-build.yml) generates a new keystore each time it builds and does not reuse a fixed signing key.

Do not assume that separate builds have the same APK signature. Compatibility when switching between upstream and this project, or upgrading over an existing installation, needs verification for the versions involved. Back up recordings and settings before switching or upgrading. The official upstream APK certificate fingerprint cannot verify this project's APKs.

### Reporting issues

For problems with this build, start with [this repository's Issues](https://github.com/dawnSwS/BCR-onKSU/issues) and include:

- Device model, Android version, and ROM.
- KernelSU / APatch version and BCR-onKSU version.
- BCR's module mount configuration, reproduction steps, and expected and actual behavior.
- Error logs relevant to the failure; review and remove personal information before sharing.

### Credits and license

The recording functionality and app interface come from [chenxiaolong/BCR](https://github.com/chenxiaolong/BCR). This project maintains downstream builds and releases with the compatibility patch; see [Issue #912](https://github.com/chenxiaolong/BCR/issues/912) and [PR #913](https://github.com/chenxiaolong/BCR/pull/913) for background. This project supports its own builds; they are not official upstream releases and carry no upstream support commitment.

Licensed under GPL-3.0; see [LICENSE](LICENSE). The source for a version consists of the upstream version identified in its Release and the patch and workflow versions used for that build.
