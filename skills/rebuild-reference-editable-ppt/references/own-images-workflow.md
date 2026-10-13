# Own images → AI slide design → editable PPT → original-image replacement

Use this mode when the user supplies original images and wants them to participate in slide generation, then appear as original files in the final PowerPoint. This is an agent-coordinated workflow with a reconstruction checkpoint and a subsequent original-picture replacement stage; it is not a single `editppt` command.

## 1. Preserve original inputs and map their intended uses

- Keep every original image unchanged. Record a stable asset ID, original path and SHA-256, intended slide, and its purpose or position. Do not infer originals from a generated page or use source-slide crops when the user has supplied actual original files.
- Distinguish the user's original pictures from design references. A style-reference image is not automatically a picture to embed in the final deck.
- Preserve provided slide text. Ask only for missing content or an ambiguous asset-to-slide assignment that prevents correct placement.
- Maintain an original-assets mapping outside the CLI's state files. Later add the exact slide and picture-object identifier for each requested replacement. Do not alter the `editppt` run-state contract to store this mapping.

## 2. Supply the original pictures to image generation

- Follow the imagegen skill and inspect every local image before an edit/reference call.
- Include the original picture files as actual image inputs, using `referenced_image_paths` when all are local, or the smallest applicable `num_last_images_to_include` otherwise. Do not describe an image in text while omitting its actual reference input.
- Request a complete slide design using the supplied pictures, slide text, and layout requirements. Explicitly reserve a distinct picture region for each original that will be restored later; avoid merging the picture into decorative geometry or synthesizing a new inseparable scene around it.
- The generated slide is a layout reference. It may visually alter an original picture, so it is not the final fidelity source for those mapped assets. Preserve the originals separately.
- Inspect the generated page for the intended content, asset assignment, readability and adequate space. Revise unusable designs before reconstruction.

## 3. Rebuild the generated page as an editable checkpoint

- Feed the generated full-slide page into the ordinary `image-to-editable-ppt` / `rebuild-reference-editable-ppt` workflow.
- Preserve the normal image-backend, page-worker, manifest, validation and assembly rules. Rebuild slide titles, prose and labels as native text; generate separated visual assets and preserve picture-object boundaries.
- Resolve each original-asset mapping to the exact reconstructed slide and picture object. Keep enough space for the original image's aspect ratio and intrinsic labels.
- Complete page validation and finalization. Preserve this generated-assets checkpoint PPTX and its validation reports before original-picture replacement.

## 4. Replace mapped picture objects with original files

- Use presentation tooling to edit the assembled PPTX and export a new final copy. Replace the existing mapped picture objects with the user's preserved original image files; keep the target position, z-order, and grouping where possible.
- Preserve original image detail, colors and aspect ratio. Prefer proportional fitting with appropriate whitespace rather than stretching or discarding important content. Use the original image bytes directly when supported by the tooling.
- Keep slide-level text and unrelated objects editable. Text already printed inside an original photo, chart or scientific figure remains part of that raster asset; avoid duplicate native text over it. Do not claim such intrinsic image text is editable.
- Keep AI-generated decorative assets that have no original counterpart. Replacement applies only to the mapped pictures requested by the user.
- Do not pass originals through `editppt image import` with a fabricated image-generation backend. Its provenance contract describes generated images; an original file remains a user-supplied asset in the separate replacement mapping.
- If an intended original file is missing or the picture correspondence cannot be resolved, report that specific unfinished replacement; do not claim the complete original-picture workflow is finished.

## 5. Verify and deliver the actual replaced deck

- Render every slide of the replaced final PPTX and inspect the original pictures, layout, aspect ratios, intrinsic labels, text editability, overlaps and clipping.
- Check the embedded media for every mapped picture against its original file. Verify byte hashes where bytes are preserved; if the tooling re-encodes an image, check decoded image content and record that transformation accurately.
- Run structural PPTX validation on the actual replaced file, and preserve the original-assets mapping and replacement QA results. The earlier generated-assets checkpoint manifest no longer describes those replaced media files: its validation is evidence for reconstruction only, not evidence that the final originals were restored. Do not edit or reuse its report to imply otherwise.
- Require both the reconstruction checkpoint and replacement-stage checks to succeed. Deliver the replaced final PPTX unless the user explicitly requests the intermediate deck as well.

Example request:

> Use my product.png and experiment.png as reference inputs when generating a complete slide. Rebuild that design as an editable PPT, then replace the corresponding generated pictures with those original files. Keep the layout and native text, and inspect the final PPTX.
