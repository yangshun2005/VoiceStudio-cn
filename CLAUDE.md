## 活动桌面端：仅 Electron

Electron（`electron/`）是唯一的桌面端和 Web UI。Tauri 外壳和旧版 UI 入口已移除；不要为它们恢复构建、运行时、发布或 CI 路径。来自最终 Tauri 安装的分诊报告应指向迁移指南，并保留其不可变的更新源。`electron/src/shared/` 下的部分模块仍由 Electron 临时共享；它们不是可运行的应用。新的 UI、IPC、安装说明、测试和浏览器资源应放在 Electron 中；在 macOS、Windows 和 Linux 上验证 Electron。

## 移动端：原生 iOS 和 Android（负责人设定 2026-10-17）

每个发布版本都从同一个发布标签和版本构建桌面端、iOS、Android 和 Docker（负责人设定 2026-10-26）。手机下载应放在同一个 GitHub Release 中；缺少某个渠道就是发布缺陷。

`mobile/ios/`（Swift/SwiftUI）和 `mobile/android/`（Kotlin/Compose）是原生应用，**在手机上**运行模型（TTS、听写、声音克隆、配音翻译；负责人设定 2026-10-18），也可以驱动用户自己的 VoiceStudio 电脑。手机优先：模型下载后即可离线工作，无需电脑；配对会增加重型引擎，按任务选择。仅配套构建是基础，不是产品。它们实现同一份共享契约 `mobile/contract/SPEC.md`：移动端功能要么两端都发布，要么都不发布。主题、API 和字符串是生成的，绝不手工复制：`bun scripts/export-mobile-tokens.ts`（来自 Electron 主题）、`uv run python scripts/export_openapi.py`（来自后端）以及 Electron 语言目录；`tests/test_mobile_contract.py` 会在出现偏差时失败。桌面端/Web UI 仍只属于 Electron。

<!-- GSD:project-start source:PROJECT.md -->
## 项目

**VoiceStudio**

VoiceStudio 是一个开源、完全本地的 ElevenLabs 替代品——一款桌面应用，支持声音克隆、声音设计、视频配音和实时听写，覆盖 646 种语言。它完全在用户机器上运行（CUDA/MPS/ROCm/CPU 自动检测），无需 API 密钥、无需账户、无云依赖。它处于活跃的 beta 阶段，用户群不断增长，用户以真实工作负载使用它（50 个视频的批处理、多引擎配置、边缘操作系统平台），并在 GitHub Issues 和 Discord 中反馈摩擦。当前版本位于根 `package.json`（唯一事实来源——见版本管理）；最新稳定标签在 [Releases 页面](https://github.com/debpalash/VoiceStudio/releases/latest)。在 `AUTO_VERSION_BUMP` 关闭时（当前负责人设置），`main` 在发布之间保持在已发布版本。

**核心价值：** **真正可用的首次运行。** 下载安装程序（或克隆仓库）的用户应能获得可用的声音克隆或配音输出，而不会撞墙——当确实出问题时，错误或文档应准确告诉他们该怎么做。

其他一切（新引擎、花哨功能）都从属于“这个东西能在各平台可靠安装和运行，且用户已依赖的引擎和流水线保持兼容。”

### 约束

- **现有引擎兼容性**：已安装引擎（IndexTTS、CosyVoice 等）的用户不得需要重新安装。触及引擎代码的修复必须与磁盘上的模型状态向后兼容。
- **跨平台一致性**：每个修复都必须在 macOS（Apple Silicon；Intel Mac 仅限于应用 UI 加远程后端，如 README.md 和 `docs/install/macos.md` 所述）、Windows（x64）和 Linux（AppImage + deb）上有效。不允许仅限平台的回归；跨平台缺陷排查（PR #51）是基线。
- **默认功能必须在每个平台上有效（严格规则，2026-05-20）：** 以默认模式发布的功能——开箱即用、无用户自定义、无选择加入开关——必须在 macOS、Windows 和 Linux 上行为一致。允许为操作系统 API / shell / 打包使用平台特定的*实现代码*，但用户可见的*默认行为*不能有分歧。仅限平台的功能（例如仅 macOS 的全局快捷键、仅 Windows 的路径选择器）必须置于明确的用户选择加入之后：设置开关、环境变量或 CLI 标志。当默认功能在某个平台上无效时，那是 P0 缺陷——要么在缺失的平台上修复它，要么将其移到选择加入之后。没有第三种选择。**此规则管的是行为，不是性能**（2026-07-30 澄清，委员会）：硬件加速预期因主机而异——CUDA、MPS、DirectML、Triton 可用性和 `torch.compile` 都按设计依赖主机，若将规则解读为禁止这些，就会禁止 GPU 支持本身。在无法工作处跳过的优化（缺少 Triton、wheel 不支持的架构、工具链无法链接的路径）不是一致性违规；用户在某个操作系统上能看到并使用、但在另一个操作系统上不能的功能才是。
- **向后兼容的项目数据**：现有 `omnivoice_data/`（用户声音、项目、设置）必须无需手动迁移即可继续工作。任何数据库 Schema 变更都通过 alembic 进行，并有经过测试的升级路径。
- **保留本地优先保证**：未经用户**明确同意**，任何内容都不离开机器，且在所有内容都被拒绝时应用必须保持完全可用。自动缺陷报告是选择加入的，仅提交到 GitHub Issues（预填 URL，从用户自己的浏览器提交）。产品分析（负责人批准 2026-07-16；美国项目迁移 2026-09-26）是选择加入的 PostHog 美国区，带有**首次运行同意提示**——两个等权重的“是/否”按钮，绝不默认开启，跳过即关闭；受同意门控，仅允许列表中的无内容元数据（`backend/core/analytics.py`）；每个构建——安装程序、Docker 和源码构建都一样（负责人反转 2026-07-20，#1193）——都携带仓库内的可发布只写令牌，并显示相同的同意询问，环境/内置令牌可覆盖它。无必需的云调用、账户或 API 密钥。已批准的调用（负责人设定 2026-10-02；完整列表见 `.github/CONTRIBUTING.md` → 质量门禁）：在用户启动的首次运行设置期间——从 GitHub `zackees/ffmpeg_bins` 下载固定版本的 ffmpeg（`backend/services/media_tools.py`）、针对受限网络的 huggingface.co/hf-mirror.com 可达性探测（`backend/services/endpoint_race.py`）以及 Hugging Face 模型下载；打开社区画廊（jsDelivr 清单，`backend/api/routers/community.py`）或启用声音预览画廊（GitHub Releases，`backend/services/gallery.py`）；打包的更新检查针对 GitHub Releases（`electron/src/main/updater.ts`）；用户点击更新时从 PyPI 更新 yt-dlp；用户选择的选择加入式 Windows ROCm 设置（负责人批准 2026-10-26，在 `scripts/windows-rocm-recipe.json` 中哈希固定）：AMD 的 `repo.radeon.com` SDK/PyTorch wheel 和 OpenNMT/CTranslate2 GitHub 发布归档；手机内容报告到 `appapi.voicestudio.sh/v1/content-reports`，仅在用户点击结果的报告表单上的发送时（`mobile/contract/SPEC.md`）；以及仅在用户开启账户功能后（首次运行设置或设置 → 账户，默认关闭），从 Electron 主进程进行 VoiceStudio Cloud 登录和账户调用（`electron/src/main/account/`），加上在云同步也开启后从后端进行设置同步（`backend/services/cloud_sync/`），关闭时全部立即停止。狭窄的负责人批准例外（2026-09-28）：GitHub Star 徽章在显示时可获取公共仓库的 star 数，并在可见时每 20 分钟刷新一次，无需凭据或 referrer。它不发送任何项目、录音或账户数据，失败时保留最后计数，且绝不阻止离线使用。
- **Beta 发布节奏（无 RC、无仪式——严格规则，2026-05-20）：** 当前发布线（目前为 0.5.x）**没有发布候选、没有 48 小时浸泡、没有正式发布仪式**。每个修复都持续进入 main；负责人在当前状态值得发布时从 main 打补丁标签。没有 `-rc` 标签。没有分阶段发布。唯一被批准的预发布是选择加入的 **Beta 渠道**（负责人批准 2026-10-22）：从 `main` 切出的 `vX.Y.Z-beta.N` 标签，作为 GitHub 预发布发布，永不成为 `latest`，拥有自己的 `electron-beta-*` 更新源和 `:beta` Docker 标签（见 `docs/RELEASING.md` → Beta 渠道）。它不增加浸泡、仪式或侧分支。不推迟到未来的次要或主要版本——每个未关闭的 Issue 和每个未关闭的社区 PR 都被吸收到当前发布线或被明确拒绝。用户跟随 `main` 获取预览；想要稳定的用户留在最新的已打标签发布上。
<!-- GSD:project-end -->

<!-- GSD:stack-start source:research/STACK.md -->
## 技术栈

曾经放在这里的 2026 年 5 月技术栈研究服务于五项能力，这些能力此后都已发布（HF 令牌设置面板、预填 URL 缺陷报告、受限网络的 uv 镜像回退、Supertonic-3 引擎、仓库内 Markdown 文档）。遵循代码本身的模式；该研究确立的持久*禁忌*：

- **缺陷报告或崩溃转储不使用第三方端点**（`sentry-tauri` 经评估后被拒绝）——缺陷报告保持选择加入，通过预填 GitHub Issue URL，从用户自己的浏览器提交。唯一被批准的第三方端点是选择加入的 PostHog 美国区产品分析和错误跟踪（负责人设定 2026-07-16；2026-09-26 迁移；净化后的渲染器错误于 2026-09-26 批准），它受首次运行提示后的同意门控，且仅发送允许列表中的无内容元数据。原始异常、DOM 和会话自动捕获保持禁用；手动捕获的渲染器异常必须在传输前替换消息并清除堆栈路径。其可发布只写项目令牌提交在仓库内（负责人反转 2026-07-20，#1193——源码构建获得与安装程序相同的受同意门控的分析；环境/内置令牌可覆盖），由 `tests/test_no_committed_analytics_token.py` 在恰好 `backend/core/analytics.py` + `electron/src/shared/utils/analytics.ts` 中允许。
- **不从应用进行基于 PAT/令牌的 GitHub 发布**——用户从自己的浏览器提交。
- **不要在 Windows 上推荐用 `setx` 设置环境变量**（静默截断，不传播到当前 shell）——使用应用内设置面板或 PowerShell `[Environment]::SetEnvironmentVariable`。
- **不要为任何未来的文档站点采用 Material for MkDocs**（自 2025 年 11 月起处于维护模式）——如果文档超出仓库范围，Astro Starlight 是先例。
- **`hf_transfer` 已弃用**——默认的 `huggingface_hub`（hf-xet）处理下载。

对于任何新内容：优先使用 `pyproject.toml` / `electron/package.json` 中已固定的内容，并在添加依赖前检查 `uv tree` 是否有冲突。
<!-- GSD:stack-end -->

<!-- GSD:conventions-start source:CONVENTIONS.md -->
## 约定

**版本管理（硬性规则，负责人设定 2026-06-11；单一来源 2026-09-26）：** **根 `package.json` 是维护中的 Electron 应用版本的唯一事实来源**——Electron 的构建器直接读取它，Vite 为 UI 和缺陷报告注入 `__APP_VERSION__`。两个工具链要求的**镜像**保持与其相等并同步递增：`pyproject.toml` 和 `backend/core/version.py` 的 `_FALLBACK_VERSION`。由 `tests/test_app_version.py` 守护。版本递增是手动的，仅在负责人要求时进行；在此之前，`main` 可以等于最新已发布版本。后果：
- 每个 PR 和预览构建都使用规范版本。预览构建产物添加工作流运行编号，而不改变已检入的版本。
- 发布 = 获得负责人对版本的批准，一起递增规范版本和维护中的镜像，然后在验证后从 `main` 为该确切版本打标签。
- Docker：`ghcr.io/debpalash/voicestudio:latest` = **main**（滚动预览）；`:X.Y.Z` + `:X.Y` + `:stable` = 已打标签的发布；`:beta` + `:X.Y.Z-beta.N` = beta 预发布（永不 `:stable`/`:X.Y`）。`:latest` 按设计是预览渠道——稳定用户固定 `:stable` 或版本标签。原 `ghcr.io/debpalash/omnivoice-studio` 坐标保留为兼容别名。
- 未经负责人要求，不要递增次要/主要版本或发明 RC/代号。beta 版本（`X.Y.Z-beta.N`）仅在负责人要求时在同样三处递增，并使用相同的同步。没有“推迟到下一版本”标签——范围被吸收或拒绝，绝不重新版本化。

**文档同步（硬性规则，负责人设定 2026-06-11）：** 任何改变这些文档所描述内容的变更——README.md、`.github/CONTRIBUTING.md`、`.github/SECURITY.md`、`.github/SUPPORT.md`、LICENSE 或 `docs/**`（安装流程、Docker 标签语义、平台支持、版本管理/发布行为、审查流程、支持的版本）——必须在**同一个 PR** 中更新这些文档。如果合并后才发现文档影响，文档修复是紧接着的下一个提交，而不是积压。过时文档被视为缺陷。

**发布说明 / 变更日志（硬性规则，负责人设定 2026-06-16）：** 每个已打标签的发布在打标签之前（或同一小时内）都要在 `CHANGELOG.md` 中获得**高质量的、面向用户的 `## [X.Y.Z] — DATE` 章节**——绝不是“Auto-generated release for vX.Y.Z…”回退内容。桌面端发布工作流逐字提取该章节作为 GitHub Release 正文，因此缺失/空章节会发布一个裸发布。质量门槛（负责人重新设计 2026-07-17，取代旧的粗体引导段落）：**安静且可扫读的变更条目**——在可选的已打标签发布引言之后（见下面的呈现规则），一个简短的 `**Highlights**` 项目符号列表（平实词语，每行一条），然后是 `### Changed` / `### Added` / `### Docs` / `### Fixed` / `### License` / `### CI` 子章节，其中每个条目是**单行一句话**，带有 `(#NNN)` Issue/PR 引用和贡献者致谢（`— thanks @user!`，如适用）。变更条目为用户而写，按主题分组，没有多行条目段落或原始提交转储。这也适用于**预览构建**：预览发布说明以相同风格总结自上次稳定版以来 `main` 上的新内容。工作流：功能合并时，添加一行片段 `changelog.d/<PR#>.<type>.md`（见 `changelog.d/README.md`），而不是编辑 `CHANGELOG.md`，这样 PR 永远不会在其上冲突；`scripts/release.py` 将片段折叠到 `## [Unreleased]` 并删除它们，已在 `[Unreleased]` 中的条目保持有效。发布时将其重命名为版本 + 日期。如果某个发布已用回退正文切出，下一个动作是回填 `CHANGELOG.md` **并** `gh release edit <tag>` 实时正文——而不是积压。

**发布呈现与致谢（负责人设定 2026-09-17）：** 已打标签的发布公告以最大的用户可见变更开头；重新设计需要真实的 UI 截图，迁移需要安装程序链接和步骤。从标签比较和包含的 PR 中核实所有贡献者致谢；分别列出作者和缺陷报告者（见 `docs/RELEASING.md`）。Highlights 保持 3–5 个项目符号；发布引言可以在简洁的变更条目之前包含散文、图片和下载表格。

**本地化（硬性规则）：** 代码库中任何地方都不得有硬编码的非英文（CJK）**用户可见文本**，翻译层除外：`electron/src/renderer/src/i18n/`（应用加载的目录；由 `tests/test_locale_parity.py` 和 Electron `locale:check` 检查）和旧版 `electron/src/shared/i18n/`（仅由共享模块测试加载）。所有 UI 字符串都通过 i18n（`locales/*.json` 中的 `t('...')` 键）；原生语言名称位于 `i18n/index.ts`（`LANGUAGES`）。功能性 CJK 是允许的，并通过 `tests/test_no_hardcoded_cjk.py` 中的允许列表跟踪——文本处理正则、模型/引擎词汇与标识符（例如 CosyVoice 说话人 ID）、本地化错误匹配、演示/评估数据和测试夹具。CI 在允许列表之外的任何硬编码 CJK 上失败；要添加合法的功能性 CJK，在该文件中扩展 `_ALLOWED_FILES` 并附上理由。

**发布部署渠道（硬性规则，负责人设定 2026-07-16）：** 版本递增只有在**每个**维护中的渠道都发布它之后才算“已发布”——完整清单在 `docs/RELEASING.md`。渠道包括 Electron GitHub Release（Linux x64、Windows x64 + 实验性 ARM64、macOS arm64 + x64）、GHCR 和 Docker Hub 的 CUDA 与 ROCm 版本，以及从 `deploy/dockerhub-overview.md` 同步的 Docker Hub 概览页。最终 Tauri 源是不可变的兼容性资产，复制到发布中供旧客户端使用；它们永不重建。打标签后验证所有渠道。预览/RC 构建始终来自 `main`；绝不切出侧分支构建。

**修复质量（硬性规则，负责人设定 2026-06-16）：** *正确地*修复问题并面向未来