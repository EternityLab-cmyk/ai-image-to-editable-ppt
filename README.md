# AI Image to Editable PPT

把截图、扫描幻灯片、PDF 或图片型 PPT，重建成可以逐项编辑的 PowerPoint。

**核心工作流：以参考图为依据，用 Codex 内置生图工具重建复杂视觉素材，再把它们作为独立图片对象放回 PPT；所有可读文字用原生文本框重建。**

Rebuild visual slide references into layered, editable PowerPoint files. Complex visuals are recreated with AI image generation; text and simple layout geometry remain native PowerPoint objects.

## 编辑能力

| 元素 | 重建方式 | 在 PowerPoint 中可做什么 |
| --- | --- | --- |
| 医学/科学插图、设备、图标、材质等复杂视觉 | AI 重建的独立透明图片 | 单独选择、移动、缩放、替换、删除 |
| 标题、标签、正文、图例 | 原生文本框 | 编辑文字、字体、字号、颜色 |
| 面板、分隔线、普通箭头等简单结构 | 原生形状 | 调整位置、尺寸、样式 |

图片对象内部的像素不会变成可编辑矢量路径。不可分割的语义组合可以作为一个图片对象，例如相互连接的解剖结构。

## 包含的技能

- [`rebuild-reference-editable-ppt`](skills/rebuild-reference-editable-ppt/SKILL.md)：强调参考图一致性、复杂插图逐项生图和独立分层，是推荐入口。
- [`image-to-editable-ppt`](skills/image-to-editable-ppt/SKILL.md)：配套的转换与验证流程，包含完整的 `editppt` Python CLI、页面清单、任务状态、素材处理和 PPTX 组装工具。

两个技能都需要安装。`imagegen` 和 `Presentations` 是运行环境提供的配套能力，本仓库不复制它们的实现，也不包含图像模型权重。

## 安装

需要 Python 3.10+、Git，以及能执行这些技能的代理环境。推荐在提供内置 `image_gen.imagegen`、图片查看和演示文稿渲染工具的 Codex 环境中运行。多页输入还需要页面子代理能力；只有单页时可以在主代理中完成。

```bash
git clone https://github.com/EternityLab-cmyk/ai-image-to-editable-ppt.git
cd ai-image-to-editable-ppt
```

将 `skills/` 下的两个目录复制到自己的技能目录；如果已有同名版本，请先备份。Codex 常用目录为 `~/.codex/skills`，设置了 `CODEX_HOME` 时使用其下的 `skills` 目录。

Windows PowerShell 示例（适用于尚未安装同名技能的默认目录）：

```powershell
$skillDestination = Join-Path $env:USERPROFILE '.codex/skills'
New-Item -ItemType Directory -Path $skillDestination -Force | Out-Null
Copy-Item -LiteralPath './skills/image-to-editable-ppt' -Destination $skillDestination -Recurse
Copy-Item -LiteralPath './skills/rebuild-reference-editable-ppt' -Destination $skillDestination -Recurse
```

macOS / Linux 示例：

```bash
mkdir -p ~/.codex/skills
cp -R skills/image-to-editable-ppt skills/rebuild-reference-editable-ppt ~/.codex/skills/
```

安装配套 CLI（在仓库根目录执行）：

```bash
pipx install --editable ./skills/image-to-editable-ppt/cli
editppt --help
editppt setup
editppt doctor
```

如果没有 `pipx`，可以在 Python 虚拟环境中执行：

```bash
python -m pip install -e ./skills/image-to-editable-ppt/cli
```

之后开启能发现已安装技能的新对话。运行时还需具备 `imagegen` 和 `Presentations` 能力，后者用于检查最终 PPTX 的实际渲染。CLI 产生的页面预览不能代替最终 PowerPoint 渲染检查。

## 使用

上传参考图、PDF 或图片型 PPT，并发送：

> 按 $rebuild-reference-editable-ppt 把这张图重建成可编辑 PPT。复杂插图逐项使用内置生图模型重建，再按原图位置放回；所有文字可编辑，各个图像对象可以独立替换，不要简化医学插图。

English example:

> Use $rebuild-reference-editable-ppt to rebuild this screenshot as an editable PPTX. Recreate each complex visual with the built-in image generator, place the assets back at their reference positions, and keep all text native and editable.

## 工作流

1. 检查参考图，记录文字、位置、颜色、层级，以及需要独立替换的视觉对象。
2. 用 `editppt prepare` 创建有序页面任务；内置生图可用时选用 `--image-backend builtin-imagegen`。
3. 按参考图生图，重建无文字的素材或稀疏素材表，保留形状、视角、比例与细节。
4. 导入生图结果，去除纯色分离背景，拆成独立透明图片，并记录来源。
5. 用页面清单定位图片、原生文本框和简单形状，构建每页 PPTX。
6. 对照原图检查预览，修复缺失对象、边缘残色、重叠和文字问题，再验证页面。
7. 组装最终 PPTX，重新渲染每张幻灯片并检查。

具体命令、清单字段和页面分工以技能文件及其引用文档为准；CLI 负责可重复的转换和组装，代理负责理解参考图、生成素材与视觉判断。

## 图像后端与 OCR

- **首选内置生图工具**：由代理直接调用，CLI 只负责导入、素材分离与后续组装。`editppt doctor` 无法检测代理是否提供内置生图。
- **CLI 备用后端**：代码包含 Codex OAuth 适配器和用户配置的 OpenAI 兼容图片 API 适配器。OAuth 适配器依赖服务端实现，其可用性随环境变化；存在本地登录文件并不保证后端请求成功。API 模式需要用户自行配置可用模型和凭据。
- **可选 PaddleOCR**：配置后可获取文字识别和尺寸提示；不配置时使用本地文字几何检测，由代理逐项核对文字。服务费用和额度以提供方当前规则为准。
- 生成与 OCR 会将任务所需参考图片发送到对应服务；如需仅本地处理，请在运行前明确提出。缺少允许使用的生图后端时，这个流程无法完成复杂视觉重建。

本仓库不包含 API 密钥、OAuth 登录文件、OCR Token、私人参考图或对话记录。凭据在使用者自己的环境中配置，不应提交到仓库。

## 质量边界

AI 重建可能产生视觉偏差；“按原图重建”是验收目标，不是像素级一致性的保证。医学、科学或其他专业图像需要检查关键结构。不同电脑的字体和 PowerPoint 渲染也可能不同，最终 PPTX 应在实际使用环境中复查。

验收清单见 [`acceptance-checklist.md`](skills/rebuild-reference-editable-ppt/references/acceptance-checklist.md)。本仓库提供工作流与运行工具，不是无需代理判断即可自动完成的单命令转换器。

## License

MIT，见 [LICENSE](LICENSE)。第三方运行依赖、在线服务和使用者输入素材适用各自的许可或条款。本项目不由 OpenAI、Microsoft 或百度官方维护。
