# 更新日志

VoiceStudio 的所有重要变更。

格式大致基于 [Keep a Changelog](https://keepachangelog.com/)。
`package.json` 是应用版本的权威来源；Electron 包元数据和后端回退版本与之保持一致。

## [未发布]

## [0.5.7] — 2026-10-26

**首先，致歉。** 这个版本比预期拖得久。紧急家事让我离开了一段时间，为等待和期间无人回复的 Issue 致歉。感谢大家的耐心，以及期间的每一份报告、修复和善意留言。

**欢迎 Shimul Debnath（[@velixio](https://github.com/velixio)）加入核心团队。** Shimul 现在协助维护 VoiceStudio——审查 Pull Request、分类 Issue、推动发布——项目不再只依赖一个人。

**VoiceStudio 现在可以在手机上运行，也能在没有 GPU 的 PC 上运行，整个应用更沉稳了。** iOS 和 Android 预览版应用在手机上本地运行模型，无 GPU 的电脑安装小型 CPU PyTorch 构建，侧边栏、Models、Dubbing、Stories 和 Workflows 页面围绕更少、更清晰的控制项重新设计。

本版本还新增了可选的 Beta 更新通道、录音片段底部播放器、如实报告 Radeon 状态的 GPU 加速面板和 Ask VoiceStudio Agent，以及数百项针对克隆、配音、有声书、下载和听写的修复。

**下载**

| 平台 | 安装包 |
| --- | --- |
| Windows x64 | [安装包](https://github.com/debpalash/VoiceStudio/releases/download/v0.5.7/VoiceStudio-0.5.7-win-x64.exe) |
| Windows ARM64（实验性） | [安装包](https://github.com/debpalash/VoiceStudio/releases/download/v0.5.7/VoiceStudio-0.5.7-win-arm64.exe) |
| macOS Apple Silicon | [DMG](https://github.com/debpalash/VoiceStudio/releases/download/v0.5.7/VoiceStudio-0.5.7-mac-arm64.dmg) |
| macOS Intel | [DMG](https://github.com/debpalash/VoiceStudio/releases/download/v0.5.7/VoiceStudio-0.5.7-mac-x64.dmg) |
| Linux x64 | [AppImage](https://github.com/debpalash/VoiceStudio/releases/download/v0.5.7/VoiceStudio-0.5.7-linux-x64.AppImage) · [deb](https://github.com/debpalash/VoiceStudio/releases/download/v0.5.7/VoiceStudio-0.5.7-linux-x64.deb) |
| Android | [APK](https://github.com/debpalash/VoiceStudio/releases/download/v0.5.7/VoiceStudio-0.5.7-android.apk) · [AAB](https://github.com/debpalash/VoiceStudio/releases/download/v0.5.7/VoiceStudio-0.5.7-android.aab) |
| iPhone | [未签名 IPA](https://github.com/debpalash/VoiceStudio/releases/download/v0.5.7/VoiceStudio-0.5.7-ios-unsigned.ipa)（用你自己的 Apple ID 侧载） |

**升级：** 覆盖安装现有 Electron 应用即可；声音、项目和设置会保留。如果安装程序询问，选择 **Install local runtime** 以刷新依赖。

- 远程计算设备、远程 worker 和 GPU 共享已移至 VoiceStudio Pro，不再包含在开源应用中；已保存的项目、声音和设置不受影响（#2748）
- 没有明确商业许可的模型（包括默认的 OmniVoice 模型）需要在 Model Catalogue 中接受一次许可；在此之前，使用它们的 API 和 MCP 请求会返回许可错误（#2689）
- 通过主机名访问 Docker、LAN 或反向代理，且没有 API key 或会话时，需要将该名称加入 `OMNIVOICE_ALLOWED_HOSTS`；IP 地址、`localhost`、`*.ts.net` 和 `host.docker.internal` 照常可用。
- 从自有网络上的服务器导入 URL 需要 `OMNIVOICE_ALLOW_PRIVATE_URL_IMPORTS=1`。
- [这些变量的设置位置](https://github.com/debpalash/VoiceStudio/blob/v0.5.7/docs/api-auth.md#requests-from-other-websites-and-host-names)：桌面应用在 `~/.config/omnivoice/env`（macOS、Linux）或 `%USERPROFILE%\.config\omnivoice\env`（Windows）中写一行；Docker 使用 `-e`。

**从 Tauri 迁移：** 关闭应用，备份其数据目录，安装 Electron，并在移除 Tauri 前验证声音和项目。请遵循[迁移指南](https://github.com/debpalash/VoiceStudio/blob/v0.5.7/docs/electron-migration.md)。Tauri v0.5.3 是最后一个 Tauri 版本；其更新器无法安装 Electron。

**安装包信任：** macOS 安装包已签名。Windows 安装包尚未代码签名，SmartScreen 可能警告；下方 **Installer signing** 表显示各平台的结果。参见[安装指南](https://github.com/debpalash/VoiceStudio/blob/v0.5.7/docs/install/troubleshooting.md)。

**亮点**

- iOS 和 Android 预览版应用可在手机上运行 VoiceStudio，下载模型后可离线使用
- 更沉稳的应用：重新设计的侧边栏、每个系列一个 Models 列表、更简洁的 Dubbing、Stories 和 Workflows 页面，以及录音片段底部播放器
- 没有独立 GPU 的 PC 安装小型 CPU PyTorch 构建即可运行；Windows on ARM 获得实验性原生安装包（#2500、#2611）
- 声音克隆会找到你从 Model Catalogue 安装的语音转文字模型，并解释过长的片段（#2442、#2498）——感谢 @drakeo338、@Bad-ptr！
- Settings → Updates 中可选的 Beta 更新通道：提前获得修复，并自动滚动到下一个稳定版本

### 变更

- 桌面下载文件现在命名为 `VoiceStudio-<version>-<platform>-<arch>`（不含 "Electron"）；安装脚本仍能找到旧版本的文件（#3504）
- 所有可折叠区域使用相同的箭头，Target languages 和 Cast 等标签不再用大写喊叫（并修复了若干错误翻译），Home 的贡献者头像不再在卡片上方留出空隙（#3497）
- 更沉稳的界面：标题栏链接位于搜索旁，不再看起来像页面标题；Transcribe 和 Stories 不再重复同一提示；"1 chapter" 正确显示；Directorial AI 工具用平实语言描述（#3493）
- Dubbing 更沉稳：单行源文件带 File actions 菜单、已加载文件的预览、无声音文件显示为提示而非错误，以及更整洁的 Dub into 面板（#3465）
- Workflows 更易用：模板从选择器打开，步骤从画布上的工具栏添加，名称在标题栏编辑，缺少设置的步骤在运行前标记（#3464）
- 更沉稳的标题栏和侧边栏：Get Pro 和 Star 按钮更安静地位于一行，页面标题只显示一次，带标签的速度/质量控制及 Auto 提示，导航行高度均匀（#3454）
- Colab notebook 现在在安装期间下载语音转文字模型，因此在 Web UI 中克隆的声音可立即获得转录（#3440）——感谢 @Liularina！
- 模型下载在各处显示一致：排队、带速度和剩余时间的进度、验证中、已取消或失败（带 Retry 和确切的下一步），运行中的下载出现在通知铃中（#3437）
- 语言选择器先列出支持的语言，其余放在一个 Show all languages 行后，最近选择显示在顶部，地区变体归组在其语言下（#3431）
- Voice refinement 提示现在说明较少的 pass 可能降低质量（#3412、#3429）
- Home 贡献者署名显示每个人当前的 GitHub 头像，每周刷新，并仍为离线使用打包（#3407）
- 移动端 Home 保持标题和贡献者头像紧凑，带维护者卡片和刷新的 GitHub 照片（#3406）
- 侧边栏设备状态显示为 Local（#3399）
- Settings → Models 每个系列显示一个列表：在同一行安装、许可和选择每个引擎及其模型，多模型引擎内联展开（#3398）
- 演示声音称为 Demo Voice，库标签页显示为 Saved 和 Recents（#3397）
- 第二个侧边栏使用一个干净、平坦的背景，带轮廓分区（#3396）
- 侧边栏的设备及速度/质量区块更沉稳、对齐，带勾选滑块（#3390）
- 折叠的侧边栏显示 VoiceStudio 标志，悬停时变为展开图标（#3381）
- Stories 有更沉稳的脚本编辑器：行按文本大小调整，章节标题显示为标题，每行有一行说话人、声音和语气标签，设置面板使用整洁的可折叠分区（#3374）
- Home 上的核心团队卡片以更友好的卡片显示全名，并跟随应用主题（#3372）
- Velixio 的核心团队卡片链接其网站（#3362）
- Velixio 的核心团队卡片链接其 X 个人资料（#3356）
- Latest take 显示一行安静详情，脚本位于其标题旁，库标签页有图标（#3354）
- Home 现在在顶部以大尺寸显示核心维护者及个人资料卡片，其他贡献者以较小尺寸显示在下方（#3353）
- Report a bug 要求一行描述，并在打开预填的 GitHub Issue 前列出将发送的内容（#3338）
- 首次运行设置显示安装文件夹及可用空间，长标签换行而非裁剪 Next，macOS 窗口按钮与标题栏对齐（#3336）
- 凭据字段有可见标签，令牌检查显示 Verified/Failed 徽章；命令面板现在能找到设置页面和项目（#3335）
- 引擎状态屏幕是单个胶囊，带已用时间，重复失败被计数，原始日志位于 Details 下，繁忙引擎显示细长的重连条（#3334）
- 引擎启动失败现在指出一个原因（启动被阻止、原生崩溃、缺少环境、权限、同步文件夹），并以匹配的操作开头：Choose another folder、Copy diagnostics 或 Retry（#3332）
- 生成失败现在指出可能原因（GPU 内存、缺少模型、引擎繁忙、超时），带修复标签，原始错误位于 Details 下，且绝不显示代理 HTML 页面（#3331）
- Android 和 iOS 新增原生生产工作区、书籍导入和 MP3/M4B 导出、详细配音编辑和音频工具、可恢复处理、小组件、配对通话和本地语音 API，并在保存前生成真实的克隆声音预览（#3325）
- Android 匹配 iOS 工作区布局，带紧凑的 Home、分组控件和可访问的图标导航（#3312）
- 更新日志条目现在在 `changelog.d/` 中每个 Pull Request 一个小文件，发布时合并到发布说明中，因此 Pull Request 不再在 `CHANGELOG.md` 上冲突（#3282）
- 侧边栏是每个分区自己面板旁的细长图标栏：Home 和 Clone 获得 Saved voices / Recent takes 列，Design 获得 Saved voices 标签页，完整导航一键可达（#3234）
- 顶栏的 GitHub 按钮是一个带实时计数的 Star 胶囊，悬停时星星填充金色（#3234）
- Stories、Audiobook 和 Dubbing 在控件旁的 Projects 标签页中显示已保存项目，因此无论你在哪里工作，文件都一键可达（#3234）
- 源 ZIP 和 tarball 省略诈骗审核攻击示例；Git 克隆和 CI 保留其回归测试（#3237）
- 维护者可用 `make virustotal` 上传公共源 ZIP 或单个文件，或通过手动 VirusTotal Actions 工作流扫描分支（#3237）
- 小型模型现在只需 2 GB 空闲磁盘即可安装；设置检查仅在低于 10 GB 时警告而非阻止（#2969）
- iOS Home 显示 Electron 的贡献者头像，Dub 围绕一个带动态图标的源卡片重新设计，iOS 底部 dock 仅图标（#2969）
- iOS 和 Android 上的模型许可审查以平实摘要、权利行和许可链接打开；原始条款移至 Exact terms 下（#2969）
- 手机应用现在使用应用 ID `sh.voicestudio.mobile`；之前安装的预览版会作为新应用并存安装，因此请重新配对并移除旧版（#3194）
- 赞助商页脚已从应用中移除；赞助集成在其 Integrations 卡片上保留 Featured 徽章（#3031）
- 展开的侧边栏在 VoiceStudio 下方显示已安装应用版本，并清楚标记开发构建（#2894）
- Pro 页面仅列出 Pro 新增内容；recipes、watch folders 和声音克隆保持免费（#2587）
- 应用标识及后端、浏览器和 Electron 开发端口现在来自一个经过验证的配置文件（#2759）
- 最新录音片段出现时，合成控件保持在底部锚定（#2759）
- 使用 Gallery 设计声音会立即打开其工作区，并将模型加载推迟到请求音频时（#2759）
- 没有明确商业许可的模型（包括默认的 OmniVoice 模型）必须在使用前接受一次许可；VoiceStudio 不是许可方，无法授予模型权利（#2689）
- 模型许可对话框提供平实摘要、许可允许的内容和可折叠详情；接受会记录日期和确切条款，条款变更会再次询问（#2689、#2755）
- Pro、导出和企业文本在所有 21 种语言中明确说明应用许可不授予模型或生成音频的权利（#2587）
- 桌面安装包包含应用的许可声明（#2587）
- Home 卡片使用功能特定的 SVG 图标，带简短的悬停和键盘焦点动画，并尊重减少动态效果设置（#2407）
- 将 Dub 工作区在导航、项目标签和键盘快捷键中重命名为 Dubbing（#2407）
- Home 优先显示创建工具，带更清晰的描述和控件；页脚可折叠并重新打开（#2407）
- Enter Studio 打开其可选权限和快捷键设置，默认展开（#2407）
- 模型包可在预设兼容引擎激活前选择和安装，更改仅在安装或使用所选包时应用（#2407）
- 首次运行设置在下载依赖前停止 Intel Mac，并解释远程后端访问（#2419）——感谢 @JoAdo07！
- 没有 NVIDIA GPU 的机器安装小型 CPU PyTorch 构建（约 5 GB 而非 9 GB 空闲），并以 float32 而非 float16 加载声音模型（#2500）——感谢 @RobAsticles、@tiropictor！
- 本可使用 GPU 的引擎在已安装 PyTorch 无法使用时报告 CPU 回退，并指出显卡（#2468、#2501）——感谢 @Pates2004！
- 设置指出你的 Radeon 显卡，而非归咎于缺少 NVIDIA 驱动，并在 Windows 和 macOS 上忽略 ROCm 覆盖（#2468、#2501）——感谢 @Pates2004！
- 源设置在选择 CPU wheel 后安装依赖，并在重启时保留；开发启动在启动 Vite 前解析 Electron 二进制（#2436）——感谢 @RobAsticles！
- 音频质量和 Voice 控件在 Clone 和 Voice Design 上从 Synthesize 框以紧凑弹出层打开（#2419）
- Synthesize 按钮在按钮内以键帽形式显示其键盘快捷键（#2419）
- 语言菜单保持在窗口内，不在边缘裁剪（#2419）
- Choose a voice 新增照片卡片、Newest/A–Z 排序、搜索，以及点击或拖放照片上传（#2419）
- 侧边栏新增 Fast、Balanced、Quality 和 Max 选项、单独的 Auto 开关、更安静的引擎状态，以及紧凑的 Simple、Models 和 Details 视图（#2419）
- 侧边栏新增硬件感知的 Auto，带实时资源使用和 TTS 优先的模型选择，并将紧凑引擎状态与基于图标的 Simple、Models 和 Details 视图分组（#2396）
- LLM 设置在启用功能前验证模型响应，用连接指导替换误导性的引擎清单，并支持经过身份验证的本地服务器（#2397）
- LLM 技能可通过 repair-agent runner 使用已安装的编码 CLI，通过 LiteLLM 使用原生付费提供商（#2397）
- Dubbing 支持 Google Cloud Translation 和 Amazon Translate，带明确的提供商设置（#2397）
- OmniVoice sidecar 对没有转录的短参考复用已安装的语音识别，与进程内克隆匹配（#2320）——感谢 @ewh1tman！
- Electron 从操作系统拒绝的默认后端端口恢复，而不更改显式配置的端口（#2358）——感谢 @rishi2288！
- Home 直接打开项目操作，Integrations 仅列出已完成应用内设置的连接器（#2351）
- 多 GPU NVIDIA 主机可选择 VoiceStudio 及其引擎子进程使用哪个物理 CUDA 适配器（#2346）——感谢 @z0tedd！
- 另一台机器上的 MCP 客户端可通过 PIN 门控的 Local network sharing 连接，仅在共享启用时接纳 LAN 主机（#2347）——感谢 @z0tedd！
- Docker、Web 设置和文档现在使用规范镜像 `ghcr.io/debpalash/voicestudio`，旧 GHCR 路径仍兼容（#2350）
- GHCR 在现有路径旁发布 VoiceStudio 容器坐标，并回填发布标签以安全迁移（#2348）
- Electron 现在是唯一的桌面和 Web UI，原生辅助程序、打包、设置、Docker 和 Network Sharing 由维护的 Electron 路径负责（#2343）
- Docker、`build:web` 和 `dev:web` 现在构建维护的 Electron 渲染器，而非归档的浏览器界面（#2341）
- Twilio 设置是引导式清单：账户、隧道、电话号码和语音步骤，带实时状态、适用于你的操作系统和