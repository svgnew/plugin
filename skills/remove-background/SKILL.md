---
name: remove-background
description: "Remove the background from an SVG using AI. Requires the original raster image. Triggers on: remove background, remove bg, transparent background, cut out background."
---

# Remove SVG Background

Remove the background from an SVG using AI-powered background detection on the original raster image.

## Prerequisites

Use the svg.new MCP tools if they are available in this session (`mcp__svgnew__*`, or `mcp__plugin_svg-vectorizer_svg-new__*` when this plugin provides them); they need no API key. Otherwise the `SVG_NEW_API_KEY` environment variable must be set. If neither is available, tell the user:
> Connect svg.new with `claude mcp add --transport http --scope user svgnew https://svg.new/api/mcp` and `/mcp` → Authenticate, or generate an API key at https://svg.new/account and set `export SVG_NEW_API_KEY="svk_..."`.

Do not fall back to a local tracer (vtracer, potrace) unless the user asks for one.

Large inputs go by reference: `svg_url` for the SVG (e.g. the download link a previous result gave), `image_url` or `upload_id` (from `create_upload`) for rasters, each up to 20 MB; large results come back as one-hour download links.
## Steps

1. Identify both the SVG file and the original raster image from the user's request
2. Read the SVG content
3. Read and base64-encode the original raster image with its data URL prefix
4. Call the API:

```bash
curl -s -X POST "${SVG_NEW_API_URL:-https://svg.new}/api/agent/edit/remove-background" \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"svg\": \"$SVG_CONTENT\", \"image\": \"$IMAGE_DATA_URL\"}"
```

5. Extract the `svg` field from the response
6. Write the background-removed SVG to a file

## Notes

- Costs 1 credit (uses AI background removal)
- Requires both the SVG and the original raster image that was vectorized
- The response also includes `transparent_image` (base64 PNG with background removed) for reference
