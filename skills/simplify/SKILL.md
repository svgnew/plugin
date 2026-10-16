---
name: simplify
description: "Reduce the number of colors in an SVG using quantization. Triggers on: simplify svg, reduce svg colors, fewer colors, quantize svg, minimize svg colors."
---

# Simplify SVG Colors

Reduce the number of unique colors in an SVG using median-cut quantization.

## Prerequisites

Use the svg.new MCP tools if they are available in this session (`mcp__svgnew__*`, or `mcp__plugin_svg-vectorizer_svg-new__*` when this plugin provides them); they need no API key. Otherwise the `SVG_NEW_API_KEY` environment variable must be set. If neither is available, tell the user:
> Connect svg.new with `claude mcp add --transport http --scope user svgnew https://svg.new/api/mcp` and `/mcp` → Authenticate, or generate an API key at https://svg.new/account and set `export SVG_NEW_API_KEY="svk_..."`.

Do not fall back to a local tracer (vtracer, potrace) unless the user asks for one.

Large inputs go by reference: `svg_url` for the SVG (e.g. the download link a previous result gave), `image_url` or `upload_id` (from `create_upload`) for rasters, each up to 20 MB; large results come back as one-hour download links.
## Steps

1. Read the SVG file specified by the user or `$ARGUMENTS`
2. Determine `max_colors` — ask the user if not specified (common values: 2, 4, 8, 16)
3. Call the API:

```bash
curl -s -X POST "${SVG_NEW_API_URL:-https://svg.new}/api/agent/edit/simplify" \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"svg\": \"$SVG_CONTENT\", \"max_colors\": $MAX_COLORS}"
```

4. The response includes `colors_before`, `colors_after`, and the modified `svg`
5. Report the color reduction to the user
6. Write the simplified SVG to the file

## Notes

- This operation is free (no credits consumed)
- `max_colors` range: 1–256
- Similar colors are merged together; distant colors stay distinct
- Shapes with `fill="none"` are not affected
