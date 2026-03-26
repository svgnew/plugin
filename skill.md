---
name: svg
description: "Convert images to SVG vectors using svg.new. Use when asked to vectorize, trace, or convert an image to SVG. Also supports recoloring SVGs, simplifying colors, removing backgrounds, and batch processing. Triggers on: vectorize, convert to svg, trace image, image to svg, svg.new, recolor svg, simplify svg."
---

# SVG.new — Image to SVG Vectorization

Convert raster images (PNG, JPG, WebP) to clean, scalable SVG vectors using the svg.new API.

## Setup

Requires an API key from svg.new (free to create at https://svg.new/account).

Set the environment variable:
```bash
export SVG_NEW_API_KEY="svk_your_key_here"
```

## Install

```bash
claude skill install --from https://github.com/svgnew/plugin/skill
```

Or manually copy this file to `~/.claude/skills/svg-new/SKILL.md`.

## Available Commands

### Vectorize an Image

Convert a raster image to SVG.

```bash
curl -s -X POST https://svg.new/api/agent/vectorize \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"image\": \"$(base64 -i INPUT_FILE | sed 's/^/data:image\/png;base64,/')\"}" \
  | jq -r '.svg' > output.svg
```

**Steps to vectorize a local file:**

1. Read the image file and base64-encode it
2. Prepend the data URL prefix (`data:image/png;base64,` or appropriate mime type)
3. POST to `/api/agent/vectorize` with the `image` field
4. The response JSON contains `{ id, svg, metadata: { credits_remaining } }`
5. Write the `svg` field to a `.svg` file

**Important:** The `image` field must be a full data URL (e.g., `data:image/png;base64,iVBOR...`).

### Recolor an SVG

Change colors in an existing SVG.

```bash
curl -s -X POST https://svg.new/api/agent/edit/recolor \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"svg": "<svg>...</svg>", "color_map": {"#ff0000": "#0000ff"}}' \
  | jq -r '.svg' > recolored.svg
```

- `color_map`: object mapping old hex colors to new hex colors
- Colors are matched case-insensitively and normalized
- Supports hex (#RGB, #RRGGBB), named colors (red, blue), and rgb()

### Simplify SVG Colors

Reduce the number of unique colors in an SVG.

```bash
curl -s -X POST https://svg.new/api/agent/edit/simplify \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"svg": "<svg>...</svg>", "max_colors": 4}' \
  | jq -r '.svg' > simplified.svg
```

- `max_colors`: target number of colors (1-256)
- Uses median-cut quantization to merge similar colors
- Response includes `colors_before` and `colors_after` counts

### Remove Background

Remove the background from an SVG using the original raster for reference.

```bash
curl -s -X POST https://svg.new/api/agent/edit/remove-background \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"svg": "<svg>...</svg>", "image": "data:image/png;base64,..."}' \
  | jq -r '.svg' > no-bg.svg
```

- Requires both the SVG and the original raster image
- Uses AI background removal (costs 1 credit)

### Batch Vectorize

Submit multiple images at once.

```bash
# Submit batch
BATCH=$(curl -s -X POST https://svg.new/api/agent/batch \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"images": [{"image": "data:image/png;base64,..."}, {"image": "data:image/png;base64,..."}]}')
BATCH_ID=$(echo $BATCH | jq -r '.batch_id')

# Poll status
curl -s https://svg.new/api/agent/batch/$BATCH_ID \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" | jq
```

- Max 50 images per batch
- Returns 202 immediately with `batch_id`
- Poll `/api/agent/batch/{id}` for status and results
- Each image in the batch costs 1 credit

## Practical Workflow

When a user asks to vectorize an image:

1. **Check for API key**: Verify `SVG_NEW_API_KEY` is set. If not, tell the user to generate one at https://svg.new/account
2. **Read the image**: Use the Read tool to read the image file (it supports images)
3. **Encode**: Base64-encode the file and construct the data URL
4. **Call the API**: Use `curl` or `fetch` via Bash to call the vectorize endpoint
5. **Save the result**: Extract the `svg` field from the JSON response and write it to a file
6. **Report**: Tell the user the output file path and credits remaining

For batch operations with many files:
1. Glob for image files
2. Base64-encode each
3. Submit as a batch
4. Poll until complete
5. Download individual SVG results

## API Response Formats

### Vectorize
```json
{
  "id": "uuid",
  "svg": "<svg>...</svg>",
  "metadata": {
    "created_at": "2025-01-01T00:00:00Z",
    "credits_remaining": 997
  }
}
```

### Recolor / Simplify
```json
{
  "svg": "<svg>...</svg>",
  "colors_before": 12,
  "colors_after": 4
}
```

### Batch Submit
```json
{
  "batch_id": "uuid",
  "total_items": 10,
  "status": "processing"
}
```

### Batch Status
```json
{
  "id": "uuid",
  "status": "completed",
  "total_items": 10,
  "completed_items": 10,
  "failed_items": 0,
  "items": [
    { "index": 0, "status": "completed", "prompt_id": "uuid", "svg_url": "https://..." }
  ]
}
```

## Error Handling

| Status | Meaning | Action |
|--------|---------|--------|
| 401 | Invalid or missing API key | Check `SVG_NEW_API_KEY` |
| 402 | Out of credits | Upgrade at https://svg.new/pricing |
| 429 | Rate limited (10/min) | Wait and retry |
| 400 | Invalid input | Check image format |
| 500 | Server error | Retry once |

## Credit Costs

| Operation | Cost |
|-----------|------|
| Vectorize (single) | 1 credit |
| Vectorize (batch, per image) | 1 credit |
| Recolor | Free |
| Simplify | Free |
| Remove background | 1 credit |
