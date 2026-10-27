## 活动桌面端：仅 Electron

Electron（`electron/`）是唯一的桌面端和 Web UI。Tauri 外壳和旧版 UI 入口已移除；不要恢复它们的构建、运行时、发布或 CI 路径。最终 Tauri 安装的反馈请引导至迁移指南，并保留其不可变的更新源。`electron/src/shared/` 下部分模块仍由 Electron 临时共享；它们不是可运行的应用。新 UI、IPC、安装说明、测试和浏览器资源归入 Electron；在 macOS、Windows 和 Linux 上验证 Electron。

## 移动端：原生 iOS 和 Android（负责人设定 2026-10-17）

每个发布版本都从同一发布标签和版本构建桌面端、iOS、Android 和 Docker（负责人设定 2026-10-26）。手机下载应放在同一个 GitHub Release 中；缺少渠道即为发布缺陷。

`mobile/ios/`（Swift/SwiftUI）和 `mobile/android/`（Kotlin/Compose）是原生应用，**在手机上**运行模型（TTS、听写、声音克隆、配音翻译；负责人设定 2026-10-18），也可驱动用户自己的 VoiceStudio 电脑。手机优先：模型下载后即可离线使用，无需电脑；配对后按任务选用重型引擎。仅配套构建是基础，不是产品。两者实现同一共享契约 `mobile/contract/SPEC.md`：移动端功能要么两端都发布，要么都不发布。主题、API 和字符串均为生成，绝不手工复制：`bun scripts/export-mobile-tokens.ts`（来自 Electron 主题）、`uv run python scripts/export_openapi.py`（来自后端）以及 Electron 语言目录；`tests/test_mobile_contract.py` 在出现偏差时失败。桌面端/Web UI 仍仅归 Electron。

# Agent 规则 — VoiceStudio

对所有 AI Agent（Claude、Codex、Cursor、审查机器人等）具有约束力。CLAUDE.md 是完整章程；本文是操作契约。冲突时以 CLAUDE.md 为准。

## Token 经济（负责人指令，2026-07-20；2026-07-28 收紧）
- **默认给出能完整回答的最短回复。** 用大纲和表格代替散文；不要开场白，不要复述刚做的事，不要重新解释 diff 已展示的修复。适用于所有回复，不仅是状态更新。
- 先给结果。不要叙述，不要重述 diff，不要空洞赞美，不要列出你反正要执行的计划。
- 状态更新：一行。最终报告：只写会改变读者下一步行动的内容。
- 不要重新推导 CI、linter 或审查机器人已计算的内容——先读它们的输出（`gh pr checks`，通过 `gh api .../pulls/N/comments` 读机器人评论）。
- 机械规则放在确定性测试中，绝不靠 Agent 努力：变更日志风格（`tests/test_changelog_style.py`）、语言目录一致性（`tests/test_locale_parity.py`）、版本同步（`tests/test_app_version.py`）、CJK（`tests/test_no_hardcoded_cjk.py`）。
- 迭代时运行针对性测试；仅在落地前运行完整套件。
- 测试和 CI 如实模拟 CI：`HF_HUB_OFFLINE=1` + 空 `HF_HUB_CACHE`——有数据的开发缓存会掩盖真实失败。

## 跨平台一致性：行为，而非性能
- 一致性规则覆盖用户可见的**行为**。硬件加速因主机而异是设计使然（CUDA/MPS/DirectML、Triton 可用性、`torch.compile`）；在物理上无法工作时跳过优化不构成一致性违规。
- 不要通过在所有平台禁用可用的优化来"修复"一致性问题。那是用真实回归换取语义上的合规。
- 用户在一个操作系统上能看到并使用、在另一个上不能的功能**就是**违规。以用户能做什么来判断，而非运行速度。

## 合并协议（硬性规则）
1. 未经审查绝不合并。先收集 CodeRabbit + Greptile 评论；有未读的 Critical/P1 绝不合并。
2. 绝不原样接受 PR：在合并前于 PR 分支上修复发现的问题（维护者提交可以；在变更日志片段中致谢贡献者）。不允许先合并后修复，不允许评论后走人。
3. 判断过期分支的 CI 前，先将当前 `main` 合并进去——旧工作流下的 PR 绿灯 ≠ main 绿灯。
4. 门禁："Tests (backend + frontend)" 绿灯 + MERGEABLE。
5. 每次合并后：观察 `main` 自身的合并后运行直至绿灯（`gh run list --branch main`）。main 红灯 = 放下一切去修复。
6. 每个 PR、修复和功能都从新获取的 `origin/main` 开始（负责人规则，2026-10-26）：`git fetch origin`，确认本地 `main` 无传入或传出提交（若有先解决），从 `origin/main` 建分支，推送前再次合并最新 `main`。`main` 要求分支在合并前保持最新；该设置保持开启。

## 变更规则（全文见 CLAUDE.md）
- 根治一类问题，而非单个实例；先失败后通过的回归测试；最小正确变更。
- macOS/Windows/Linux 默认行为一致；仅平台功能需显式选择加入。默认行为分歧 = P0。
- 本地优先：不新增必需的网络调用；任何 HF 下载以已安装状态或用户显式操作为前提；所有合成音频经由 `mark_synthetic` 检查点。获准的调用列于 `.github/CONTRIBUTING.md` → Quality gates：用户启动的首次运行设置（来自 GitHub `zackees/ffmpeg_bins` 的 ffmpeg、huggingface.co/hf-mirror.com 可达性探测、HF 模型下载）、打开/启用画廊（jsDelivr 清单、GitHub Releases）、打包更新检查、用户点击的 PyPI yt-dlp 更新、用户选择的 Windows ROCm 设置（负责人批准 2026-10-26，哈希固定：`repo.radeon.com` wheel 及 OpenNMT/CTranslate2 GitHub 发布）、经同意的分析、预填 URL 的缺陷报告、从结果 Report 表单发送的手机内容报告、输入密钥后的 Pro 许可证检查，以及 VoiceStudio Cloud 登录/账户调用（云同步开启后的设置同步）仅在用户开启后（默认关闭；关闭即立即停止）。负责人批准的例外（2026-09-28）：公开 GitHub star 数可每 20 分钟自动刷新，不带凭据/referrer 或用户内容；离线使用不受影响。
- 每个用户可见字符串经由 i18n，存在于全部 21 个 `electron/src/renderer/src/i18n/locales/*.json` 文件（应用加载的目录）中并有真实翻译；`tests/test_locale_parity.py` 和 Electron `locale:check` 强制执行。旧版 `electron/src/shared/i18n/` 目录仅由共享模块测试加载。
- 同一 PR 内同步文档。变更日志：每个 PR 在 `changelog.d/<PR#>.<type>.md` 中写一行低调说明（不是 `CHANGELOG.md`），结尾为 `(#N)` + 社区工作加 `— thanks @user!`；发布脚本将片段折叠进 Unreleased。
- 带标签的发布公告以最大的用户可见变更为首；重新设计需真实 UI 截图，迁移需安装链接和步骤。从标签比较和所含 PR 中核实所有贡献者致谢；分别列出作者和缺陷报告者（见 `docs/RELEASING.md`）。
- 版本管理：根 `package.json` 是唯一事实来源；未经负责人要求绝不升版本。
- JavaScript 依赖变更需重新生成根 `bun.lock`（Docker 运行 `--frozen-lockfile`）。
- 归属：提交、PR 描述和评论只携带提交者的 git 身份。绝不为 Agent 署名（无 Agent `Co-authored-by:`、"Generated with …"、会话链接），绝不添加其他姓名/邮箱。人类共同作者可以。由 `commit-identity` 检查强制执行。
- Issue：吸收或拒绝——绝不推迟到未来版本。实现社区报告的修复前先检查开放 PR 队列。

## 共享选择控件

- 所有新增或重新设计的选择框使用 `electron/src/shared/components/SearchableSelect.jsx`。声音选择复用 `VoiceSelector`。不要引入原生 `<select>` 控件。
- 提供本地化的 `ariaLabel`；在滚动或裁剪容器内使用 `menuPortal`。保留键盘选择和禁用状态。

## Agent 技能

项目开发技能固定在 `skills-lock.json` 中并安装于 `.agents/skills/`：Vite 和 FastAPI。
仓库规则和跟踪器映射覆盖通用技能指导。

### Issue 跟踪器

`debpalash/VoiceStudio` 上的 GitHub Issues，通过 `gh` CLI。见 `docs/agents/issue-tracker.md`。

### 分诊标签

五个规范角色，每个标签字符串等于其名称。见 `docs/agents/triage-labels.md`。

### 领域文档

单上下文：`docs/adr/` 加一个根 `CONTEXT.md`，后者惰性创建且尚不存在——缺失时跳过。见 `docs/agents/domain.md`。