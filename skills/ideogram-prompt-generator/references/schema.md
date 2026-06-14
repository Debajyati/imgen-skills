# Ideogram 4.0 JSON Schema Reference

## Top-Level Object

```json
{
  "aspect_ratio": "W:H",                     // REQUIRED — string, e.g. "16:9", "1:1", "9:16"
  "high_level_description": "...",           // REQUIRED — string, ≤50 words
  "style_description": { ... },             // OPTIONAL — only when style cues present
  "compositional_deconstruction": { ... }   // REQUIRED
}
```

Required top-level fields: `aspect_ratio`, `high_level_description`, `compositional_deconstruction`.

---

## `aspect_ratio`
- Type: `string`
- Format: `"W:H"` with positive integers — `"1:1"`, `"16:9"`, `"9:16"`, `"4:5"`, `"3:1"`, `"2:3"`, `"1936:1088"`, etc.
- Must be a concrete ratio. NEVER the literal string `"auto"`.

---

## `high_level_description`
- Type: `string`
- Max: 50 words (hard cap)
- One long sentence preferred; two sentences maximum.
- Reads as a natural-language prompt — not an analysis.
- Starts with the subject. Never opens with "This image shows", "depicts", "captures".
- Names recognized pop-culture entities by full name.
- For transparent background: must include the exact phrase `on a transparent background`.

---

## `style_description` (optional)
```json
{
  "aesthetics": "string",
  "lighting": "string",
  "photo": "string",
  "medium": "string",
  "color_palette": ["#RRGGBB", ...]
}
```
All five sub-fields required **when** `style_description` is present.

- **Include** when: user names an art style, texture, or medium; or prompt strongly implies one.
- **Omit** when: prompt is purely scene-based with no style cues.
- `medium` carries the named style (`"Digital painting"`, `"Mixed-media digital collage"`, `"Studio Ghibli animation"`).
- `photo`: `"N/A"` when not a photographic medium.
- `color_palette`: array of hex color strings (`"#FDD7E4"`).

---

## `compositional_deconstruction`
```json
{
  "background": "string",   // REQUIRED — scene shell prose
  "elements": [ ... ]       // REQUIRED — array of element objects
}
```

### `background`
- Type: `string`
- Prose description of the scene shell only: walls, floor/ground, ceiling, sky, atmospheric context, distant blurred scenery, scene-wide ambient lighting.
- **NEVER** contains: furniture, people, vehicles, animals, equipment, free-standing objects.
- Ground/floor/sky always lives here — never as elements.
- For transparent background: must be exactly `"transparent background"` — no other content.

### `elements`
Array of element objects. Two types:

#### obj element
```json
{
  "type": "obj",                              // REQUIRED
  "bbox": [y1, x1, y2, x2],                  // optional — integers 0–1000
  "desc": "string",                           // REQUIRED — 30–60 words, 60-word hard cap
  "color_palette": ["#RRGGBB", ...]           // REQUIRED
}
```

#### text element
```json
{
  "type": "text",                             // REQUIRED
  "bbox": [y1, x1, y2, x2],                  // optional
  "text": "VERBATIM TEXT\nLINE TWO",          // REQUIRED for text type — verbatim characters
  "desc": "string",                           // REQUIRED — typography, size, color, orientation
  "color_palette": ["#RRGGBB", ...]           // REQUIRED
}
```

#### bbox coordinate system
- Format: `[y1, x1, y2, x2]`
- `y` axis: 0 = top edge → 1000 = bottom edge
- `x` axis: 0 = left edge → 1000 = right edge
- Constraints: `y1 < y2`, `x1 < x2`, all values integers 0–1000
- Shape warning: bbox spans are normalized to image dimensions. A `[0,0,500,500]` box is square only on a 1:1 frame; on 16:9 it becomes a wide rectangle. Scale spans to match actual on-screen shape.

#### Required element fields
- `type`: always required
- `bbox`: recommended for precisely-placeable subjects; omit for dense/unenumerable visuals
- `desc`: always required
- `color_palette`: always required
- `text`: required only on `type: "text"` elements
