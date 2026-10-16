---
name: recolor
description: "Change colors in an SVG file. Map old colors to new colors. Triggers on: recolor svg, change svg color, replace color in svg, swap colors."
---

# Recolor SVG

Replace colors in an SVG file by mapping old colors to new ones.

## Prerequisites

Use the svg.new MCP tools (`mcp__svgnew__*`) if they are available in this session; they need no API key. Otherwise the `SVG_NEW_API_KEY` environment variable must be set. If neither is available, tell the user:
> Connect svg.new with `claude mcp add --transport http --scope user svgnew https://svg.new/api/mcp` and `/mcp` → Authenticate, or generate an API key at https://svg.new/account and set `export SVG_NEW_API_KEY="svk_..."`.

Do not fall back to a local tracer (vtracer, potrace) unless the user asks for one.
## Steps

1. Read the SVG file specified by the user or `$ARGUMENTS`
2. Determine which colors to replace — ask the user if not specified
3. Build a color map object: `{ "#old_hex": "#new_hex", ... }`
4. Call the API:

```bash
curl -s -X POST "${SVG_NEW_API_URL:-https://svg.new}/api/agent/edit/recolor" \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"svg\": \"$SVG_CONTENT\", \"color_map\": $COLOR_MAP}"
```

5. Extract the `svg` field from the response
6. Write it back to the file (or a new file if the user prefers)

## Notes

- This operation is free (no credits consumed)
- Colors are matched case-insensitively
- Supports hex (#RGB, #RRGGBB), named colors (red, blue), and rgb() values
- Only fill colors are modified; strokes are preserved
