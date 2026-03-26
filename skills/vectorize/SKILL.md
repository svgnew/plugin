---
name: vectorize
description: "Convert a raster image (PNG, JPG, WebP) to a clean, scalable SVG vector using AI tracing. Triggers on: vectorize, convert to svg, trace image, image to svg, make svg, png to svg, jpg to svg."
---

# Vectorize Image to SVG

Convert a raster image to a clean SVG vector using svg.new's AI tracing engine.

## Prerequisites

The `SVG_NEW_API_KEY` environment variable must be set. If not, tell the user:
> Generate an API key at https://svg.new/account, then set `export SVG_NEW_API_KEY="svk_..."`.

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
