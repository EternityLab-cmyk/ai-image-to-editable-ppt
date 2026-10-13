# AI Image to Editable PPT

完整工作流：**放入自己的原始图片 → AI 生图设计整页 → 重建成可编辑 PPT → 把对应图片替换回原始素材**。

也支持把现有截图、扫描幻灯片、PDF 或图片型 PPT，重建成可以逐项编辑的 PowerPoint。

**从生图时就使用自己的素材**：先把自己的照片、实验图、产品图、插图或 Logo 作为参考图片传入 Codex 内置生图工具，让 AI 设计整体页面。随后把生成的整页重建为文字、结构和图片分层的可编辑 PPT，最后将指定对象换回用户提供的原始图片，保留原始素材的细节。

**原图替换是完整流程的收尾步骤**：AI 生成页面中的图片可能发生变化，制作 PPT 时用一开始提供的原始文件替换对应对象。成品中这些图片仍可独立移动、缩放和再次替换，文字与版式继续编辑。

Bring your own original images into AI slide generation, rebuild the generated layout as an editable PowerPoint, then replace the corresponding AI-rendered pictures with the original assets. Also supports reconstruction of existing slide references. Text and simple layout geometry remain native PowerPoint objects.

## 编辑能力

| 元素 | 重建方式 | 在 PowerPoint 中可做什么 |
| --- | --- | --- |
| 医学/科学插图、设备、图标、材质等复杂视觉 | AI 重建的独立透明图片 | 单独选择、移动、缩放、替换、删除 |
| 标题、标签、正文、图例 | 原生文本框 | 编辑文字、字体、字号、颜色 |
| 面板、分隔线、普通箭头等简单结构 | 原生形状 | 调整位置、尺寸、样式 |

图片对象内部的像素不会变成可编辑矢量路径。原始照片或实验图内部已有的文字也仍属于原图；幻灯片层面的标题、正文和标签则用可编辑文本框重建。不可分割的语义组合可以作为一个图片对象，例如相互连接的解剖结构。

## 自带原图：从生图到 PPT 的完整流程

1. **提供原始素材**：上传需要出现在幻灯片中的图片和文字内容，并说明哪张图用于哪个位置或用途。保留这些原始文件。
2. **将原图作为生图参考输入**：生图工具同时接收这些图片和页面设计要求，生成整体视觉稿。原图在这一步就参与设计。
3. **把整页视觉稿重建为可编辑 PPT**：拆分视觉对象，重建原生文本框和简单布局结构，并记录每个原始素材对应的图片对象。
4. **换回原始图片**：在 PPT 组装完成后，由代理将对应的生图图片对象替换为一开始提供的原始文件，保留现有位置、层级和版式，检查比例和留白。
5. **检查最终文件**：重新渲染替换后的 PPT，核对原始素材、可编辑文字和布局，再交付最终 `.pptx`。

这样既能使用 AI 的整页设计，又能让最终 PPT 中指定的照片、实验图、产品图或 Logo 使用原始素材，而不依赖生图模型重现它们的每个细节。没有原始素材对应的装饰性视觉，可以继续使用 AI 重建的独立图片。

该流程由代理协调生图、`editppt` 重建和演示文稿工具的原图替换阶段；详细执行规则见 [自带原图工作流](skills/rebuild-reference-editable-ppt/references/own-images-workflow.md)。

## 换成自己的图片或原始素材

重建完成后，每个分离的视觉素材都是独立的 PowerPoint 图片对象。你可以自己把其中某张图换成自己的图片，也可以换回已有的原始高清素材；例如把示意设备换成自己的产品照片、把通用插图换成自己的实验图。

1. 打开生成的 `.pptx`，选中要替换的图片。
2. 使用 PowerPoint 的“更改图片 / Change Picture”功能，选择自己的图片文件。
3. 检查新图片的比例、裁剪和透明背景，必要时微调尺寸和位置；不同 PowerPoint 版本的菜单位置可能不同。

已有文字和其他独立对象可以继续编辑，无需重新重建整张幻灯片。如果手头只有整张原始截图，而没有独立高清素材，需要先获得对应的独立图片，才能精确换回该对象。

也可以在生成完成后，把 PPT 和自己的替换素材一起交给代理，明确指定“第几页、哪个对象、换成哪张图”，让代理修改已有图片对象并检查布局。例如：

> 第 2 页右侧的设备图片换成我上传的 product.png，保留已有标题和版式，检查替换后的比例和位置。

## 包含的技能

- [`rebuild-reference-editable-ppt`](skills/rebuild-reference-editable-ppt/SKILL.md)：推荐入口，支持自带原图参与生图设计、分层重建和最终原图替换，也支持已有参考图的忠实重建。
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

**从自己的素材开始：**上传原始图片及页面内容，并发送：

> 按 $rebuild-reference-editable-ppt 完成完整工作流：先把我上传的 product.png 和 experiment.png 作为参考图片输入内置生图工具，设计一页幻灯片；再将生成页面重建为可编辑 PPT；最后将对应图片对象替换回这两个原始文件，保留版式，文字可编辑，并检查最终 PPT。

**重建已有页面：**上传参考图、PDF 或图片型 PPT，并发送：

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
