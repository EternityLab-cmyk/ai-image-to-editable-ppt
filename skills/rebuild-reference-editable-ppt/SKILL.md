---
name: rebuild-reference-editable-ppt
description: Create layered editable PowerPoint from visual references or the user's own original images. Supports supplying original pictures to AI slide generation, rebuilding the generated layout as editable PPT, and replacing corresponding picture objects with the original files. Also use for source-faithful screenshot/PDF reconstruction, complex illustrations as independent image objects, and native editable text and layout. Triggers include “自带原图生图再转PPT”, “生图后换回原始图片”, “按原图分层重建”, and “逐项生成素材再定位叠加”.
---

# Reference-faithful layered editable PPT

Rebuild supplied visual references as editable slides without replacing their visual identity with simplified vector approximations. Treat AI-generated raster assets as legitimate editable PowerPoint image objects: each independent semantic visual can be moved, resized, replaced, or deleted, while text and structural layout remain native.

## Select the entry workflow

- Existing slide references: follow the reconstruction workflow below.
- Original pictures plus a request to design slides: first read `references/own-images-workflow.md` completely. Supply the user's pictures as actual inputs to image generation, rebuild the resulting full-slide designs through this skill, then replace mapped picture objects with the preserved original files. Treat original-image replacement as part of completion when requested; do not deliver a generated-only PPT and leave the replacement to the user.

The object-source contract below governs the reconstruction checkpoint. Explicitly requested original-file replacement is a subsequent stage covered by `references/own-images-workflow.md`; it does not authorize source-slide crops as substitutes for the original files.

## Required skill stack

Use the following skills in this order:

1. Read and follow `image-to-editable-ppt` completely as the governing conversion workflow and deterministic `editppt` runtime.
2. Read and follow `imagegen` for all raster generation, reference-based editing, chroma-key separation, and output validation.
3. Use `Presentations` for final-deck rendering, visual inspection, narrowly targeted native-object corrections, and mapped original-picture replacement after reconstruction when requested.

If the generic presentation workflow conflicts with `image-to-editable-ppt`, let the specialized reconstruction workflow own page manifests, page builds, worker dispatch, recording, and final assembly. Do not replace a prepared reconstruction run with a generic slide template.

If `image-to-editable-ppt` is unavailable, install it before proceeding or report the missing dependency. Do not silently substitute a different reconstruction method.

Select the image backend by environment:

- In Codex with a callable built-in `imagegen` tool, use the `builtin-imagegen` run contract and follow its required local-reference inspection policy.
- In Claude Code or another agent without Codex's built-in `imagegen`, use the `editppt-image-cli` run contract and `editppt image generate/edit`. Require either working Codex OAuth or a user-configured OpenAI-compatible image API backend.
- If neither backend is available, stop and tell the user exactly which image-generation dependency is missing. Never convert the missing backend into permission to simplify the visuals.

Before final delivery, read `references/acceptance-checklist.md` completely and apply it.

## Non-negotiable object-source contract

Classify every visible item before authoring:

- Use AI-generated or AI-edited raster assets for medical illustrations, scientific illustrations, anatomical structures, instruments, devices, material samples, icons, pictograms, shields, bottles, monitors, tissue, organs, decorative semantic marks, and other visual-identity-bearing objects.
- Use native PowerPoint text boxes for all readable titles, subtitles, labels, captions, bullet points, legends, and ordinary prose.
- Use native PowerPoint structure only for panels, rings, sectors, borders, dividers, basic connectors, ordinary arrows, simple circles, and other layout geometry.
- Use one independent image object for each visual that the user would reasonably want to move or replace independently.
- Keep an inseparable semantic cluster together when splitting it would destroy meaning or alignment. For example, a pancreas + duodenum + anastomosis + sutures may be one medical illustration asset, while a separate drain bottle remains another asset.

Never use any of the following as a shortcut:

- simplified native-vector approximations of semantic visuals;
- emoji, dingbats, stock symbols, or “similar enough” icons;
- direct crops from the source image as final slide assets;
- a full-page or full-panel screenshot hidden behind editable text;
- generated images that contain the slide's editable text;
- invisible, transparent, off-canvas, or tiny text added only to satisfy validation.

The reference image or a reference crop may be supplied to image generation as an edit/reference input. It may not be embedded directly as the final separated object.

## Workflow

### 1. Preserve and inspect inputs

- Preserve all source files and prior PPT versions.
- Export a new PPTX instead of overwriting the reference or an earlier deliverable.
- Inspect every source image at full resolution.
- Record slide order, canvas size, all readable text, all foreground visuals, structural geometry, colors, and grouping relationships.
- Define the intended segmentation before generating any asset.

### 2. Prepare the deterministic run

- Confirm `editppt --help`; install the skill-local CLI if necessary.
- Run `editppt setup` and `editppt doctor` when the runtime has not been initialized.
- Prepare all input images in one ordered run. Use `--image-backend builtin-imagegen` in Codex when the tool is callable; otherwise use `--image-backend editppt-image-cli` in Claude Code or another non-Codex environment.
- For multiple images or pages, use the required page-worker dispatch workflow; never let the parent author multi-page page artifacts directly.
- If no PaddleOCR token is configured, offer the free token once after preparation and wait for the user's choice. Regenerate hints after configuration. If the user declines, continue offline and manually verify every text string.

### 3. Inventory before generation

For each page, create a complete inventory containing:

- every readable text line and its role;
- every independent semantic visual or inseparable visual cluster;
- every native structural element;
- background strategy;
- expected asset count and asset-to-slide mapping.

Do not begin slide assembly until the inventory accounts for every visible object.

### 4. Recreate source-faithful assets

- Use built-in `imagegen` first when the selected run contract is `builtin-imagegen`; otherwise use the selected `editppt-image-cli` backend.
- View every local reference image before an edit call.
- Use reference-based image editing rather than free-form invention whenever the source shows the target object.
- Require preservation of the source object's shape, viewpoint, stroke, palette, texture, proportions, internal details, and visual identity.
- Prohibit readable text, pseudo-text, watermarks, extra objects, beautification, style changes, synonym-symbol substitution, and simplification.
- Generate sparse asset sheets on a flat chroma-key color absent from all subjects; leave generous padding between objects.
- Submit page-local image jobs serially.
- Import every selected result through `editppt image import`, process sheets through `editppt image process-sheet`, and record exact provenance.
- Reconcile the split output against the inventory. Regenerate missing, fused, distorted, color-damaged, or incorrect assets.

If the key color damages an object's edges or internal colors, regenerate the asset sheet with a different key color. Do not accept color loss as a minor warning.

### 5. Build editable text and structure

- Use `text_hints.json` and the source image together; hints assist but never replace manual reading.
- Transcribe every visible text item exactly.
- Use native text boxes with measured positions and font sizes; keep same-level typography consistent.
- Rebuild only layout geometry with native shapes.
- Preserve z-order: background, structural geometry, separated images, editable text, then intentional overlays.
- Prevent any duplicate text or visual from remaining in both an image layer and a native layer.
- Build only through the page manifest and `editppt page build`.

### 6. Validate each page

- Render the page and compare it against the source at full size.
- Generate the source-versus-preview contact sheet.
- Fix missing objects, wrong objects, incorrect asset grouping, color fringes, visual drift, overlaps, clipping, and text crowding before validation.
- Run `editppt page validate` and require top-level `passed: true`.
- Require every image path to have provenance and every positioned object to have source-pixel coordinates.
- Record the page only after the worker returns all required artifacts.

On Windows, if validation or recording fails only because GBK cannot print characters such as `•` or `®`, rerun the same command with UTF-8 process output, for example:

```powershell
$env:PYTHONUTF8='1'; $env:PYTHONIOENCODING='utf-8'; editppt page validate <page-dir>
```

Do not change slide content to work around a console-encoding error.

### 7. Finalize and visually inspect the deck

- Finalize only after every page is recorded.
- Require the final structural validation to pass with the expected slide count and no contract violations.
- Render every final slide individually, not only a montage.
- Inspect the actual final PPTX render for gaps that deterministic validation may miss, especially touching labels, overlaps, font substitution, clipped text, and images shifted during assembly.
- If a focused post-finalization native-object correction is necessary, preserve the assembled deck, edit only the identified object with the presentation tooling, export a new final copy, render it again, and rerun structural validation.

## User communication

- State at the start that complex visuals will be recreated as independent AI raster assets and that text will remain native and editable.
- Report progress by concrete milestones: inventory complete, asset sheets generated, assets separated, page built, visual QA complete, page validation passed, final deck validated.
- Never claim completion from structure validation alone.
- Report any object whose source-faithful separation is genuinely blocked; do not hide the problem behind an approximation.
- When the user requested the original-picture workflow, completion requires the mapped original files to be embedded in the delivered PPTX and the replaced final file to be inspected. Apply `references/own-images-workflow.md` rather than treating editable picture objects alone as completion.
- Deliver only the final PPTX unless the user requests intermediate assets or previews.

## Invocation examples

- “Use `$rebuild-reference-editable-ppt` to turn these two figures into one two-page PPT. Do not simplify any medical illustrations.”
- “按 `$rebuild-reference-editable-ppt` 把截图逐项生成素材并定位叠加，所有文字都要可编辑。”
- “Use `$rebuild-reference-editable-ppt`; each device, icon, tissue illustration, and material sample must be its own editable image object.”
- “先把我上传的产品图和实验图作为参考图片输入生图工具，设计整页；再转成可编辑 PPT，最后把对应图片换回我提供的原始文件。”
