---
name: vectorize
description: "Convert a raster image (PNG, JPG, WebP) to a clean, scalable SVG vector using AI tracing. Triggers on: vectorize, convert to svg, trace image, image to svg, make svg, png to svg, jpg to svg."
---

# Vectorize Image to SVG

Convert a raster image to a clean SVG vector using svg.new's AI tracing engine.

## Prerequisites

Use the svg.new MCP tools (`mcp__svgnew__*`) if they are available in this session; they need no API key. Otherwise the `SVG_NEW_API_KEY` environment variable must be set. If neither is available, tell the user:
> Connect svg.new with `claude mcp add --transport http --scope user svgnew https://svg.new/api/mcp` and `/mcp` → Authenticate, or generate an API key at https://svg.new/account and set `export SVG_NEW_API_KEY="svk_..."`.

Do not fall back to a local tracer (vtracer, potrace) unless the user asks for one.

Files over ~100 KB are too big to pass inline to the MCP `vectorize` tool. Call `create_upload`, run the curl PUT command it returns with the file's path (up to 20 MB, straight to svg.new's storage), then call `vectorize` with the `upload_id`; the result comes back as a one-hour download link (`curl -sL -o output.svg "<link>"`). If the image is at a public HTTPS URL, `image_url` also works (up to 20 MB). Otherwise use the curl route below, which builds the base64 in the shell.
## Steps

1. Identify the image file from the user's request or `$ARGUMENTS`
2. Read the file and determine its MIME type from the extension:
   - `.png` → `image/png`
   - `.jpg` / `.jpeg` → `image/jpeg`
   - `.webp` → `image/webp`
3. Base64-encode the file contents
4. Construct the data URL: `data:<mime>;base64,<encoded>`
5. Call the API:

```bash
curl -s -X POST "${SVG_NEW_API_URL:-https://svg.new}/api/agent/vectorize" \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"image\": \"$DATA_URL\"}"
```

6. Extract the `svg` field from the JSON response
7. Write it to a `.svg` file (same name as input, different extension)
8. Report the output path and credits remaining from `metadata.credits_remaining`

## Notes

- Maximum image size: 20MB
- Costs 1 credit per vectorization
- The API returns the full SVG as a string in the `svg` field
- The `id` field can be used to view the result at `https://svg.new/edit/{id}`
