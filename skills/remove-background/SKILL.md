---
name: remove-background
description: "Remove the background from an SVG using AI. Requires the original raster image. Triggers on: remove background, remove bg, transparent background, cut out background."
---

# Remove SVG Background

Remove the background from an SVG using AI-powered background detection on the original raster image.

## Prerequisites

The `SVG_NEW_API_KEY` environment variable must be set.

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
