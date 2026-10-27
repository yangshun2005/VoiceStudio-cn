# VoiceStudio — 许可声明

## 缩写

AGPL-3.0-only

## 声明

版权所有 2024 至今 Palash Debnath 及 VoiceStudio 贡献者。

VoiceStudio 是**自由开源软件，依据 GNU Affero 通用公共许可证第 3 版（AGPL-3.0）授权**。你可以自由使用、复制、修改和再分发。这**包括对应用程序本身的商业及内部业务使用**。模型权重、分词器及其他第三方资产仍受其自身条款约束；本应用许可证不授予也不概括那些单独条款下的权利。

由于这是 **Affero** GPL，还有一项额外义务：如果你修改了 VoiceStudio 并通过网络向他人提供该修改版本，你必须同样以 AGPL-3.0 条款向这些用户提供你修改版本的完整对应源代码。完整文本见 [`LICENSE`](LICENSE)。

AGPL-3.0 下的商业使用是免费的。收费的是**商业许可证**——面向希望将 VoiceStudio 嵌入闭源或专有产品或服务、且不愿承担 AGPL-3.0 著佐权义务的组织——以及 VoiceStudio Pro 功能。套餐见 <https://voicestudio.sh/pro>；其他咨询请联系 `hi@voicestudio.sh`。

贡献者依据[贡献者许可协议](.github/CLA-1.0.md)将其贡献授权给 Yupcha Softwares Private Limited（维护 VoiceStudio 的公司）。贡献在公共仓库期间，仍以 AGPL-3.0 或其他 OSI 认可的许可证在该处提供（CLA 第 4 条）。

（本声明为通俗语言摘要；具有约束力的条款是 [`LICENSE`](LICENSE) 中完整的 GNU AGPL-3.0 文本。）

### 范围

这些条款涵盖 VoiceStudio 应用——Electron 桌面及 Web 应用（`electron/`）、其原生桌面辅助程序（`native/`）、FastAPI 后端（`backend/`），以及配套的构建/打包文件（`scripts/`、`deploy/`、`.github/`）。改编自 T3 Code 的 Electron 组件保留其 MIT 声明于 `electron/T3CODE-LICENSE.txt`。

捆绑的 `omnivoice/` Python 包——Han Zhu 的基础 TTS 模型——由其上游作者**单独以 Apache License 2.0 授权**，此处未重新授权。Apache License 2.0 与 GNU AGPL-3.0 兼容，可与之结合。见 `pyproject.toml`。

下载的模型权重不由 VoiceStudio 重新授权。默认的 `k2-fsa/OmniVoice` 模型卡标明其代码为 Apache-2.0，预训练权重为 CC-BY-NC。其 `audio_tokenizer/LICENSE` 包含单独的 Boson Higgs Audio 2 和 Meta Llama 社区条款。VoiceStudio 自有代码的商业许可证不替代上述任何条款。

维护中的"关于"面板显示选定的模型致谢及必需的 Higgs Audio 和 Llama 字面署名文本。证据及遗留缺口见[模型致谢来源](docs/model-credits.md)。这些可见致谢不构成完整的声明合规或对特定使用的许可。

[模型许可证记录](backend/config/model_licenses.json)区分已审查的非商业条款与未审查的上游元数据。商业使用标记为 false 包含未解决的审查；这并不表示所列每个模型都禁止商业使用。初始清单未主张任何商业许可。

模型管理器的版本化声明区分商业模型使用、商业输出使用、再分发及声音/录音同意。未知评估不等于许可，也不等于认定所有使用均被禁止。某一修订或转换的证据不自动覆盖另一修订或转换。

可选的[已审查文件工作流](docs/licensing/model-review-workflow.md)记录对所示声明及确切证据的本地确认。它不接受与提供方的协议、不授予提供方访问权、不授予商业权利、不验证声音同意，也不添加豁免。它不改变应用 AGPL 许可证授予的权利。现有引擎设置及常规下载/首次使用路径保持独立不变；目前没有生产模型具备此已审查工作流所需的完整证据。

第三方依赖保留其自身许可证。已解析集合见 `bun.lock`、`uv.lock` 和 `native/desktop-bridge/Cargo.lock`。

锁定的 PyAV 15.1.0 wheel 捆绑了 FFmpeg 和 x264/x265 库。PyAV 的源码许可证本身不描述这些二进制文件的条款。[wheel 审计](docs/licensing/pyav-15.1.0-audit.md)记录了其哈希、构建标志、上游许可证标签补丁及未解决的再分发要求。

### 参考

GNU Affero 通用公共许可证第 3 版的完整规范文本逐字复制于 [`LICENSE`](LICENSE)。权威副本位于 <https://www.gnu.org/licenses/agpl-3.0.txt>。

> **为何本声明单独成文件：** `LICENSE` 必须仅包含逐字的 AGPL-3.0 文本，不得有其他内容，以便 GitHub 的许可证检测（以及决定采用与否的企业许可证扫描器）将其识别为 `AGPL-3.0-only`，而非回退为 "Other" / `NOASSERTION`。