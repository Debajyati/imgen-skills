# Elements Deep-Dive Reference

## Single Subject = Single Element

ONE coherent subject (animal, person, vehicle, building, plant, instrument, machine) = exactly ONE `obj` element. Anatomical and structural parts are descriptive attributes inside `desc` — NOT separate elements.

**Forbidden splits:**
- A bee split into 8 elements (thorax/abdomen/wings/eyes/legs)
- A car split into (body/wheels/windshield/door/hood)
- A person split into (head/torso/limbs)
- A building split into (foundation/walls/windows/roof/door)
- A flower split into (petals/stem/leaves)

**Multiple distinct subjects → multiple elements** (person AND dog; two bees; three runners → one element each).

**Transparent enclosure + featured contents = ONE element.** Display cases, snow globes, terrariums, aquariums, specimen jars, bell jars with a featured subject: name enclosure + contents in one unified desc.

**Configured parts + revealed interior = ONE element.** Car with open door, machine with raised hood, building with drawn curtains: open state and revealed interior are attributes of the single subject's desc.

---

## Shell-Affixed Prominent Objects (Dual-Mention Rule)

Objects simultaneously part of the shell AND focal elements (chalkboard filling a classroom wall, fireplace built into a living-room wall, large mounted TV, stage proscenium, built-in altar/bookshelf, large fixed reception desk, fixed sign/banner):

1. **Mention in `background`** as part of the shell.
2. **Emit as first obj element** with `"the primary background element"` at the start of its `desc`.
3. **Place FIRST in the elements list** so painter's algorithm draws it behind foreground items.

Applies ONLY to objects that genuinely define the room's architectural identity. Free-standing furniture → elements only, no background mention.

---

## Element `desc` Rules

### What to write (30–60 words, 60-word hard cap)
- Identity first, then major attributes, then one distinguishing detail.
- Each desc is a standalone catalog entry — open with the subject's identity, NOT a referring phrase like "the X".

**Major attributes to always name:**
- People: skin tone, hair (color + style), each visible garment with color, expression/gaze, pose, distinguishing feature (mole, glasses, jewelry, held prop).
- Objects: shape, material, color, distinctive parts (handle, label, logo, marking).
- Scenes/structures: type, primary material, color, distinctive structural elements.

**Spatial anchoring — use named references:**
- CORRECT: `applied to the forehead near the hairline above the left eyebrow`
- INCORRECT: `pressed against the skin`
- CORRECT: `resting on the lower-right corner of the table directly in front of the laptop`
- INCORRECT: `sitting on the surface`

### What NOT to include in `desc`

**No shadows.** Cast shadows, drop shadows, contact shadows, ambient occlusion — describe in `background` only when scene-wide; otherwise omit. The renderer infers them.

**No camera/render language.** Depth of field, focus, sharpness, bokeh, exposure, motion blur, lens blur, lens flare, chromatic aberration, film grain — these belong in `high_level_description` or `background` ONLY when the user explicitly named them. NEVER inside an obj desc.
- EXCEPTION: viewpoint/angle (`from a low-angle perspective`, `bird's-eye view`, `eye-level`) IS allowed when the prompt calls for it. Place once, in the focal subject's desc or background.

**No impressionistic adjectives.** Forbidden: `luminous`, `radiant`, `vibrant`, `lush`, `dynamic`, `glowing` (metaphorically), `gorgeous`, `stunning`, `breathtaking`, `mesmerizing`. Use observable properties instead: `cheekbone catches a small highlight`, not `luminous complexion`.

**No scene-context repetition per element.** Lighting direction, ambient surface, mounting context, weather → describe ONCE in `background`. Each element's desc focuses on what's unique to that element.

**Skip low-value micro-prose:**
- Surface-finish details (`finely granular matte texture with subtle sheen along the elytral ridges`) → pick one short descriptor (matte/glossy/metallic/textured) or omit.
- Per-limb pose mechanics → pick ONE summary action phrase plus major attributes.
- Per-element camera/shadow/lighting micro-detail → belongs in `background`.
- Fabric weave, skin texture nuances, micro-anatomy.

---

## Bbox Strategy

**Include bboxes when:** precise positioning matters — portrait subjects, products on a surface, logos, signs on a wall, individually-placeable objects.

**Omit bboxes when:** dense or hard-to-enumerate visuals — crowds, fields of wildflowers, scattered particles, starry skies.

**Coordinate system:**
- `[y1, x1, y2, x2]` — all integers 0–1000.
- `x`: 0 = left edge, 1000 = right edge.
- `y`: 0 = top edge, 1000 = bottom edge.
- Enforce: `y1 < y2`, `x1 < x2`.

**Shape scaling:** Values are normalized to the image's W:H. A `[0,0,500,500]` box is square only on 1:1; on 16:9 it becomes a wide rectangle, on 9:16 a tall rectangle. For round or square on-screen regions, scale spans so `(x2-x1)/(y2-y1) ≈ W/H`. For single-subject wide-frame prompts, prefer narrower x-spans. For multi-subject prompts, give each a tight bbox.

---

## Specificity — Commit to One Value

This JSON feeds a diffusion model. Leave nothing for the model to invent.

**Banned hedge phrasings** (elements and background):
`things like`, `such as`, `e.g.`, `for example`, `or similar`, `various`, `could include`, `might be`, `some kind of`, `style of`, `implied`, `suggested`, `hinted`, `barely visible`, `possibly`, `perhaps`, `maybe`, `reads as`, `almost`.

**Banned alternative listings:** `pale off-white or pale green`, `oak or walnut`, `late afternoon or early evening`, `bold or semibold`. Pick ONE and commit.

**Typography:** name ONE typeface category (serif / sans-serif / display / script / monospace), ONE weight (bold/regular/light/medium), ONE style (italic / upright). Never two joined by `or`.

---

## Named Prompt Elements Must Appear

Every explicitly named visual unit in the user prompt MUST appear as its own element:
- Input `text:` sections → each entry becomes its own text element, verbatim. 3 entries → ≥3 text elements.
- Quoted strings (single or double quotes) → each is its own text element.
- Speech bubbles/dialogue/captions → text element for the string + obj element for the bubble/balloon.
- Named decorative elements (`small medical cross icon top-left`, `flame flourish at the tail`) → each its own obj.
- Named badges/chips/CTAs/strips → each its own obj (+ text if carries a quoted string).
- Named accents/graphic devices (`hairline rule`, `dot grid`, `accent line`) → each its own obj (unless a scene-wide overlay → background).

**No placeholder enumeration.** Sequentially-numbered/lettered sets (stones 1–50, parking spaces A1–A20, calendar grid of 31 dates, 22-name roster) → EACH item is its own element. No `etc.`, no grouping, no range shorthand.

---

## Text Handling

For each text element:
- `text` — literal characters appearing in the image, verbatim. Preserve diacritics, capitalization, punctuation. Never transliterate or strip non-ASCII.
- `bbox` — optional, same system as obj elements.
- `desc` — covers size, location, font style, color, orientation, visual effects. Do NOT repeat the literal characters from `text` inside `desc` — refer by role/position.

**Sources of text to include:**
1. User-quoted text (single or double quotes) — verbatim.
2. Format-required text — headlines, taglines, author names, dates, venues, CTA copy, brand names, publisher marks.
3. In-scene contextual text — signage, labels, license plates, badges, jersey numbers, t-shirt prints, awnings, neon signs, name tags.
4. Numeric content — race numbers, jersey numbers, dates, prices, scores, time displays, address numbers. Numbers ARE text.
5. Prominent product brand text — if an element names a prominent product (bottle, cosmetic, package, beverage) and the user didn't supply a real brand, invent a complete brand identity and list every label as text elements.

**Multi-line text:** use `\n` for line breaks within one text element. Use SEPARATE elements for visually distinct text blocks.

**Stylized hero typography:** stack with `\n` at natural word breaks — long single-line stylized titles produce typos and dropped letters. e.g., `"ENTRE\nVERSOS E\nCONTOS"` not `"ENTRE VERSOS E CONTOS"`.

**Language scoping:** prose fields (`desc`, `background`, `high_level_description`) are always in English. Only the `text` field follows the user's brief language.

---

## Scene Population

When the brief is sparse, add believable secondary subjects, micro-props, environmental texture.

**Populate by depth layer:** foreground crop, midground, background each need their own content.

**Commit to a specific identity:** "Southeast Asian village" is a hedge. "Vietnamese pho stall by the rice paddies outside Hoi An" is a real place. Specific commitment shapes architecture, signage script, food, dress, props.

**Built environments → text everywhere.** Real shops/stalls/restaurants/vehicles carry text on practically every surface. Generate generously: shop name, sub-signs (`OPEN`/`TODAY'S SPECIAL`), menu board with handwritten items, price labels, jar/bottle labels, name tags, posters. `text: []` is almost always wrong for built environments.

**Fantasy/sci-fi bonus:** stack sky drama (galaxies, ringed planets, nebulae), opposing focal points (volcano right/waterfall left), mid-distance scale anchors (crystal columns, megastructures), light/energy effects, exotic architecture/geology, deeply saturated palettes.

**Override — skip population when the brief says:** `minimal`, `sparse`, `empty`, `lonely`, `isolated`, `quiet`, `still`, `negative space`, `alone`, `single subject`, `in the middle of nowhere`.

---

## Pop Culture, Brands, Named References

When the user names or clearly implies a brand, trademark, product (sneaker/car/device), public figure, athlete, musician, actor, fictional character, film, show, game, franchise, or team — carry an explicit named reference in the relevant element `desc`. Never replace with a generic stand-in.

- Do NOT replace `Nike Dunk Low Panda` with `black and white retro sneakers`.
- Do NOT replace `Spider-Man` with `a red-and-blue masked superhero`.
- Do NOT replace `The Beatles` with `four men in matching suits`.

Exception: user asks for an anonymous lookalike — then a generic description is correct.

---

## Photoreal Defaults

For photographic prompts (no specified medium beyond `photo`/`photorealistic`/`selfie`/real-world scene):
- Default to iPhone aesthetic — phone snapshot, ambient natural light, neutral white balance, accurate skin tones, ordinary framing.
- AVOID DSLR-magazine markers (creamy bokeh, telephoto compression, dramatic rim lighting, cinematic grade) — those signal AI generation.
- Default lighting: `natural daylight`, `overcast daylight`, `diffused daylight`, `cool-neutral white balance`.
- The word **"warm"** as a grading adjective is BANNED (triggers the amber/golden AI look). When a scene physically has a warm light source (candle, sodium lamp, sunset), describe the SOURCE (`candle flame`) and the light pool colour (`amber pool from the candle`) — but the global grade stays neutral.
- Default composition: prefer non-centered framing (off-center, rule-of-thirds, asymmetrical, leading lines) unless the prompt explicitly calls for centered/symmetrical.
- No motion blur in candid/realistic/iPhone-aesthetic photos.
- Don't stack saturation descriptors (`vibrant + bright + intense + saturated + electric + neon`) for a neutral subject. Mention saturation ONCE only when the prompt explicitly asks.

**"Professional picture/photo/portrait" of a person means PROFESSIONAL CONTEXT:** corporate headshot, LinkedIn profile, neutral business attire, soft even daylight, neutral backdrop. NOT dramatic studio rim-lighting or dark moody backdrop.
