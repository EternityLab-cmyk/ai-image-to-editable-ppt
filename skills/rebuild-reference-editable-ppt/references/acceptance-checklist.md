# Acceptance checklist

Apply this checklist to every page and to the final deck. Treat every failed item as a current-task fix unless the user explicitly changes the requirement for that object.

## Source fidelity

- The slide canvas, composition, group relationships, palette, and visual hierarchy match the reference closely.
- Every source visual is accounted for in the inventory.
- Medical and scientific visuals preserve the correct anatomy, object identity, viewpoint, proportions, and relevant internal detail.
- No object was replaced by a merely synonymous symbol or generic stock icon.

## Asset separation

- Every independent semantic visual is a separate image object unless it was deliberately classified as an inseparable cluster.
- At the reconstruction checkpoint, raster assets came from the image-edit/generation and separation workflow. In the explicitly requested original-picture mode, mapped final picture objects instead contain the preserved user-provided originals, verified under `own-images-workflow.md`; source-slide crops remain disallowed substitutes.
- Every asset is complete and has clean alpha edges.
- No asset contains unwanted chroma-key color, halos, missing tissue color, fused neighboring objects, pseudo-text, or a watermark.
- Every image has recorded provenance and an explicit slide position.

## Editability

- Slide-level titles, labels, captions, legends, and bullet points are visible native text boxes. Intrinsic text inside a restored original picture remains part of that picture and is not presented as editable text.
- Text is not baked into generated visual assets except unavoidable brand/background identity approved by the workflow.
- Panels, rings, sectors, dividers, and ordinary connectors are native structural objects where practical.
- The user can select, move, resize, replace, or delete each separated visual independently.

## Typography and layout

- Text strings match the reference exactly.
- Same-level text uses consistent fonts, sizes, weights, and colors.
- One-line labels do not wrap unexpectedly.
- Adjacent labels have visible spacing and do not touch.
- No text, image, or shape is clipped or outside the slide.
- No unintended overlap remains.
- Z-order matches the reference and no object is duplicated across layers.

## Visual QA

- A full-size preview exists for every page.
- A source-versus-preview contact sheet exists for every page.
- The page author inspected the preview rather than relying only on automated validation.
- The final assembled PPTX was rendered again and every slide was inspected individually.
- Any issue revealed only in the final render was corrected and revalidated.

## Structural QA

- Every page has its manifest, image-generation jobs, page PPTX, preview, contact sheet, validation report, and page result.
- Every page validation contains top-level `passed: true`.
- Final validation reports the expected page count, no missing parts, no failed page validations, and no contract violations.
- The source and earlier deliverables remain preserved; the final output is a new PPTX.

## Own-original-picture mode

- Original pictures were supplied as actual reference inputs during initial full-slide image generation.
- Every requested original has an explicit mapping to its final slide and picture object.
- Mapped generated picture objects were replaced with the preserved original files before final delivery.
- Embedded original-picture content was checked against the inputs, including any re-encoding.
- The replaced final PPTX was rendered and checked independently of the generated-assets checkpoint; checkpoint validation is not reused as replacement-stage proof.
